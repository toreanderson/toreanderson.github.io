---
published: true
title: 'IPv6 network boot with UEFI and iPXE'
layout: post
---

Here at [Redpill Linpro](http://www.redpill-linpro.com) we make extensive use
of network booting to provision software onto our servers. Many of our servers
don't even have local storage - they boot from the network every time they
start up. Others use network boot in order to install an operating system to
local storage. The days when we were running around in our data centres with
USB or optical install media are long gone, and we're definitively not looking
back.

Our network boot infrastructure is currently built around
[iPXE](http://ipxe.org/), a very flexible network boot firmware with powerful
scripting functionality. Our virtual servers (using
[QEMU/KVM](http://wiki.qemu.org/KVM)) simply execute iPXE directly. Our
physical servers, on the other hand, use their standard built-in
[PXE](https://en.wikipedia.org/wiki/Preboot_Execution_Environment) ROMs in
order to [chainload](http://ipxe.org/howto/chainloading) an iPXE
[UNDI](https://en.wikipedia.org/wiki/Universal_Network_Device_Interface) ROM
over the network.

IPv6 PXE was first included in [UEFI version 2.3 (Errata
D)](http://www.uefi.org/sites/default/files/resources/UEFI_Spec_2_3_D.pdf),
published five years ago. However, not all servers support IPv6 PXE yet,
including the ageing ones in my lab. I'll therefore focus on virtual servers
for now, and will get back to IPv6 PXE on physical servers later.

## Enabling IPv6 support in iPXE

At the time of writing, [iPXE does not enable IPv6 support by
default](http://lists.ipxe.org/pipermail/ipxe-devel/2015-November/004482.html).
This default [spills over into Linux distributions like
Fedora](https://bugzilla.redhat.com/show_bug.cgi?id=1280318). I'm trying to get
this changed, but for now it is necessary to manually rebuild iPXE with IPv6
support enabled.

This is done by [downloading the iPXE sources](http://ipxe.org/download) and
then enabling [`NET_PROTO_IPV6`](http://ipxe.org/buildcfg/net_proto_ipv6) in
[`src/config/general.h`](https://git.ipxe.org/ipxe.git/blob/HEAD:/src/config/general.h#l38).
Replace `#undef` with `#define` so that the full line reads `#define
NET_PROTO_IPV6`.

At this point, we're ready to [build
iPXE](http://ipxe.org/download#replacing_an_existing_pxe_rom). For the
`virtio-net` driver used by our QEMU/KVM hypervisors, the correct command is
`make -C /path/to/ipxe/src bin/1af41000.rom`. To build a UEFI image suitable
for [chainloading](http://ipxe.org/download#uefi), run `make -C
/path/to/ipxe/src bin-x86_64-efi/ipxe.efi` instead.

On [RHEL7](http://www.redhat.com)-based hypervisors, upgrading iPXE is just a
matter of replacing the default `1af41000.rom` file in `/usr/share/ipxe` with
the one that was just built.

## Network configuration

The network must be set up with both [ICMPv6 Router
Advertisements](https://en.wikipedia.org/wiki/Neighbor_Discovery_Protocol)
(RAs) and [DHCPv6](https://en.wikipedia.org/wiki/DHCPv6). RAs are necessary in
order to provision the booting nodes with a default IPv6 router, while DHCPv6
is the only way to advertise IPv6 [network boot
options](http://tools.ietf.org/html/rfc5970).

When it comes to the assignment of IPv6 addresses, you can use either
[SLAAC](https://en.wikipedia.org/wiki/IPv6#Stateless_address_autoconfiguration_.28SLAAC.29)
or [DHCPv6 IA\_NA](http://tools.ietf.org/html/rfc3315#section-22.4). iPXE
supports both approaches. Avoid using both at the same time, though, as doing
so may trigger [a
bug](http://lists.ipxe.org/pipermail/ipxe-devel/2015-November/004485.html)
which could lead to the boot process getting stuck halfway through.

You'll probably want to provision the nodes with an IPv6 DNS server. This can
be done both using [DHCPv6](http://tools.ietf.org/html/rfc3646) and [ICMPv6
RAs](http://tools.ietf.org/html/rfc6106). iPXE supports both approaches, so
either will do just fine. That said, I recommend enabling both at the same
time. It might very well be that some UEFI implementation only supports one of
them.

### ICMPv6 Router Advertisement configuration

````
protocol radv {
  # Use Google's public DNS server.
  rdnss {
    ns 2001:4860:4860::8888;
  };
  interface "vlan123" {
    managed no;       # Addresses (IA_NA) aren't found in DHCPv6
    other config yes; # "Other Configuration" is found in DHCPv6 
    prefix 2001:db8::/64 {
      onlink yes;     # The prefix is on-link
      autonomous yes; # The prefix may be used for SLAAC
    };
  };
}
````

The configuration above is for [BIRD](http://bird.network.cz/). It is all
pretty standard stuff, but pay attention to the fact that the `other config`
flag is enabled. This is required in order to make iPXE ask the DHCPv6 server
for the [*Boot File URL
Option*](http://tools.ietf.org/html/rfc5970#section-3.1).

### DHCPv6 server configuration

````
option dhcp6.user-class code 15 = string;
option dhcp6.bootfile-url code 59 = string;
option dhcp6.client-arch-type code 61 = array of unsigned integer 16;

option dhcp6.name-servers 2001:4860:4860::8888;

if exists dhcp6.client-arch-type and
   option dhcp6.client-arch-type = 00:07 {
    option dhcp6.bootfile-url "tftp://[2001:db8::69]/ipxe.efi";
} else if exists dhcp6.user-class and
          substring(option dhcp6.user-class, 2, 4) = "iPXE" {
    option dhcp6.bootfile-url "http://boot.ipxe.org/demo/boot.php";
}

subnet6 2001:db8::/64 {}
````

The config above is for the [ISC DHCPv6](https://www.isc.org/downloads/dhcp/)
server. The first paragraph declares the various necessary DHCPv6 options and
their syntax. For some reason, ISC `dhcpd` does not appear to have any
intrinsic knowledge of these, even though they're standardised.

The second paragraph ensures the server can advertise an IPv6 DNS server to
clients. In this example I'm using [Google's Public
DNS](https://developers.google.com/speed/public-dns/); you'll probably want to
replace it with your own IPv6 DNS server.

The `if/else` statement ensures two things:

1. If the client is an [UEFI firmware performing IPv6
   PXE](http://www.iana.org/assignments/dhcpv6-parameters/dhcpv6-parameters.xhtml#processor-architecture),
   then we just chainload an UEFI-compatible iPXE image. (As I mentioned
   earlier, I haven't been able to fully test this config due to lack of lab
   equipment supporting IPv6 PXE.)
2. If the client is iPXE, then we give it an iPXE script to execute. In this
   example, I'm using the iPXE project's [demo
   service](http://boot.ipxe.org/demo/boot.php), which boots a very basic Linux
   system.

Finally, I declare the subnet prefix where the IPv6-only VMs live. Without
this, the DHCPv6 server will not answer any requests coming from this network.
Since I'm not using stateful address assignment (DHCPv6 IA\_NA), I do not need
to configure an IPv6 address pool.

## Conclusion

Thanks to iPXE and UEFI, network boot can be made to work just as well over
IPv6 as over IPv4. The only real remaining problem is that many server models
still lack support for IPv6 PXE, but I am assuming this will become less of an
issue over time as they upgrade their UEFI implementations to version 2.3
(Errata D) or newer.

In virtualised environments, nothing is missing. Apart from the somewhat
annoying requirement to rebuild iPXE to enable IPv6 support, it *Just Works*.
This is evident from by the boot log below, which shows a successful boot of a
QEMU/KVM virtual machine residing on an IPv6-only network.

````
[root@kvmhost ~]# virsh create /etc/libvirt/qemu/v6only --console
Domene v6only opprettet fra /etc/libvirt/qemu/v6only
Connected to domain v6only
Escape character is ^]

Google, Inc.
Serial Graphics Adapter 06/09/14
SGABIOS $Id: sgabios.S 8 2010-04-22 00:03:40Z nlaredo $ (mockbuild@) Mon Jun  9 21:33:48 UTC 2014
4 0

SeaBIOS (version seabios-1.7.5-8.el7)
Machine UUID ebe11d4a-11d4-4ae8-b249-390cdf7c79ec

iPXE (http://ipxe.org) 00:03.0 CA00 PCI2.10 PnP PMM+7FF979E0+7FEF79E0 CA00

Booting from Hard Disk...
Boot failed: not a bootable disk

Booting from ROM...
iPXE (PCI 00:03.0) starting execution...ok
iPXE initialising devices...ok

iPXE 1.0.0+ (f92f) -- Open Source Network Boot Firmware -- http://ipxe.org
Features: DNS HTTP iSCSI TFTP AoE ELF MBOOT PXE bzImage Menu PXEXT

net0: 00:16:3e:c2:16:b7 using virtio-net on PCI00:03.0 (open)
  [Link:up, TX:0 TXE:0 RX:0 RXE:0]
Configuring (net0 00:16:3e:c2:16:b7).................. ok
net0: fe80::216:3eff:fec2:16b7/64
net0: 2001:db8::216:3eff:fec2:16b7/64 gw fe80::21e:68ff:fed9:d156
Filename: http://boot.ipxe.org/demo/boot.php
http://boot.ipxe.org/demo/boot.php.......... ok
boot.php : 127 bytes [script]
/vmlinuz-3.16.0-rc4... ok
/initrd.img... ok
Probing EDD (edd=off to disable)... ok

iPXE Boot Demonstration
=======================

Linux (none) 3.16.0-rc4+ #1 SMP Wed Jul 9 15:44:09 BST 2014 x86_64 unknown

Congratulations!  You have successfully booted the iPXE demonstration
image from http://boot.ipxe.org/demo/boot.php

See http://ipxe.org for more ideas on how to use iPXE.

root:/#
````

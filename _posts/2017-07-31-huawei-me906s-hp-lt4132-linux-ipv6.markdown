---
published: true
title: "Huawei ME906s-158 (a.k.a. HP lt4132): Linux and IPv6 support (or lack thereof)"
layout: post
---

_**UPDATE 2018-12-01**: The parts about missing IPv6 support in this post is
outdated, see [this
post](/2018/12/01/huawei-me906s-hp-lt4132-enabling-ipv6.html) for updated
information._

I recently purchased a new laptop, an [HP EliteBook 820
G4](http://www8.hp.com/us/en/products/laptops/product-detail.html?oid=11122281).
When ordering, I was given the choice of two different LTE WWAN modems: the _HP
lt4120_ and the _HP lt4132_. The former is a rebranded _Foxconn T77W595_, while
the latter is a rebranded [_Huawei
ME906s-158_](http://consumer.huawei.com/solutions/m2m-solutions/en/products/tech-specs/me906s-158-en.htm).

Both modems cost about the same and there were reports of people getting them
both working under Linux, so it didn't seem to matter much which of them I
chose. I eventually decided on the _lt4132_, the primary reason being that its
[specifications](http://consumer.huawei.com/solutions/m2m-solutions/en/products/tech-specs/me906s-158-en.htm)
clearly state that it supports IPv6. This made the _lt4132_ seem like the safe
choice, as I was unable to easily confirm that the _lt4120_ supported IPv6.

I was wrong. I _should_ have opted for the _lt4120_. Read on for the details.

## Linux support

The _lt4132_ did not work out of the box with my preferred Linux distribution
[Fedora 26](https://getfedora.org/);
[ModemManager](https://www.freedesktop.org/wiki/Software/ModemManager/) didn't
recognise it as a supported modem.

The modem was visible in the output from `usb-devices`, however:

```
T:  Bus=01 Lev=01 Prnt=01 Port=02 Cnt=02 Dev#=  3 Spd=480 MxCh= 0
D:  Ver= 2.00 Cls=00(>ifc ) Sub=00 Prot=ff MxPS=64 #Cfgs=  3
P:  Vendor=03f0 ProdID=a31d Rev=01.02
S:  Manufacturer=HP
S:  Product=HP lt4132 LTE/HSPA+ 4G Module
S:  SerialNumber=0123456789ABCDEF
C:  #Ifs= 7 Cfg#= 2 Atr=a0 MxPwr=2mA
I:  If#= 0 Alt= 0 #EPs= 1 Cls=02(commc) Sub=06 Prot=00 Driver=cdc_ether
I:  If#= 1 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=06 Prot=00 Driver=cdc_ether
I:  If#= 2 Alt= 0 #EPs= 3 Cls=ff(vend.) Sub=06 Prot=10 Driver=(none)
I:  If#= 3 Alt= 0 #EPs= 2 Cls=ff(vend.) Sub=06 Prot=13 Driver=(none)
I:  If#= 4 Alt= 0 #EPs= 2 Cls=ff(vend.) Sub=06 Prot=12 Driver=(none)
I:  If#= 5 Alt= 0 #EPs= 2 Cls=ff(vend.) Sub=06 Prot=14 Driver=(none)
I:  If#= 6 Alt= 0 #EPs= 2 Cls=ff(vend.) Sub=06 Prot=1b Driver=(none)
````

The problem here is `Cfg#= 2`, indicating that the modem is in configuration
`2`.  The Linux kernel selects configuration `2` by default, but that does not
work with ModemManager. Configuration `3` (
[MBIM](https://docs.microsoft.com/en-us/windows-hardware/drivers/network/mb-interface-model)
mode) is a much better choice.

Changing to configuration `3` is easy enough. Note that it is essential to
first deconfigure the device by selecting configuration `0` and wait a few
milliseconds. Going directly from `2` to `3` does _not_ work. Thus:

```console
$ echo 0 > /sys/bus/usb/devices/1-3/bConfigurationValue
$ sleep 1
$ echo 3 > /sys/bus/usb/devices/1-3/bConfigurationValue
```

The `1-3` part might not be correct for your system. If it's not, `grep lt4132
/sys/bus/usb/devices/*/product` will probably tell you what the correct sysfs
device path is.

This made ModemManager recognise the modem. At this point I could run `nmcli
con add type gsm ifname '*' apn telenor.smart` to make
[NetworkManager](https://wiki.gnome.org/Projects/NetworkManager) successfully
establish a mobile data connection. Well, ostensibly - it still didn't quite
work. All the data traffic was being blackholed. This was solved by enabling
the `ndp_to_end` USB quirk, like so:

```console
$ echo Y > /sys/class/net/wwp0s20f0u3c3/cdc_ncm/ndp_to_end
```

In the future, the _lt4132_ will be better supported out of the box. It will
not be necessary to manually deal with these settings; the upcoming version of
[usb_modeswitch](http://www.draisberghof.de/usb_modeswitch/bb/viewtopic.php?p=18195#p18195)
will automatically select USB configuration `3`, and a patch I wrote to
[automatically enable the `ndp_to_end` USB
quirk](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a68491f895a937778bb25b0795830797239de31f)
will be part of the Linux kernel starting with version 4.13.

In the interim, however, it is easy enough to automate the application of these
tweaks by using [udev](https://en.wikipedia.org/wiki/Udev) rules. Simply create
a file called, e.g., `/etc/udev/rules.d/hp-lt4132.rules` and add the following
three lines to it:

```
ACTION=="add|change", SUBSYSTEM=="usb", ATTR{idVendor}=="03f0", ATTR{idProduct}=="a31d", ATTR{bConfigurationValue}!="3", ATTR{bConfigurationValue}:="0"
ACTION=="add|change", SUBSYSTEM=="usb", ATTR{idVendor}=="03f0", ATTR{idProduct}=="a31d", ATTR{bConfigurationValue}!="3", RUN+="/bin/sh -c 'sleep 1; echo 3 > %S%p/bConfigurationValue'"
ACTION=="add|change", SUBSYSTEM=="net", ATTRS{idVendor}=="03f0", ATTRS{idProduct}=="a31d", ATTR{cdc_ncm/ndp_to_end}=="N", ATTR{cdc_ncm/ndp_to_end}:="Y"
```

That's all it takes. (You will probably want to change the vendor/product IDs
`03f0`/`a31d` if you don't have the same HP-branded flavour of the _Huawei
ME906s-158_ I do, though.)

## Lack of IPv6 support

The [Huawei ME906s-158 product
page](http://consumer.huawei.com/solutions/m2m-solutions/en/products/tech-specs/me906s-158-en.htm)
clearly specifies that the modem supports IPv6. Turns out, this is a lie - or
at best, extremely misleading.

When I attempted to establish an IPv6 mobile data connection, it would just
fail:

```console
$ mmcli -m 0 --simple-connect=apn=telenor.smart,ip-type=ipv6
error: couldn't connect the modem: 'GDBus.Error:org.freedesktop.libmbim.Error.Status.NoDeviceSupport: NoDeviceSupport'
```

Requesting dual-stack with `ip-type=ipv4v6` instead would ostensibly succeed,
but it would only yield an IPv4-only connection.

I also tried the modem under Windows 10. It was only able to get IPv4
connectivity there too, so it seems clear that this is a firmware issue. For
the record, I'm on the latest firmware available fom HP, version
`11.617.13.00.00`.

In order to figure out what was going on, I used the `option` serial port
driver to interact with the modem's [AT command
interface](https://en.wikipedia.org/wiki/Hayes_command_set#GSM):

```console
$ modprobe option
$ echo 03f0 a31d > /sys/bus/usb-serial/drivers/option1/new_id
$ cat /dev/ttyUSB0 &
```

The first thing to check is the output of the `AT+CGDCONT=?` command, a
[3GPP](http://www.3gpp.org/)-standardised command which returns the supported
data types:

```console
$ echo $'AT+CGDCONT=?\r' > /dev/ttyUSB0
+CGDCONT: (0-11),"IP",,,(0-2),(0-3),(0,1),(0,1),(0-2),(0,1)

OK
```

This is a smoking gun: there _should_ have been two more lines returned here,
one with `"IPV6"` and another with `"IPV4V6"` in the second comma-separated
field. This output is essentially the modem stating _«I only support IPv4»_.

Huawei has published an [extensive
document](http://download-c.huawei.com/download/downloadCenter?downloadId=51047&version=120450&siteCode=)
that details all the various AT commands supported by the modem. One of these
is `AT^IPV6CAP`, a Huawei proprietary command to _«Query IPv6 Capability»_ (see
page 281).  The possible return codes and their meanings are documented as
follows:

> 1  IPv4 only<br/>
> 2  IPv6 only<br/>
> 7  IPv4 only, IPv6 only and IPv4v6

So let's see what it says:

```console
$ echo $'AT^IPV6CAP?\r' > /dev/ttyUSB0
^IPV6CAP: 1

OK
```

This appears to confirm what the `AT+CGDCONT=?` command already told us, the
modem is IPv4-only. However, the `AT^IPV6CAP=?` command did give me a little
bit of hope:

```console
$ echo $'AT^IPV6CAP=?\r' > /dev/ttyUSB0
^IPV6CAP: (1,2,7)

OK
```

I interpret this to mean that the modem actually _does_ contain support for
IPv6 and IPv4v6; only that it is currently in an IPv4-only operational mode.
The question then becomes: how to change the operational mode to `7`? I have no
idea, unfortunately. It's not `AT^IPV6CAP=7`, for what it's worth.

I eventually had to give up on making IPv6 work with this modem. Perhaps it
will be fixed in a future firmware update, but until then my recommendation is
clear: __stay away from the _Huawei ME906s-158_ / _HP lt4132_ LTE modem!__

Support tickets were opened with both HP and Huawei support about the issue, by
the way. Despite several rounds of escalating their respective cases, none of
them were able to figure out a solution. HP support eventually gave up on
solving the case and shipped me a replacement _HP lt4120_ free of charge
instead. That did the trick; it turns out the _lt4120_ supports IPv6 perfectly.
I'll have to commend HP for good customer support here; they obviously
considered the lack of IPv6 support to be a real defect and did what was
necessary to fix the problem in the most efficient manner.

---
published: false
title: 'Making a Homenet router out of OpenWrt'
layout: post
---

This post provides step-by-step instructions on how to take a residential
gateway running [OpenWrt](https://openwrt.org), installing the software from
the [Hnet project](http://www.homewrt.org) on it, and finally converting it to
be a fully-fledged [Homenet](http://tools.ietf.org/wg/homenet/) router. The
post is quite long, but don't let that put you off - it's only because I go
through the process in minute detail. The entire conversion process shouldn't
take you more than 10-15 minutes.

Unlike the Hnet project's [own setup
instructions](http://www.homewrt.org/doku.php?id=run-conf), I'll use only the
OpenWrt web interface [LuCI](http://luci.subsignal.org), and provide plenty of
screenshots. That way I'm hoping to help make Hnet and Homenet a little bit
more accessible to users who might not be too comfortable working with
OpenWrt's command line interface.

If you don't know what Homenet is or why you would want your residential
gateway to be a Homenet router in first place, I suggest you go read my
[previous post, where I introduced
Homenet](/2015/10/02/homenet-the-future-of-home-networking.html).

## Prerequisites

First of all, you'll need a residential gateway with [OpenWrt 15.05 *Chaos
Calmer*](https://forum.openwrt.org/viewtopic.php?id=59528) installed. Consult
OpenWrt's [Table of Hardware](http://wiki.openwrt.org/toh/start) to check if
your device is supported. The device page linked from the table should contain
installation instructions. The router I'll be using in this walk-through is a
[Netgear WNDR3700v2](http://wiki.openwrt.org/toh/netgear/wndr3700).

Second, I recommend that you use a laptop with both wired and wireless
connectivity. That way, you can safely reconfigure the router's wired ports
while connected with wireless and vice versa, thus greatly reducing the risk of
inadvertently locking yourself out of your device. While it's certainly
possible to make do without, the following instructions will assume that you're
using such a laptop.

## The home network: bridged or routed?

Like most residential gateways, OpenWrt comes by default with a single logical
*LAN* interface which is just layer-2 bridge consisting of all the wired and
wireless interfaces in the router, excluding the *WAN* interface. That is
however not how a Homenet router is meant to operate. In Homenet, layer-3 is
king: each interface has its own isolated network segment, complete with its
own IP prefixes. Standard layer-3 routing is used whenever hosts on different
segments need to communicate.

In this post, I'll set it up the proper Homenet way. That said, a Homenet
router also supports traditional bridged LAN segments, so you can also set it
up that way if the idea of not having full layer-2 connectivity between all the
hosts in your home network scares you.

## Step 1: Install the Hnet software suite

OpenWrt 15.05 *Chaos Calmer* doesn't come with the Hnet software installed by
default, so our first order of business is to install it from the Internet.
Connect your router's *WAN* port to an Internet-connected network (such as
directly to your ISP or your pre-existing home LAN), and your laptop to one of
the router's *LAN* ports using wired Ethernet. You should now be able to access
LuCI, OpenWrt's web interface, at [http://openwrt.lan](http://openwrt.lan):

<a rel="walkthrough" href="/images/20151011-01-luci-login.png" class="fancybox"
title="OpenWrt 15.05 login screen"><img width="100%"
src="/images/20151011-01-luci-login.png"/></a>

Log in as `root` without a password. It will take you to LuCI's
[Status/Overview](http://openwrt.lan/cgi-bin/luci/admin/status/overview) page:

<a rel="walkthrough" href="/images/20151011-02-status-overview.png"
class="fancybox" title="The default Status Overview page"><img width="100%"
src="/images/20151011-02-status-overview.png"/></a>

It insists that you set a password, so head to
[System/Administration](http://openwrt.lan/cgi-bin/luci/admin/system/admin) to
do so:

<a rel="walkthrough" href="/images/20151011-03-system-administration.png"
class="fancybox" title="Set a password to make it stop naggging"><img
width="100%" src="/images/20151011-03-system-administration.png"/></a>

You can now optionally visit
[Network/Interfaces](http://openwrt.lan/cgi-bin/luci/admin/network/network) to
verify that the router's *WAN* interface has been automatically configured:

<a rel="walkthrough" href="/images/20151011-04-network-interfaces.png"
class="fancybox" title="Confirm the WAN connectivity"><img width="100%"
src="/images/20151011-04-network-interfaces.png"/></a>

Head to
[System/Software](http://openwrt.lan/cgi-bin/luci/admin/system/packages) and
click the **Update lists** button to refresh the list of software available for
download:

<a rel="walkthrough" href="/images/20151011-05-system-software.png"
class="fancybox" title="Hit the Update lists button"><img width="100%"
src="/images/20151011-05-system-software.png"/></a>

When that has completed, download and install the `ipset` package and then
`hnet-full`. (I'm not 100% certain that `ipset` is strictly necessary, but
you'll get a warning when installing `hnet-full` if `ipset` isn't already
installed.)

<a rel="walkthrough" href="/images/20151011-06-system-software.png"
class="fancybox" title="After updating package lists, install the ipset
package"><img width="100%" src="/images/20151011-06-system-software.png"/></a>
<a rel="walkthrough" href="/images/20151011-07-system-software.png"
class="fancybox" title="After installing ipset, install the hnet-full
package"><img width="100%" src="/images/20151011-07-system-software.png"/></a>

After the installation of `hnet-full`, all the software necessary for Homenet
operation is installed. It is now necessary to reboot the router in order for
the software to become fully operational (this is probably a bug). You can do
so from the
[System/Reboot](http://openwrt.lan/cgi-bin/luci/admin/system/reboot) page:

<a rel="walkthrough" href="/images/20151011-08-system-reboot.png"
class="fancybox" title="Reboot to fully activate the Hnet software suite"><img
width="100%" src="/images/20151011-08-system-reboot.png"/></a>

## Step 2: Disable the non-Homenet ULA prefix handling

After the reboot, head to
[Network/Interfaces](http://openwrt.lan/cgi-bin/luci/admin/network/network).
Near the bottom of the page there's a text field labelled **IPv6 ULA-Prefix**
that contains an auto-generated prefix. Empty this text field and then click
**Save & Apply**:

<a rel="walkthrough" href="/images/20151011-09-network-interfaces.png"
class="fancybox" title="Clear the IPv6 ULA-Prefix field"><img width="100%"
src="/images/20151011-09-network-interfaces.png"/></a>

Why is this necessary? Hnet generates and maintains its own ULA prefixes
independently of the **IPv6 ULA-Prefix** setting. However, due to a bug Homenet
interfaces created in LuCI will end up with *two* ULA prefixes assigned; the
native Homenet-maintained one in addition to the non-Homenet one specified in
**IPv6 ULA-Prefix**. Removing the non-Homenet setting successfully works around
this bug.

## Step 3: Convert the WAN interface to Homenet

Stay on the
[Network/Interfaces](http://openwrt.lan/cgi-bin/luci/admin/network/network),
page and make a note of which physical interface the default non-Homenet *WAN*
and *WAN6* interfaces are using (*eth1* in my case), then click the **Delete**
button in to remove them. You should now be left only with the default
non-Homenet *LAN* interface:

<a rel="walkthrough" href="/images/20151011-10-network-interfaces.png"
class="fancybox" title="After removal of the default WAN and WAN6 interfaces">
<img width="100%" src="/images/20151011-10-network-interfaces.png"/></a>

Once they're gone, click **Add new interface...**. You can give it any name you
want, except for *LAN*, *WAN*, or *WAN6*. Hnet will automatically detect the
role of an interface, as long as it does not have any of those special names.
(I'm calling mine *e0*, short for *Ethernet port 0*.) Choose the protocol
*Automatic Homenet (HNCP)*, set it to cover the same physical interface as the
old *WAN*/*WAN6* interface did, and finally click **Submit** and then **Save &
Apply**.

<a rel="walkthrough" href="/images/20151011-11-create-interface.png"
class="fancybox" title="Create a new Homenet interface"><img width="100%"
src="/images/20151011-11-create-interface.png"/></a>

If everything went well, you should be returned to the interface list, and
after a few second your new Homenet interface should show as having acquired
connectivity from the upstream network (assuming it's still connected to an
upstream network, that is):

<a rel="walkthrough" href="/images/20151011-12-network-interfaces.png"
class="fancybox" title="Confirm Internet connectivity via the new interface">
<img width="100%" src="/images/20151011-12-network-interfaces.png"/></a>

## Step 4: Convert the wireless interfaces to Homenet

We'll first need to remove the wireless interfaces from the default non-Homenet
*LAN* bridge. Hit **Edit** on the row with the *LAN* interface, and go to the
*Physical Settings* tab. Remove the tick in the checkbox next to any wireless
interface you see, and click **Save & Apply**.

<a rel="walkthrough" href="/images/20151011-13-interface-configuration.png"
class="fancybox" title="Remove the wireless interfaces from the LAN bridge">
<img width="100%" src="/images/20151011-13-interface-configuration.png"/></a>

Next, head to
[Network/Wifi](http://openwrt.lan/cgi-bin/luci/admin/network/wireless) to see
the list of wireless interfaces:

<a rel="walkthrough" href="/images/20151011-14-network-wifi.png"
class="fancybox" title="The default list of wireless interfaces"><img
width="100%" src="/images/20151011-14-network-wifi.png"/></a>

Click the **Edit** button for one of the wireless interfaces, tick the checkbox
next to *create:* and give the new interface a name. You can use any name you
want, except for *LAN*/*WAN*/*WAN6* as discussed above. You might also want to
take some time to explore the various tabs here in order to configure security
and encryption, wireless band and channel, country, and so on.

If your device has multiple wireless interface, I strongly suggest that you
also give them different *ESSID*s. This is because most wireless clients will
assume that all access points using the same ESSID connect to the same layer-2
segment. That's not the case in Homenet, so if a client roams from one AP to
another, it might experience connectivity issues. Using differing ESSIDs will
prevent this from occurring.

<a rel="walkthrough"
href="/images/20151011-15-wifi-interface-configuration.png" class="fancybox"
title="Change the ESSID and create a new wifi interface"><img width="100%"
src="/images/20151011-15-wifi-interface-configuration.png"/></a>

When you're happy with the setup, click **Save & Apply**. Repeat the process
for all the wireless interfaces in the list. The final step is to click each
interface's **Enable** button in the interface list to turn on the radio:

<a rel="walkthrough" href="/images/20151011-16-network-wifi.png"
class="fancybox" title="Both wireless interfaces reconfigured and enabled"><img
width="100%" src="/images/20151011-16-network-wifi.png"/></a>

Head back to
[Network/Interfaces](http://openwrt.lan/cgi-bin/luci/admin/network/network).
You should see the new wireless interfaces in the list:

<a rel="walkthrough" href="/images/20151011-17-network-interfaces.png"
class="fancybox" title="The interface list with the new wireless interfaces">
<img width="100%" src="/images/20151011-17-network-interfaces.png"/></a>

Click **Edit** for one of them. On the next page, set the protocol to
*Automatic Homenet (HNCP)* and click **Switch protocol** and then **Save &
Apply**.

<a rel="walkthrough" href="/images/20151011-18-interface-configuration.png"
class="fancybox" title="Switch the protocol to Automatic Homenet (HNCP)"><img
width="100%" src="/images/20151011-18-interface-configuration.png"/></a>

Repeat the process for any other wireless interfaces in the list. When you're
done, the interfaces should all have been configured with IP addresses:

<a rel="walkthrough" href="/images/20151011-19-network-interfaces.png"
class="fancybox" title="Both wireless interfaces reconfigured as Homenet"><img
width="100%" src="/images/20151011-19-network-interfaces.png"/></a>

At this point, disconnect your laptop's Ethernet cable and connect to one of
the ESSIDs you just created. If it works, congratulations! Your laptop is now
connected to a Homenet-handled network segment. From now on you'll need to
access LuCI at [http://openwrt.home](http://openwrt.home) (note that the domain
suffix has changed).

## Step 5: Create per-port VLANs in the embedded switch

(If you're going for a traditional bridged layer-2 home network, you can skip
this section.)

The external LAN ports on my router are connected to an embedded Ethernet
switch, which in turn has a single interface connected to the "CPU" where
OpenWrt runs. The following figure from the OpenWrt Wiki illustrates the
architecture:

<a href="http://wiki.openwrt.org/_media/media/toh/netgear/wndr3700-c.png"
class="fancybox" title="Netgear WNDR3700v2 architecture"><img width="100%"
src="http://wiki.openwrt.org/_media/media/toh/netgear/wndr3700-c.png"/></a>

I'll use VLANs to make each of the four external LAN ports their own Homenet
interface. This is done on the
[Network/Switch](http://openwrt.home/cgi-bin/luci/admin/network/vlan) page. My
WNDR3700v2's default configuration contains only a single VLAN:

<a rel="walkthrough" href="/images/20151011-20-network-switch.png"
class="fancybox" title="The default WNDR3700v2 VLAN configuration"><img
width="100%" src="/images/20151011-20-network-switch.png"/></a>

My new configuration consists of four VLANs, one for each external LAN port.
Each of the VLANs is set up as *untagged* for its associated external LAN port,
*tagged* for the CPU port, and *off* for all other ports. I've opted to give
each VLAN the same ID as the number of its associated external LAN port, but
this is just a matter of preference - at the end of the day, it doesn't matter
which values the VLAN IDs are set to. When you're happy, click **Save &
Apply**.

<a rel="walkthrough" href="/images/20151011-21-network-switch.png"
class="fancybox" title="Add one VLAN per physical port, all tagged on the CPU
port"><img width="100%" src="/images/20151011-21-network-switch.png"/></a>

## Step 5: Create Homenet interfaces for the LAN ports

Return to
[Network/Interfaces](http://openwrt.home/cgi-bin/luci/admin/network/network).
Delete the old *LAN* interface the same way you did with *WAN* and *WAN6*, so
you're left only with the new Homenet interfaces you've created so far:

<a rel="walkthrough" href="/images/20151011-22-network-interfaces.png"
class="fancybox" title="Old non-Homenet LAN interface has been removed"><img
width="100%" src="/images/20151011-22-network-interfaces.png"/></a>

What now remains to be done is to create Homenet interfaces for each of the
VLANs in the switch. This is done in the same way I created the *e0* interface
earlier; first, click **Add new interface...**. In the next view, give it a
name of your liking (except *LAN*/*WAN*/*WAN6*), chose the *Automatic Homenet
(HNCP)* protocol, set it to cover one of the VLAN interfaces you just created,
and click **Submit** and then **Save & Apply**.

<a rel="walkthrough" href="/images/20151011-23-create-interface.png"
class="fancybox" title="Create a new Homenet interface for a switch VLAN"><img
width="100%" src="/images/20151011-23-create-interface.png"/></a>

Repeat this procedure for each of the VLANs configured in the embedded switch.
When you're done, the list on the
[Network/Interfaces](http://openwrt.home/cgi-bin/luci/admin/network/network)
should look something like this:

<a rel="walkthrough" href="/images/20151011-24-network-interfaces.png"
class="fancybox" title="Finished Homenet Network/Interfaces list (top half)">
<img width="100%" src="/images/20151011-24-network-interfaces.png"/></a>
<a rel="walkthrough" href="/images/20151011-25-network-interfaces.png"
class="fancybox" title="Finished Homenet Network/Interfaces list (bottom
half)"><img width="100%"
src="/images/20151011-25-network-interfaces.png"/></a>

## Mission accomplished!

Congratulations! Your router is now a pure Homenet router. Head to
[Status/Homenet](http://openwrt.home/cgi-bin/luci/admin/status/hnet) to see a
dynamically updated graph of your Homenet topology:

<a rel="walkthrough" href="/images/20151011-26-status-homenet.png"
class="fancybox" title="Dynamic Homenet topology graph"><img width="100%"
src="/images/20151011-26-status-homenet.png"/></a>

Of course, with only a single Homenet router this graph isn't extremely
interesting, but at least it should show your router, its interfaces, and any
IP prefixes it has been assigned. You can click on various nodes in the graph
to get more details in JSON format.

Assuming you followed my advice on interface naming, none of the interfaces in
your router now have pre-determined roles such as *WAN* or *LAN*. You may, for
example, connect your upstream Internet connection to the port labelled *LAN 3*
and a regular host to the port labelled *WAN* if you want - it will work just
as fine as the other way around. This ability alone will give Homenet an
unprecedented level of «plug&play-ness» compared to the regular residential
gateways in sale today.

If you own several residential gateways supported by OpenWrt, try converting
them all to Homenet routers and connect them to each other in arbitrary ways -
including via wireless. They'll automatically discover each other and form a
coherent network topology, which the
[Status/Homenet](http://openwrt.home/cgi-bin/luci/admin/status/hnet) graph will
reflect in seconds. I've tried this and it *Just Works*. Good-bye, IPv4 NAT
stacking and DHCPv6-PD cascading! You shall <u>not</u> be missed. That said,
hosts or non-Homenet routers connecting to the Homenet will be granted a DHCPv6
Prefix Delegation if they ask for one.

It is also possible to connect your Homenet to multiple ISPs, from different
routers if you so prefer, and it should all *Just Work*. Well, in theory anyway
- I haven't yet tested Homenet with multiple ISPs myself. If you do, please let
me know how it worked out. You can reach me, and the Hnet team itself, at
`#hnet-hackers` at [freenode](http://freenode.net/).

---
layout: post
title: IPv6 on Get FTTH
description: IPv6 on Get FTTH
author: tore
---

[Get](https://www.get.no) ([Telia](https://www.telia.no)'s fixed broadbrand
brand in Norway) has recently launched an opt-in IPv6 pilot available to their
their fibre customers. I am happy to report that it works great and scores 10/10
on [test-ipv6.com](https://test-ipv6.com).

I first learned about this in [this
article](https://www.digi.no/artikler/innsikt-har-du-bare-gammeldags-internett-her-er-ipv6-planene-til-flere-norske-internettleverandorer/480306?key=Phnt5PTg)
(Norwegian), where Get states that they provide IPv6 to a handful of test
customers on fibre.

To join the pilot I simply had to ask their customer service. Shortly after I
received an e-mail with the new configuration settings: static IPv4 /30 and IPv6
/64 WAN link networks plus a static IPv6 /56 prefix for use on the LAN side.
(They do not use Neighbour Discovery or DHCPv6 for automatic configuration.)

My understanding is that Get can currently not configure their own CPE to act as
an IPv6 router, so it is necessary to bring your own HGW and leave the Get CPE
in bridge mode.

I am very happy to have native IPv6 at home again, and really hope that Get will
roll this out to all of their other FTTH customers in the near future.

---
published: true
title: 'Evaluating DHCPv6 relays'
layout: post
---

One of the few remaining IPv4-only services here at [Redpill
Linpro](http://www.redpill-linpro.com) is our provisioning infrastructure,
which is based on
[PXE](https://en.wikipedia.org/wiki/Preboot_Execution_Environment) network
booting. I've long wanted to do something about that. Now that more and more
servers are shipping with
[UEFI](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)
support, I am finally in a position to start looking at it.

I'm starting out by figuring out which DHCPv6 relay implementation we'll be
using. This post details my evaluation process, conclusion, and choice.

## Network topology

Most of the servers we want to provision are usually located in a dedicated
customer [VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) that is connected to
a set of redundant routers running Linux. The routers speak
[VRRP](https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol) on
each of the VLANs in order to decide which router is the primary one serving
the VLAN in question.

Furthermore, each of the routers have multiple redundant uplinks to the core
network, and are using a dynamic
[IGP](https://en.wikipedia.org/wiki/Interior_gateway_protocol) to ensure
optimal routing and fault tolerance. The DHCPv6 server is reached through the
core network using unicast routing.

The following figure illustrates the topology:

<a href="/images/20151102-dhcpv6-topology.png" class="fancybox" title="DHCPv6
topology"><img src="/images/20151102-dhcpv6-topology.png"/></a>

## Our desired capabilities

Our current IPv4-only network boot infrastructure relies heavily on using
Ethernet [MAC addresses](https://en.wikipedia.org/wiki/MAC_address) to
distinguish between clients. Being able to continue to do so will make the
introduction of IPv6 support quick and easy. We would therefore like for the
implementation to support the DHCPv6 [Client Link-Layer Address
Option](http://tools.ietf.org/html/rfc6939).

As discussed in the previous section, the network configuration on the routers
is dynamic and could change without notice. An ideal DHCPv6 relay
implementation would be able to notice such changes and automatically adapt to
the new environment. In our environment, this would mean being able to cope
with:

* A new VLAN interface showing up, e.g., when we provision a new customer.
* The IP address configuration on a VLAN interface changing, e.g., due to a
  VRRP fail-over event.
* The route to the DHCPv6 server changing from one uplink interface to another,
  e.g., due to changed route metrics in our core network.

Finally, regarding the software itself, we'd like for it to be:

* [Free and
  open-source](https://en.wikipedia.org/wiki/Free_and_open-source_software).
* Actively maintained and developed.
* Already packaged for [Ubuntu](http://www.ubuntu.com).

## Available implementations and their capabilities

From what I was able to determine, there are four available open-source DHCPv6
relay implementations. These are, in alphabetical order:

* [dhcpv6](https://fedorahosted.org/dhcpv6/) (1.2.0)
* [Dibbler](https://klub.com.pl/dhcpv6/) (1.0.1RC1)
* [ISC DHCP](https://www.isc.org/downloads/dhcp/) (4.3.2)
* [WIDE-DHCPv6](http://wide-dhcpv6.sourceforge.net/) (20080615)

The versions I tested are shown in parenthesis.

### Desired capability 1: Support for the Client Link-Layer Address Option

Of the tested implementations, only *Dibbler* supported this feature. It is
enabled by adding the line `option link-layer` in the configuration file.

### Desired capability 2: Detecting new interfaces on the fly

Disappointingly enough, none of the tested implementations were able to do
this.  *dhcpv6* will by default listen on all available interfaces, but it does
not detect new interfaces showing up after it has started.

The other three implementations all require that the listening interfaces be
configured explicitly.

### Desired capability 3: Detecting IPv6 addresss changing during runtime

Only *WIDE-DHCPv6* was able to do this. It appears to check what the local
address on the interface is every time it relays a packet, so it always sets
the `link address` field in the relayed DHCPv6 packet correctly.

The other three implementations read in the global address (or lack thereof)
for each interface when they start, and do not notice any changes. Thus, there
is a risk that the `link address` field in their relayed packets is set
incorrectly.

### Desired capability 4: Coping with route to DHCPv6 server changing

Only *dhcpv6* supports this without any weirdness. The address of the DHCPv6
server is specified with the `-su` command line option, and packets are relayed
to it using a standard routing lookup.

*ISC DHCP* and *WIDE-DHCPv6* behave in a rather bizarre way. They both require
that the interface facing the DHCPv6 server is explicitly specified on the
command line, but for some reason they completely ignore it and instead use a
standard routing lookup to reach the server.

*Dibbler* also requires that the upstream interfaces are explicitly configured.
If there is no route to the DHCPv6 server on one of these interfaces, it will
log the following error for each DHCPv6 request:

    Low-level layer error message: Unable to send data (dst addr: 2001:db8::d)
    Failed to send data to server unicast address.

That said, it is possible to simply configure both `eth0` and `eth1` as
upstream interfaces. This will ensure all requests are correctly relayed
regardless of which interface has the active route to the DHCPv6 server. That
said, I'm only awarding halv a point to *Dibbler* here, both due to the
clunkyness of the workaround and the constant stream of error messages it will
result in.

### Desired capability 5: Free and open-source software

Yes! Every tested implementation qualifies.

### Desired capability 6: Actively maintained

Only *Dibbler* and *ISC DHCP* appear to be. According to its own homepage,
*dhcpv6* was discontinued in 2009. *WIDE-DHCPv6* has not seen any release since
2008.

### Desired capability 7: Available in Ubuntu's software archive

Only *dhcpv6* is missing, the rest are an `apt-get install` away.

## Conclusion

Out of a maximum 7 points, the final scores are as follows:

1. *Dibbler*: 4.5 points
2. *ISC DHCP*: 4 points
3. *WIDE-DHCPv6*: 4 points
4. *dhcpv6*: 2 points

Disappointingly enough, none of them are able to run continously in a dynamic
environment like ours. To work around this, we'll probably have to devise a
system that automatically generates new configuration and restarts the relay
whenever a network configuration change is detected. Should a DHCPv6 request
arrive exactly when the relay is being restarted, it will likely be retried
within seconds, so this is extremely unlikely to cause any operational issues.

This workaround will handle the lack of desired capabilities 2 through 4. After
disregarding these, only *Dibbler* gets full score (due to its support for the
*Client Link-Layer Address Option*). *Dibbler* is thus the obvious choice.

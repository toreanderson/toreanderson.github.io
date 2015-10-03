---
published: true
title: 'Homenet - the future of home networking'
layout: post
---

Today's residential home networks are quite simple. They usually have a single
Internet connection, which is plugged in to the
[*WAN*](https://en.wikipedia.org/wiki/Wide_area_network) port of a [residential
gateway](https://en.wikipedia.org/wiki/Residential_gateway).  The gateway will
typically feature a few wired Ethernet ports and a wireless access point, which
are in most cases bridged together to form a single layer-2
[*LAN*](https://en.wikipedia.org/wiki/Local_area_network) segment. The LAN
segment is configured with [private IPv4
addresses](https://en.wikipedia.org/wiki/Private_network), and the gateway
performs [IPv4
NAT](https://en.wikipedia.org/wiki/Network_address_translation#One-to-many_NAT),
so that the hosts and devices on the LAN segment can communicate with the IPv4
Internet.

If the requirements of the home network are equally simple, this will work well
enough for most users. However, the moment you start adding more functionality,
things get complicated quickly. For example, how does one go about introducing
[another Internet connection](https://en.wikipedia.org/wiki/Multihoming)? Or
another residential gateway? Or [IPv6](https://en.wikipedia.org/wiki/IPv6)? Or
all of the above? Someone competent in computer networking might well be able
to set it up, but [Joe Public](https://en.wikipedia.org/wiki/John_Q._Public)
will probably have to resort to trial and error. He might end up with a
completely non-functional network, or he might get lucky and stumble across
configuration that ostensibly works - but even in this case, it is very likely
that the home network would suffer a loss of functionality and/or performance
in the process.

## Introducing the IETF Homenet working group

Fortunately, the fact that residential home networks were rapidly falling
behind the technology curve wasn't lost on the [IETF](https://www.ietf.org).
In 2011, the working group *[Home
Networking](http://tools.ietf.org/wg/homenet)* - colloquially known as
*Homenet* - was founded to create a set of standards that would allow even the
most non-technical user to fully unleash the potential of his home network in a
self-configuring «plug and play» manner. Quoting from the [working group
charter](http://datatracker.ietf.org/wg/homenet/charter/):

> This working group focuses on the evolving networking technology within and
> among relatively small "residential home" networks. For example, an obvious
> trend in home networking is the proliferation of networking technology in an
> increasingly broad range and number of devices.  [...]
>
> Home networks need to provide the tools to handle these situations in a
> manner accessible to all users of home networks. Manual configuration is
> rarely, if at all, possible, as the necessary skills and in some cases even
> suitable management interfaces are missing.
>
> The purpose of this working group is to focus on this evolution, in
> particular as it addresses the introduction of IPv6, by developing an
> architecture addressing this full scope of requirements:
>
> o prefix configuration for routers<br/>
> o managing routing<br/>
> o name resolution<br/>
> o service discovery<br/>
> o network security<br/>

The decision to base the new standards on an IPv6 foundation was likely an easy
one to make. Not only is IPv6 the only future-proof option, it also comes with
certain features that facilitate automatic and self-configuring networks (such
as ubiquitous [link-local
addresses](https://en.wikipedia.org/wiki/Link-local_address#IPv6) and
[SLAAC](https://en.wikipedia.org/wiki/IPv6_address#Stateless_address_autoconfiguration)).
That said, Homenet won't deprive anyone of their IPv4 connectivity - the
working group isn't blind to the reality that IPv4 will remain a necessity for
most users in the years to come:

> The group should assume that an IPv4 network may have to co-exist alongside
> the IPv6 network and should take this into account insofar as alignment with
> IPv6 is desirable.

So far, the Homenet working group has published one RFC titled [IPv6 Home
Networking Architecture Principles](http://tools.ietf.org/html/rfc7368), and is
currently actively working on [a number of other
Internet-Drafts](http://tools.ietf.org/wg/homenet/) that describe the
nitty-gritty details of how it all fits together.

## Running code: the Hnet project

While it's clearly important to have the Homenet standard properly documented
in RFCs, these documens aren't particularly useful on their own. At the end of
the day, it's the availability of functioning implementations of those RFCs,
i.e., *running code*, that truly matters.

In spite of being a work in progress, the current draft specifications has
proven mature enough for the [*Hnet* project](http://www.homewrt.org) to build
a working open-source Homenet implementation. The Hnet project is included in
the latest stable [OpenWrt](http://www.openwrt.org) release, [version 15.05
*Chaos Calmer*](https://forum.openwrt.org/viewtopic.php?id=59528).

That means that if you own a residential gateway device that's amongst the
[several hundred models supported by
OpenWrt](http://wiki.openwrt.org/toh/start) (or are willing to spend something
like €20-€30 on one), you can already <u>**today**</u> take Homenet for a spin
and experience the future of home networking. In the past few weeks I've been
doing just that, and I can say that I am pleasantly surprised at how well it
actually works. It's even fully integrated in OpenWrt's web interface - no
command line familiarity required.  I've converted my own home network to be
based exclusively on OpenWrt and Hnet, and I see no reason to going back to my
old legacy setup.

In an upcoming post I will explain how to take a default installation of
OpenWrt 15.05 *Chaos Calmer*, install the software from the Hnet project, and
configure it to be a Homenet router. Stay tuned!

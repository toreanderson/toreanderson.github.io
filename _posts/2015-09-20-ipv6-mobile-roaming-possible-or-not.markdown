---
published: true
title: 'IPv6 mobile roaming: Possible or not?'
layout: post
---

Since 2012 I have opted to use only mobile providers who provide me with IPv6
connectivity. To begin with, I was a customer of [Network
Norway](https://en.wikipedia.org/wiki/Network_Norway). They were kind enough to
include me in their IPv6-only pilot with
[DNS64](http://tools.ietf.org/html/rfc6147)/[NAT64](http://tools.ietf.org/html/rfc6146)
pilot. Unfortunately, due to being aqcuired over and over again, their IPv6
pilot lost momentum and never went anywhere. In 2014, I changed to [Telenor
Norway](http://www.telenor.no). Telenor provides two
[APNs](https://en.wikipedia.org/wiki/Access_Point_Name) with IPv6 support:
`telenor.mobil`, which supports only `IPV6` PDP context type only, as well as
providing DNS64/NAT64 service; and `telenor.smart`, their default APN, which
supports the `IP`, `IPV6`, and `IPV4V6` PDP context types
([CGN](https://en.wikipedia.org/wiki/Carrier-grade_NAT) for IPv4, no
DNS64/NAT64). All Telenor subscribers can configure their devices to use these
APNs; IPv6 is a fully supported production service in Telenor's mobile network.

During these years I have travelled a lot. I have been to much of Europe, as
well as in several other countries including Canada, Japan, and USA. Wherever I
went, roaming with IPv6 has in my experience been one of those things that
*Just Works*. Therefore it surprised me greatly to hear that during the [APNIC
40](https://conference.apnic.net/40) conference, [Telstra
Australia](https://www.telstra.com.au/)'s [Sunny
Yeung](https://www.linkedin.com/in/xevious)
[stated](https://twitter.com/apnic/status/641470146968600576) that *«until
every carrier has activated IPv6 there is no way to activate IPv6 for
international roaming»*. Sunny couldn't possibly be right, could he? Had I just
*imagined* that IPv6 roaming worked for me?

As luck would have it, an upcoming business trip to Sweden gave me the
opportunity double-check whether or not IPv6 roaming works. I used [Jason
Fesler](https://twitter.com/jasonfesler)'s excellent
[test-ipv6.com](http://ds.test-ipv6.com) site to prove beyond any doubt that
**IPv6 roaming** ***does*** **work**, as demonstrated below:

![test-ipv6.com screenshot from roaming Jolla phone](/_images/20150920-jolla-test-ipv6-screenshot.jpg)
![Console screenshot from roaming Jolla phone](/_images/20150920-jolla-console-screenshot.jpg)

The two screenshots above are from my [Jolla Phone](http://www.jolla.com) when
connected to the `telenor.smart` APN using an dual-stack `IPV4V6` PDP context.
As the second screenshot shows, the phone has been provisioned by a globally
unique IPv6 address as well as a [private IPv4
address](http://tools.ietf.org/html/rfc1918). (A keen observer might note that
there is no indication that I am actually roaming in either screenshot. You'll
just have to take my word for it; I simply don't know which command is used to
show the roaming status in Jolla's [Sailfish OS](https://sailfishos.org/).)

![Screenshot from roaming laptop](/_images/20150920-laptop-screenshot.png)

This screenshot shows me roaming using the built-in cellular modem in my
laptop. This modem is not exactly state of the art, so it does not support the
dual-stack `IPV4V6` PDP context type. Therefore, I instead use a single-stack
`IPV6` PDP context towards the `telenor.mobil` APN. You can also see that I am
using [clatd](https://github.com/toreanderson/clatd) to set up a
[464XLAT](http://tools.ietf.org/html/rfc6877) CLAT interface. This provides
any legacy IPv4-only applications running on my laptop with *seemingly* native
IPv4 connectivity they can use to communicate with the IPv4 Internet.

Sunny, I hope you consider this post very good news. After all, it might mean
that deploying IPv6 in Telstra's mobile network isn't the impossibility that
your APNIC 40 statement suggests you currently think it is. While it's evident
that you haven't found the way to do it yet, the efforts your colleagues in
Telenor Norway demonstrates that there clearly is **a** way. I know several of
the folks involved in Telenor's IPv6 efforts, they are a friendly bunch - do
not hesitate to contact me if you want me to introduce you to them! I'm certain
that they would heppily help you finding the way to deploying IPv6 in Telstra's
mobile network.

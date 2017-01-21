---
published: true
title: 'IPv6 roaming in the United Kingdom'
layout: post
---

Earlier this week I visited the United Kingdom to attend the excellent
[UKNOF36](https://indico.uknof.org.uk/event/38/) meeting.

As I usually do when when going abroad, I spent some time testing to what
extent IPv6 works while roaming in the various
[PLMNs](https://en.wikipedia.org/wiki/Public_land_mobile_network) I have access
to.

The previous posts in this series are:

* [IPv6 roaming in Sweden](/2016/11/21/ipv6-roaming-in-sweden.html)
* [IPv6 roaming in Belgium and
  Romania](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html)

These posts contain some more technical background about the testing
methodology, so I suggest you skim through them in order to better interpret
the test results in this post.

# Test results

## [O2](http://www.o2.co.uk) - MCCMNC 23410

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | N/A              | N/A                  |
| Telenor Norway | 2G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 4G   | N/A              | N/A                  |

I was not able to get 4G coverage with any of my SIM cards in this network,
which probably means that neither Tele 2 nor Telenor has a 4G roaming agreement
with O2.

While in 2G and 3G coverage Tele 2 and Telenor's HLR/HSS blacklisting trick
comes into play.  (See the [*IPv6 roaming in Belgium and
Romania*](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html) post for an
explanation of what that trick is.)

## [Vodafone](http://www.vodafone.co.uk) - MCCMNC 23415

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | Fails            | IPv4-only connection |
| Telenor Norway | 2G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | IPv4-only connection |

In 2G and 3G coverage this looks like the standard HLR/HSS blacklisting trick.
However the 4G behaviour is very unusual, as the blacklisting trick does not
apply here.

IPv6-only PDP contexts work fine with my Telenor SIM card, but not with my Tele
2 one. My phone logs this latter failure as being due to an *unknown/invalid
cause code* so I have no idea about what's going on here.

Dual stack `IPV4V6` PDP contexts do not work in 4G coverage with any of my SIM
cards, and any attempt to use them results in IPv4-only connectivity. As this
is not caused by Telenor and Tele 2's blacklisting trick, the logical
conclusion is that Vodafone is deliberately blocking dual stack PDP contexts
from being used in their end.

I also saw very similar IPv6-hostile behaviour in [Vodafone
Romania](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html#vodafonehttpswwwvodafonero---mccmnc-22601)'s
network. I wonder if that is a coincidence or not.

## [3](http://www.three.co.uk) - MCCMNC 23420

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | N/A              | N/A                  |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | N/A              | N/A                  |
| Telenor Norway | 2G   | N/A              | N/A                  |
| Telenor Norway | 3G   | N/A              | N/A                  |
| Telenor Norway | 4G   | N/A              | N/A                  |

It appears Telenor doesn't have a roaming agreement with this operator (my
phone reported *no access to network*). With my Tele 2 SIM card I could not get
neither 2G or 4G coverage, only 3G. In 3G coverage Tele 2's HLR/HSS
blacklisting trick comes into play.

## [EE](http://ee.co.uk) - MCCMNC 23430

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 2G   | Fails (cause 27) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 27) | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

This looks pretty much as expected for an operator where the HLR/HSS
blacklisting trick is being used to block IPv6 in 2G and 3G coverage. However,
it's the first time I've seen this result in 3GPP cause code 27 (*missing or
unknown APN*). Usually I see code 33 (*requested service option not
subscribed*). Not sure if this difference is significant somehow, but the
outcome is the same in any case.

When in 4G coverage, both IPv6-only and dual stack PDP contexts worked just
fine.

That said, I did have trouble getting dual stack to work in EE 4G coverage when
I used another one of phones. Unfortunately I did not have time to investigate
that further during my brief visit. Next time, perhaps.

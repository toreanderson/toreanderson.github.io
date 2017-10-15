---
published: true
title: 'IPv6 roaming in Sweden'
layout: post
---

I attended the [Netnod Tech Meeting
2017](https://www.netnod.se/netnod-events/netnod-tech-meeting-2017) in
Stockholm earlier this week. As I usually do when when going abroad, I spent
some time testing to what extent IPv6 works while roaming in the various
[PLMNs](https://en.wikipedia.org/wiki/Public_land_mobile_network) I have access
to.

The previous posts in this series are:

* [IPv6 roaming in Belgium and
  Romania](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html)
* [IPv6 roaming in the United Kingdom](/2017/01/21/ipv6-roaming-in-the-uk.html)
* [IPv6 roaming in Czechia](/2017/10/12/ipv6-roaming-in-czechia.html)

# Test results

## [Telia](https://www.telia.se) - MCCMNC 24001

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 2G   | Fails            | IPv4-only connection |
| Telenor Norway | 3G   | Fails            | IPv4-only connection |
| Telenor Norway | 4G   | N/A (no service) | N/A (no service)     |

It would appear that Telenor Norway does not have a 4G roaming agreement with
Telia. My phone was unable to register in Telia's 4G network, at least.

In 3G and 2G coverage, I could register, but IPv6 did not work. Requesting dual
stack connectivity would only yield IPv4. This is of course a quite acceptable
outcome for the vast majority of users, as the Internet will ostensibly work
just fine..

In all likelihood the IPv6 failures observed on 2G and 3G is due to Telenor
Norway's
[HLR](https://en.wikipedia.org/wiki/Network_switching_subsystem#Home_location_register_.28HLR.29)
removing the IPv6 capabilities from my subscriber profile before transmitting
it to Telia's
[vSGSN](https://en.wikipedia.org/wiki/GPRS_core_network#Serving_GPRS_support_node_.28SGSN.29).
This is done to forestall the possible IPv6-related failures described in [RFC
7445](https://tools.ietf.org/html/rfc7445) sections
[3](https://tools.ietf.org/html/rfc7445#section-3) and
[6](https://tools.ietf.org/html/rfc7445#section-6).

Presumably Telenor Norway will, at some point in the future, remove this IPv6
capability blacklisting for Telia, after having ascertained that Telia's 2G/3G
network does not have any issues with supporting the IPv6 PDP types.

## [3](https://www.tre.se) - MCCMNC 24002

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 3G   | Fails            | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

In 4G coverage, IPv6 works perfectly. In 3G coverage, it fails - presumably due
to the same IPv6 capability blacklisting as described above for Telia.

I did however notice at one point that if I connected a dual stack IPV4V6 PDP
context while in 3's 4G network, and then moved into an area that had only 3G
coverage, IPv6 kept working perfectly. Thus it would appear that 3's 3G network
has no issues supporting visiting subscribers using IPv6.

Note that 3 does not operate a 2G network.

## [Tele2](https://www.tele2.se) - MCCMNC 24007

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 3G   | Fails            | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

Same results as for 3.

I did not get to test physically moving from 4G to 3G coverage. That said, I
know for a fact that Tele2 provides IPv6 to their own mobile subscribes, so it
seems like a safe bet to assume that their 3G network would support IPv6 just
fine, if it hadn't been for Telenor's IPv6 capability blacklisting.

## [Telenor Sweden](https://www.telenor.se) - MCCMNC 24008

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 3G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

Perfect score. It is perhaps not surprising that if a single Swedish operator
would be fully «IPv6-approved», and therefore exempt from Telenor Norway's IPv6
capability blacklisting, it would be their Swedish sister company Telenor
Sweden.

It might also be worth noting that Telenor Sweden is the vPLMN my phone prefers
to register in if I leave it in the default automatic mode. Therefore, for
Telenor Norway subscribers, IPv6 _Just Works_ while visiting Sweden - unless
one manually fiddles around with the network settings.

## [Net4Mobility](http://net4mobility.com) - MCCMNC 24024

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 2G   | Works perfectly  | Works perfectly      |

Perfect score.

The Net4Mobility PLMN is, as far as I can understand, a joint venture between
Tele2 and Telenor Sweden that provides shared 2G coverage for both of those
providers. That is, if I lock my phone on to MCCMNC 24007 (Tele2) or 24008
(Telenor Sweden), it will nevertheless change to 24024 (Net4Mobility) if I also
limit it to 2G only.

This means Net4Mobility is logically part of Telenor Sweden's network, and thus
it makes sense that it, like MCCMNC 24008, is exempt from Telenor Norway's IPv6
capability blacklisting.

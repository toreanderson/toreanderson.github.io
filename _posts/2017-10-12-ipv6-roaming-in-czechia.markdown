---
published: true
title: 'IPv6 roaming in Czechia'
layout: post
---

I spent this weekend in Czechia. As I usually do when when going abroad, I
spent some time testing to what extent IPv6 works while roaming in the various
[PLMNs](https://en.wikipedia.org/wiki/Public_land_mobile_network) I have access
to.

The previous posts in this series are:

* [IPv6 roaming in Sweden](/2016/11/21/ipv6-roaming-in-sweden.html)
* [IPv6 roaming in Belgium and
  Romania](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html)
* [IPv6 roaming in the United Kingdom](/2017/01/21/ipv6-roaming-in-the-uk.html)

Those posts contain some more technical background about the testing
methodology, so I suggest you skim through them in order to better interpret
the test results in this post.

# Test results

## [T-Mobile](https://www.t-mobile.cz) - MCCMNC 23001

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 2G   | Fails            | IPv4-only connection |
| Telenor Norway | 3G   | Fails            | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

While in 2G and 3G coverage Telenor's HLR/HSS blacklisting trick comes into
play, blocking any kind of IPv6 usage. (See the [*IPv6 roaming in Belgium and
Romania*](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html) post for an
explanation of what that trick is.)

These results do not necessarily mean that T-Mobile has a problem with
supporting IPv6 on 2G and/or 3G. It could very well be that it is entirely due
to Telenor's HLR/HSS blacklisting, and that it would start working immediately
if Telenor were to move T-Mobile to their IPv6 whitelist.

When in 4G coverage, IPv6-only and dual stack work perfectly. This is as
expected, because the HLR/HSS blacklisting trick does not work on 4G.

## [O2](https://www.o2.cz) - MCCMNC 23002

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 2G   | Fails            | IPv4-only connection |
| Telenor Norway | 3G   | Fails            | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

Exactly the same results as T-Mobile. Telenor's HLR/HSS IPv6 blacklisting in
action.

## [Vodafone](https://www.vodafone.cz) - MCCMNC 23003

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Telenor Norway | 2G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 3G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

Vodafone is the only Czech operator to get a perfect score. IPv6-only and dual
stack connectivity always works, regardless of the technology used.

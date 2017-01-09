---
published: true
title: 'IPv6 roaming in Belgium and Romania'
layout: post
---

I briefly visited Belgium and Romania last month. Using SIM cards from [Tele 2
Sweden](http://www.tele2.se) and [Telenor Norway](https://www.telenor.no), both
of which support the dual-stack `IPV4V6` and IPv6-only `IPV6` [PDP
context](https://en.wikipedia.org/wiki/GPRS_core_network#PDP_context) types, I
spent some time testing whether or not I was able to get working IPv6 Internet
connectivity while roaming in the various available
[PLMNs](https://en.wikipedia.org/wiki/Public_land_mobile_network).

In many cases, IPv6-only connection attempts failed completely. Furthermore,
dual stack connection attempts more often than not resulted in an IPv4-only
Internet connection. Full [test results](#test-results) below.

These frequent failures are however not as dramatic as they might sound. Both
Tele 2 and Telenor are using a blacklisting trick that blocks their subscribers
from using IPv6 when roaming in certain operators (whose IPv6 capabilities
hasn't yet been verified). See [RFC 7445 section
3](https://tools.ietf.org/html/rfc7445#section-3) and [section
6](https://tools.ietf.org/html/rfc7445#section-6) for technical details on how
this trick works. When roaming in an operator blacklisted in this manner,
IPv6-only connection attempts made in 2G/3G coverage will fail with 3GPP cause
code 33 (*requested service option not subscribed*), while dual stack
connection attempts will result in IPv4-only Internet connectivity.

The good news is that when my devices were set up to request dual-stacked
`IPV4V6` PDP contexts, they would in every single case get at least the same
level of Internet connectivity as they would when requesting an IPv4-only `IP`
PDP context; having the devices request dual-stacked connectivity had no
downside whatsoever.

I've also performed the same kind of IPv6 roaming testing in
[Sweden](/2016/11/21/ipv6-roaming-in-sweden.html) a while back.

# Test results

## Belgian PLMNs

### [Proximus](http://www.proximus.be) - MCCMNC 20601

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 2G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 3G   | Works perfectly  | Works perfectly      |

I was not able to test in 4G/LTE coverage, as it appears that neither Tele 2
nor Telenor have a 4G/LTE roaming agreement with Proximus.

Tele 2 is applying the blacklisting trick described above. Telenor, on the
other hand, does not and IPv6 works perfectly.

### [Orange](https://www.orange.be) - MCCMNC 20610

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 2G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 33) | IPv4-only connection |

In the Orange PLMN I got identical behaviour with both my SIM cards. It appears
both Tele 2 and Telenor are blacklisting, and there is no 4G/LTE roaming
agreement.

As with Tele 2/Proximus, dual stack «works» in the sense that I get IPv4-only
Internet connectivity.

### [BASE](https://www.base.be) - MCCMNC 20620

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 2G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

BASE demonstrates how the blacklisting trick only works on 2G/3G and not on 4G.
Both Tele 2 and Telenor are blacklisting here, but nevertheless dual stack and
IPv6-only works perfectly if the data PDP context is established while in 4G
coverage.

## Romanian PLMNs

### [Vodafone](https://www.vodafone.ro) - MCCMNC 22601

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 32) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 32) | IPv4-only connection |
| Tele 2 Sweden  | 4G   | Fails (cause 32) | IPv4-only connection |
| Telenor Norway | 2G   | Fails (cause 32) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 32) | IPv4-only connection |
| Telenor Norway | 4G   | Fails (cause 32) | IPv4-only connection |

Vodafone is an interesting case. The fact that IPv6 and dual stack fails on 2G
and 3G with 3GPP cause code 32 (*service option not supported*) instead of 33,
and the fact that it also fails in the same way on 4G/LTE, indicate that this
is *not* caused by the blacklisting trick. Instead, it would appear that
Vodafone is deliberately blocking visitors from using of IPv6 on their network.

For operators such as Tele 2 and Telenor, this is not such a big deal, as
dual-stack still «works» by falling back on IPv4-only connectivity. Operators
using [464XLAT](https://tools.ietf.org/html/rfc6877), on the other hand, will
likely find Vodafone's behaviour hugely problematic.

### [Telekom](https://www.telekom.ro) - MCCMNCs 22603 (2G) and 22606 (3G)

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 2G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 3G   | Fails (cause 33) | IPv4-only connection |

Identical results as with Orange in Belgium. I was unable to get 4G/LTE
coverage, and both Tele 2 and Telenor applies blacklisting on 2G/3G.

### [Digi.Mobil](http://www.rcs-rds.ro) - MCCMNC 22605

Neither Tele 2 nor Telenor seems to have any form of roaming agreement with
this operator, at least I was completely unable to register in their network,
and thus unable to perform any IPv6 testing.

### [Orange](https://www.orange.ro) - MCCMNC 22610

| Home PLMN      | Tech | IPV6 PDP context | IPV4V6 PDP context   |
|----------------|------|------------------|----------------------|
| Tele 2 Sweden  | 2G   | Fails (cause 33) | IPv4-only connection |
| Tele 2 Sweden  | 3G   | Fails (cause 33) | IPv4-only connection |
| Telenor Norway | 2G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 3G   | Works perfectly  | Works perfectly      |
| Telenor Norway | 4G   | Works perfectly  | Works perfectly      |

Tele 2 does not appear to have a 4G/LTE roaming agreement with Orange, and the
blacklisting trick is being used on 2G/3G.

With Telenor, on the other hand, there's no blacklisting and both IPv6-only and
dual stack works perfectly on all technologies.

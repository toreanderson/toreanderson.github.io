---
published: true
title: 'IPv6 roaming in the United States'
layout: post
---

I spent some time in the United States last month. Equipped with SIM cards from
both Tele2 Sweden (MCCMNC 24007) and Telenor Norway (MCCMNC 24201), I set out
to test IPv6 while roaming, as I usually do while abroad.

The previous posts in this series are:

* [IPv6 roaming in Belgium and
  Romania](/2017/01/09/ipv6-roaming-in-belgium-and-romania.html)
* [IPv6 roaming in the United Kingdom](/2017/01/21/ipv6-roaming-in-the-uk.html)
* [IPv6 roaming in Czechia](/2017/10/12/ipv6-roaming-in-czechia.html)
* [IPv6 roaming in Sweden](/2017/10/15/ipv6-roaming-in-sweden.html)

There were two mobile networks that I was able to register in and test:
[AT&T](https://www.att.com/) (3G/4G, no 2G) and
[T-Mobile](https://www.t-mobile.com/) (2G/3G/4G). The test results are found
below.

I could also see three other 4G networks show up in a network scan (Caprock
Cellular, [Sprint](https://www.sprint.com/) and
[Verizon](https://www.verizonwireless.com/)), but none of those were available
to me. Presumably neither Tele2 nor Telenor have roaming arrangements with any
of those operators.

## AT&T - MCCMNC 310410

| Home PLMN  | Tech | IPV6 PDP context      | IPV4V6 PDP context        |
|------------|------|-----------------------|---------------------------|
| Tele2 SE   | 3G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Tele2 SE   | 4G   | Works perfectly       | Works perfectly           |
| Telenor NO | 3G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Telenor NO | 4G   | Works perfectly       | Works perfectly           |

## T-Mobile - MCCMNC 310260

| Home PLMN  | Tech | IPV6 PDP context      | IPV4V6 PDP context        |
|------------|------|-----------------------|---------------------------|
| Tele2 SE   | 2G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Tele2 SE   | 3G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Tele2 SE   | 4G   | Works perfectly       | Works perfectly           |
| Telenor NO | 2G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Telenor NO | 3G   | Fails (cause code 33) | IPv4-only (cause code 50) |
| Telenor NO | 4G   | Works perfectly       | Works perfectly           |

3GPP cause code #33 means _«requested service option not subscribed»_, while
cause code #50 means _«PDP type IPv4 only allowed»_. The latter doesn't
indicate a fatal error, as I do automatically get a working IPv4-only
connection to the Internet.

The fact that IPv6 consistently fails on 2G/3G is in all likelihood due to
Tele2/Telenor's
[HLR](https://en.wikipedia.org/wiki/Network_switching_subsystem#Home_location_register_.28HLR.29)
removing the IPv6 capabilities from my subscriber profile before transmitting
it to the AT&T/T-Mobile's
[vSGSN](https://en.wikipedia.org/wiki/GPRS_core_network#Serving_GPRS_support_node_.28SGSN.29).

Tele2 and Telenor do this to forestall the possible IPv6-related failures
described in [RFC 7445](https://tools.ietf.org/html/rfc7445) sections
[3](https://tools.ietf.org/html/rfc7445#section-3) and
[6](https://tools.ietf.org/html/rfc7445#section-6). In this case, it is in all
likelihood an unnecessary precaution, considering that both AT&T and T-Mobile
are known to have deployed IPv6 to their own customers.

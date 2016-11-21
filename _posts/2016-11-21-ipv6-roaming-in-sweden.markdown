---
published: true
title: 'IPv6 roaming in Sweden'
layout: post
---

There has been much talk about IPv6 potentially causing problems for
subscribers roaming between mobile networks. There's [an entire
RFC](https://tools.ietf.org/html/rfc7445) dedicated to the possible failure
cases, and it has even been claimed that [*«until every carrier has activated
IPv6 there is no way to activate IPv6 for international
roaming»*](https://twitter.com/apnic/status/641470146968600576).

My own personal experience with IPv6 roaming hasn't been quite that bleak, so
I've therefore decided to thoroughly test IPv6 roaming whenever I have the
chance and chronicle the results here, one post per country I visit.

As I am currently attending [Internetdagarna 2016](https://internetdagarna.se/)
in Stockholm, I now have the opportunity to perform this testing for Sweden.

There are [four mobile network operators in
Sweden](https://en.wikipedia.org/wiki/List_of_mobile_network_operators_of_Europe#Sweden).
However, the only one I could roam in while using my [Telenor
Norway](https://www.telenor.no/) SIM card appeared to be [Telenor
Sweden](http://www.telenor.se/). (No big surprise, there.) Thus I was only able
to test the Telenor Sweden
[PLMN](https://en.wikipedia.org/wiki/Public_land_mobile_network).

The tests were performed by separately attempting to establish single-stack
`IPV6` and dual-stack `IPV4V6` data bearers and then visiting
[ds.test-ipv6.com](http://ds.test-ipv6.com). This procedure was repeated for
all available access technologies I was able to use. The results were as
follows:

| Visited PLMN   | MCCMNC | Tech | IPV6 bearer       | IPV4V6 bearer      |
|----------------|--------|------|-------------------|--------------------|
| Telenor Sweden | 24024  | 2G   | 10/10 (IPv6-only) | 10/10 (dual stack) |
| Telenor Sweden | 24008  | 3G   | 10/10 (IPv6-only) | 10/10 (dual stack) |
| Telenor Sweden | 24008  | 4G   | 10/10 (IPv6-only) | 10/10 (dual stack) |

Thus I can conclude that IPv6 roaming in the Telenor Sweden PLMN works 100%
perfectly. Kudos to Telenor Norway and Telenor Sweden for making that happen!

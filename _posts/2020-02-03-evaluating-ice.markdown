---
layout: post
title: Evaluating ice
description: Evaluating ice
author: tore
---

The newest [PLMN](https://en.wikipedia.org/wiki/Public_land_mobile_network) in
Norway is [ice](https://ice.no). I had heard rumours that they supported IPv6,
so I decided to order a subscription to try them out for myself.

# IPv6 support

IPv6 did unfortunately not work out of the box on my Android 10 phone. The
reason was that the *APN protocol* and *APN roaming protocol* settings were set
to **IPv4** by default. That is very unfortunate, as it ascertains that
essentially none of their customers will actually use IPv6.

That said, IPv6 started working fine after I changed those settings to
**IPv4/IPv6**, as demonstrated by a quick visit to
[test-ipv6.com](http://test-ipv6.com):


![Figure 1: test-ipv6.com screenshot]({{site.post_image}}/evaluating-ice/figure1.png)

IPv6 also worked just fine in my Linux laptop:

```console
[:~] $ mmcli -b 10
  --------------------------------
  General            |  dbus path: /org/freedesktop/ModemManager1/Bearer/10
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwp0s20f0u3c3
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: ice.net
                     |    roaming: allowed
                     |    ip type: ipv4v6
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 100.83.189.109
                     |     prefix: 30
                     |    gateway: 100.83.189.110
                     |        dns: 185.83.167.68, 185.83.167.4
                     |        mtu: 1500
  --------------------------------
  IPv6 configuration |     method: static
                     |    address: 2a05:9cc4:7e:85fb:5a2c:80ff:fe13:9208
                     |     prefix: 64
                     |    gateway: ::
                     |        dns: 2a05:9cc4:f000:1::4, 2a05:9cc3:f000:1::4
                     |        mtu: 1500
  --------------------------------
  Statistics         |   duration: 30
```

As shown above, ice are using Carrier-grade NAT with IPv4 addresses assigned
from [RFC 6598](https://tools.ietf.org/html/rfc6598) space. I can not fault
them for that, given that there are [no new public IPv4 addresses to be had
from the RIPE NCC](https://www.ripe.net/manage-ips-and-asns/ipv4/ipv4-run-out).

The IPv6 address is public, as expected. I can ping it from external sources
using ICMPv6, but it appears that all other externally initiated IPv6 traffic
is blocked somewhere inside ice's core network. This is true for their mobile
broadband APN `internet` as well, unfortunately. This overzealous firewalling
means that the usefulness of IPv6 in mobile broadband and IoT use cases is
unnecessarily limited.

# Mobile broadband: wifi router mandatory

One of the reasons I wanted to evaluate ice was to see if their offerings were
suitable for a mobile broadband IoT project I manage at work. (They were not,
due to their IPv6 firewalling.)

However, while investigating this I noticed something I found rather odd,
namely that ice do not allow their customers to [order mobile broadband
subscriptions](https://www.ice.no/mobilt-bredband/) without also purchasing a
wireless router from them.

I have zero use for a wireless router; the IoT devices I was considering them
for all have built-in LTE modems. So does my laptop, for that matter. Why ice
do not offer a SIM card-only mobile broadband subscription is beyond my
comprehension. Hopefully they will do so in the future.

# GDPR violations

After I logged in to the online customer portal for the first time, I noticed
this interesting section on my profile page:

![Figure 2: Consents section in portal]({{site.post_image}}/evaluating-ice/figure2.png)

This basically states that I have given consents (Norwegian: *samtykker*) to
two different categories of marketing as well as to customer surveys. I am
certain, however, that I was never asked to consent to any of these while
signing up as a subscriber - thus making this a clear violation of [Article 7
GDPR](https://eur-lex.europa.eu/eli/reg/2016/679/oj#d1e2001-1-1).

I certainly do not appreciate companies not treating my personal data
responsibly according to the GDPR, so I promptly sent a request to ice's data
protection officer, requesting that they document when and how I gave these
alleged consents. It shall be interesting to see what they answer.

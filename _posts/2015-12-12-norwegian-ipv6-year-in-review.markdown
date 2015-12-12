---
published: true
title: 'Norwegian IPv6 year in review'
layout: post
---

2016 is soon approaching. In this post I'll take a look in the rear-view mirror
to see how well we did in Norway with regards to IPv6 deployment in 2015. I
focus on the status on the end-user side of things, that is, the extent of IPv6
deployment amongst Norwegian ISPs. This is due to the fact that my employer
[Redpill Linpro](http://www.redpill-linpro.com) mainly provide managed services
to content providers, so the traffic entering our dual-stacked data centres can
only tell a story about how the ISPs are doing.

## End of 2015 status: 7-8% IPv6 adoption

Our customer [VG](http://www.vg.no) is kind enough to let me use their web site
traffic to publish [graphs detailing IPv6 deployment in
Norway](http://fud.no/munin/Networking/Networking/index.html). VG is the
largest Norwegian web site, appealing to a broad audience. Therefore the
collected data gives a very good basis for making accurate statistics about the
Norwegian population in general. The graph below visualises this data, showing
how the Norwegian IPv6 adoption rate has developed throughout 2015:

<a href="/images/20151212-ipv6-adoption.png" class="fancybox" title="Norwegian
IPv6 adoption in 2015"><img src="/images/20151212-ipv6-adoption.png"/></a>

This shows that at the time of writing, **7.3%** of all the traffic that
reached VG in the previous week was IPv6. While this is an increase compared to
the beginning of the year, it is a disappointingly small one - only about a
single percentage point.

The graph usually peaks above 8.5% every weekend and drops below 7% during
weekdays. This tells us that that Norwegians are much more likely to have IPv6
at home than at work.

It is worth noting that other large content providers are also measuring IPv6
usage in Norway, and their measurements appear to confirm that my numbers are
in the right ballpark:
[Akamai](https://www.stateoftheinternet.com/trends-visualizations-ipv6-adoption-ipv4-exhaustion-global-heat-map-network-country-growth-data.html#countries)
currently reports 7% deployment, while
[Google](http://www.google.com/intl/en/ipv6/statistics.html#tab=per-country-ipv6-adoption&tab=per-country-ipv6-adoption)
reports 7.94%.

On a global scale, Norway is actually quite average. The last few months in
Google's [global IPv6 adoption
graph](http://www.google.com/intl/en/ipv6/statistics.html#tab=ipv6-adoption&tab=ipv6-adoption)
are eerily similar to the VG data for the same period. Ranking the countries by
their Google-reported IPv6 adoption percentage shows we're #15 in the world:

1.  Belgium - 39.43%
2.  Switzerland - 26.22%
3.  United States - 22.95%
4.  Portugal - 21.57%
5.  Germany - 20.49%
6.  Greece - 19.05%
7.  Peru - 15.77%
8.  Luxembourg - 15.59%
9.  Czech Republic - 9.84%
10. Ecuador - 9.64%
11. Estonia - 9.60%
12. St. Kitts & Nevis - 9.16%
13. Malaysia - 8.88%
14. Japan - 8.77%
15. Norway - 7.94%

I'd say this is nothing to celebrate, except perhaps that we fare better than
all of our Nordic neighbours (but probably not for long, as Finland is #16 and
is climbing fast).

## How did the Norwegian ISPs fare in 2015?

<a href="/images/20151212-isp-comparsion.png" class="fancybox" title="Norwegian
IPv6 traffic, broken down by ISP"><img
src="/images/20151212-isp-comparsion.png"/></a>

Norway is essentially an IPv6 duopoly. Over 80% of all IPv6 traffic originates
from two ISPs: the incumbent telco [Telenor](http://www.telenor.no) and the
cable ISP [Get](http://www.get.no).

On the two next spots we find the NREN [UNINETT](http://www.uninett.no) and the
fibre ISP [Altibox](http://www.altibox.no). These two are responsible for a
tiny (but measurable) share of IPv6 traffic each.

The four networks I've mentioned account for over 90% of all the IPv6 traffic.
The remaining 10% is the long tail, consisting of way too many networks to
mention individually here, as they are responsible for only a miniscule amount
of IPv6 traffic each.

Below I examine more closely how Telenor and Get fared in 2015. Note that the Y
axis of the following graphs shows a percentage of *all* traffic, i.e.,
including IPv4 traffic and IPv6 traffic from other ISPs. It does not say
anything about how many of Telenor's or Get's subscribers are dual-stacked.
Unfortunately, I don't have such statistics at the moment. [Akamai
does](https://www.stateoftheinternet.com/trends-visualizations-ipv6-adoption-ipv4-exhaustion-global-heat-map-network-country-growth-data.html#networks),
however.

### Zooming in on Telenor

Telenor uses two distinct IPv6 prefixes, allowing me to make two graphs: One
for their mobile subscribers, and another all their wired broadband subscribers
(i.e., cable, DSL, and fibre).

<a href="/images/20151212-telenor.png" class="fancybox" title="Share of
Norwegian traffic coming from IPv6-enabled Telenor subscribers (mobile
excluded)"><img src="/images/20151212-telenor.png"/></a>

The above graph is for Telenor's wired broadband customers, which is the
largest group overall. It is disappointing to see that this group has not grown
*at all* in 2015; rather, it looks like the percentage at the end of the year
will be slightly *lower* than it was at the start of the year! Telenor is a
long way from having rolled out IPv6 to their entire customer base, so I am
truly hoping that they will pick up the pace again in 2016. (In case you're
wondering, the marked drop in the end of January was caused by a critical
problem in their network.)

<a href="/images/20151212-telenor-mobil.png" class="fancybox" title="Share of
Norwegian traffic coming from IPv6-enabled Telenor subscribers (mobile
only)"><img src="/images/20151212-telenor-mobil.png"/></a>

Telenor's mobile subscribers are doing much better. Their IPv6 traffic has more
than doubled in 2015. It is however quite worrying to see that the trend the
last couple of months is clearly a negative one.

Telenor is by far Norway's largest ISP. In absolute numbers, they are without
question the largest source of IPv6 traffic too. However, according to Akamai,
only 5.5% of Telenor's subscribers are IPv6-capable, so there is clearly a huge
potential for increased IPv6 deployment in Telenor in the future.

### Zooming in on Get

<a href="/images/20151212-get.png" class="fancybox" title="Share of Norwegian
traffic coming from IPv6-enabled Get subscribers"><img
src="/images/20151212-get.png"/></a>

Get is growing their IPv6 deployment. It's not going very fast, but it is a
steady positive trend. (The decline in the summer months is better explained by
Get's customers leaving home to go on holidays than anything Get did.)

According to Akamai, 24.1% of Get's customers are IPv6-capable. This means Get
is the ISP with the largest share of IPv6-capable customers in Norway - well
done! At the same time, three out of four of their customers remain IPv4-only,
so there is plenty of potential for further improvements in 2016.

## Summary and hopes for 2016

If I'm being honest, I must say that 2015 turned out to be a rather
disappointing year for IPv6 adoption in Norway.  In the second half of 2014 I
observed a rapid growth, but this trend did unfortunately not continue in 2015.

I'm hoping that Get and Telenor will intensify their IPv6 deployments in 2016.
Especially Telenor has a lot of potential for growth - for example, their cable
customers must currently manually opt-in to get IPv6, and all the Apple devices
on their mobile network remain IPv4-only. If neither of those two things change
in 2016 I'll be **very** disappointed.

When it comes to the other major national Norwegian ISPs, I truly hope that
2016 will be the year when I'll start seeing significant amounts of IPv6
traffic from them. I'm thinking in particular about the likes of Altibox,
[NetCom](http://www.netcom.no), and [NextGenTel](http://www.nextgentel.no)
here.

I'd like to end on a more positive note, though. I'll do that by commending
[Difi](https://standard.difi.no/english), a.k.a. *The Agency for Public
Management and eGovernment*, for having made significant progress towards
making [IPv6 support a mandatory requirement in the Norwegian public
sector](https://standard.difi.no/forslag-og-saker/saker/revisjon-bor-ipv4-og-ipv6-dual-stack-vaere-obligatorisk-forvaltningsstandard/vurdering-bor-kravet-om-dual-stack-ipv4-og-ipv6-gjores-obligatorisk).
In 2016 this will likely become Norwegian "law". The Norwegian public sector is
huge and wealthy, so the moment the service providers start realising that
lacking IPv6 support will disqualify them from bidding on lucrative government
contracts, I think we'll see quite a few laggards scrambling to catch up.

Happy New IPv6 Year!

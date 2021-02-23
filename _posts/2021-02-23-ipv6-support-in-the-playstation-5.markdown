---
published: true
title: 'IPv6 support in the PlayStation 5'
layout: post
---

Almost five years after I [wrote about the PlayStation 4's lacklustre IPv6
support](/2016/06/15/ipv6-support-in-the-playstation-4.html), I have finally
managed to get my hands on the PlayStation 5 – and I have of course taken a
close look at its IPv6 capabilities.

## Does the PlayStation 5 support IPv6 at all?

It does indeed! In the *View Connection Status* page of the network settings,
it displays any assigned IPv6 address, gateway and DNS server; right next to
their IPv4 counterparts.

![Connection Status]({{ site.post_image
}}/ipv6-support-in-the-playstation-5/connection-status.jpg)

This is a clear improvement over the PS4, whose IPv6 support was not
acknowledged by the user interface at all.

## What does the the PS5 use IPv6 for?

Not much, really. I have seen it use IPv6 for the following:

* `IN AAAA` DNS queries.
* A HTTP `GET` request towards `http://ena.net.playstation.net/netstart/ps4`
  *[sic]* immediately after connecting to the network. The response from the
  server is `403 Forbidden`. As I observed back in 2016, the PS4 did the exact
  same thing.
* HTTPS requests towards `https://image.api.playstation.com` while browsing the
  PlayStation Store app.
* The Netflix app uses IPv6 for both API/metadata and video traffic.
  Interestingly enough the Netflix app appears to use its own built-in stub
  resolver with 8.8.8.8 as its upstream, so its `IN AAAA` queries are using
  IPv4 transport.

I have also seen it perform `IN AAAA` DNS queries for several IPv4-only
`*.playstation.com` and `*.playstation.net` domain names.

Some other things that appear to be IPv4-only include:

* `IN A` DNS queries.
* Logging in to the PlayStation Network.
* Browsing the PlayStation Store (except for images as previously noted).
* Downloading games and media apps from the PlayStation Store.
* Syncing game trophy lists.
* Playing videos with the NRK TV, Twitch and YouTube media apps.
* Online multiplayer with Fortnite.
* The built-in Internet speed test.

## Technical implementation details

* It does not realy support IPv6-only networks. If there's no DHCPv4 service on
  the network, it will claim that the network connection failed. But Netflix
  works regardless, see below.
* It supports SLAAC, the RA *RDNSS Option* and DHCPv6 – both stateful and
  stateless.
* If no IPv6 DNS server is being provided by the RA *RDNSS Option* or DHCPv6 it
  will not configure IPv6 addressing or routing either. This is probably
  because…
* …it uses its IPv4 DNS server for all `IN A` DNS queries, and its IPv6 DNS
  server for all `IN AAAA` queries.
* When using SLAAC, the Interface ID appears to be randomised and changes on
  every reconnect.
* If `OnLink=0` is present in the RA *Prefix Information Option*, it will not
  configure an address using SLAAC even though `Autonomous=1`. This is probably
  a bug.
* If `Managed=1` in the RA, it will use stateful DHCPv6 to obtain an address
  using `IA_NA`. It will also request DNS servers in the same exchange.
* Stateful DHCPv6 address assignment through `IA_NA` works fine even though
  there is no *Prefix Information Option* in the RAs.
* If addresses are available from both stateful DHCPv6 and SLAAC, it will
  prefer the one from DHCPv6. The UI only shows a single globally scoped IPv6
  address.
* If `Managed=0` and `OtherConfig=1` in the RA, it will use stateless DHCPv6 to
  obtain DNS servers.
* If `OtherConfig=1`, it will prefer DNS servers learned from DHCPv6 over any
  learned from the RA RDNSS Option. If `OtherConfig=0`, the opposite is true.

The PS5 was running system software version 20.02-2.50.00.08-00.00.00.0.1
during my testing.

## IPv6-only and DNS64/NAT64

If there is no DHCPv4 service on the LAN, the network settings UI will say
**Failed** for both *Wired LAN 1* and *Internet connection*. In spite of that,
the *View Connection Status* page does show an IPv6 address, DNS server and
gateway. Not much work in this state, I mostly encounter errors saying *You're
offline*, *No Internet connection available*, and so forth. The exception is the
Netflix app, which continues to work just fine! (It is now using the system IPv6
DNS server instead of 8.8.8.8.)

If I keep DHCPv4 disabled but start advertising a DNS64-enabled DNS server, the
PS5 still claims that *Wired LAN 1* and *Internet connection* is **Failed**.
However the *View PlayStation Network Status* page has begun working (this is
just an embed of
[https://status.playstation.com](https://status.playstation.com)). Twitch also
starts working (using NAT64 for its Internet traffic). Fortnite, NRK TV,
PlayStation Store and YouTube, on the other hand, still do not work.

Lastly, if I re-enable DHCPv6 while leaving DNS64/NAT64 in place, it does not
seem to be using IPv6 via NAT64 for much. PlayStation Store downloads, Fortnite,
NRK TV and YouTube continue to use IPv4 only.


## Conclusion

The PS5 does support IPv6, and the Netflix app proves that IPv6 connectivity is
available for use. Apart from Netflix, though, there is not much use of IPv6.
That goes both for the system software and its bundled apps, as well as for
third-party apps and games.

Some of that might be attributable to a lack of IPv6 support on the server side,
as evidenced by `IN AAAA` queries for hostnames that have no IPv6 addresses. On
the other hand, when I use DNS64 to make those `IN AAAA` queries be answered, it
doesn't really make much of a difference – IPv4 is still preferred most of the
time.

Furthermore, most of the PS5 apps and games I tested do not make any `IN AAAA`
queries in the first place, so DNS64/NAT64 would not help in any case.

Thus software upgrades would be necessary to make these apps and games fully
IPv6 capable. The YouTube app is a good example of this; it is well known that
the YouTube servers supports IPv6, but their PS5 app never issues any `IN AAAA`
queries, so it ends up using IPv4 for its video traffic.

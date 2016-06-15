---
published: true
title: 'IPv6 support in the PlayStation 4'
layout: post
---

The other day, I noticed with great interest that my [PlayStation
4](https://www.playstation.com/ps4/) was using IPv6 to communicate with the
Internet. I'm fairly certain that this behaviour is new, so I decided to
investigate.

This is what appeared on the wire when it connected to the network:

```
  1 0.000000000           :: -> ff02::16     ICMPv6 110 Multicast Listener Report Message v2
  2 0.072956000           :: -> ff02::1:ffe2:19c7 ICMPv6 78 Neighbor Solicitation for fe80::2d9:d1ff:fee2:19c7
  3 0.799982000           :: -> ff02::16     ICMPv6 90 Multicast Listener Report Message v2
  4 1.600965000 fe80::2d9:d1ff:fee2:19c7 -> ff02::16     ICMPv6 90 Multicast Listener Report Message v2
  5 2.957012000 fe80::2d9:d1ff:fee2:19c7 -> ff02::2      ICMPv6 70 Router Solicitation from 00:d9:d1:e2:19:c7
  6 2.970763000 fe80::385a:20ff:fe70:f441 -> fe80::2d9:d1ff:fee2:19c7 ICMPv6 270 Router Advertisement from 3a:5a:20:70:f4:41
  7 2.971328000 fe80::2d9:d1ff:fee2:19c7 -> ff02::1:2    DHCPv6 110 Solicit XID: 0xe0e8c5 CID: 0003000100d9d1e219c7
  8 2.973796000 fe80::385a:20ff:fe70:f441 -> fe80::2d9:d1ff:fee2:19c7 DHCPv6 191 Advertise XID: 0xe0e8c5 CID: 0003000100d9d1e219c7 IAA: 2a02:fe0:c071:f00a::f1e
  9 2.974148000 fe80::2d9:d1ff:fee2:19c7 -> ff02::1:2    DHCPv6 152 Request XID: 0xe0e8c5 IAA: 2a02:fe0:c071:f00a::f1e CID: 0003000100d9d1e219c7
 10 2.977070000 fe80::385a:20ff:fe70:f441 -> fe80::2d9:d1ff:fee2:19c7 DHCPv6 223 Reply XID: 0xe0e8c5 CID: 0003000100d9d1e219c7 IAA: 2a02:fe0:c071:f00a::f1e
 11 2.977472000           :: -> ff02::1:ff00:f1e ICMPv6 78 Neighbor Solicitation for 2a02:fe0:c071:f00a::f1e
 12 3.000971000 fe80::2d9:d1ff:fee2:19c7 -> ff02::16     ICMPv6 90 Multicast Listener Report Message v2
 13 3.400970000 fe80::2d9:d1ff:fee2:19c7 -> ff02::16     ICMPv6 90 Multicast Listener Report Message v2
 14 3.977343000 fe80::2d9:d1ff:fee2:19c7 -> ff02::1:ff70:f441 ICMPv6 86 Neighbor Solicitation for fe80::385a:20ff:fe70:f441 from 00:d9:d1:e2:19:c7
 15 3.977615000 fe80::385a:20ff:fe70:f441 -> fe80::2d9:d1ff:fee2:19c7 ICMPv6 86 Neighbor Advertisement fe80::385a:20ff:fe70:f441 (rtr, sol, ovr) is at 3a:5a:20:70:f4:41
 16 3.977874000 2a02:fe0:c071:f00a::f1e -> 2a02:fe0:1:2:1:0:1:110 DNS 103 Standard query 0xc4e3  AAAA ena.net.playstation.net
 17 3.987868000 2a02:fe0:1:2:1:0:1:110 -> 2a02:fe0:c071:f00a::f1e DNS 241 Standard query response 0xc4e3  CNAME ena.net.playstation.net.edgekey.net CNAME e4963.dscg.akamaiedge.net AAAA 2a02:26f0:ac:181::1363 AAAA 2a02:26f0:ac:197::1363
 18 3.988383000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 TCP 94 62420→80 [SYN] Seq=0 Win=65535 Len=0 MSS=1440 WS=64 SACK_PERM=1 TSval=415148157 TSecr=0
 19 4.005888000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e TCP 94 80→62420 [SYN, ACK] Seq=0 Ack=1 Win=28560 Len=0 MSS=1440 SACK_PERM=1 TSval=3194590031 TSecr=415148157 WS=32
 20 4.006231000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 TCP 86 62420→80 [ACK] Seq=1 Ack=1 Win=65664 Len=0 TSval=415148175 TSecr=3194590031
 21 4.006361000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 HTTP 166 GET /netstart/ps4 HTTP/1.1
 22 4.021963000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e TCP 86 80→62420 [ACK] Seq=1 Ack=81 Win=28576 Len=0 TSval=3194590047 TSecr=415148175
 23 4.022418000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e HTTP 587 HTTP/1.1 403 Forbidden  (text/html)
 24 4.022479000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e TCP 86 80→62420 [FIN, ACK] Seq=502 Ack=81 Win=28576 Len=0 TSval=3194590048 TSecr=415148175
 25 4.022780000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 TCP 86 62420→80 [ACK] Seq=81 Ack=503 Win=65152 Len=0 TSval=415148191 TSecr=3194590048
 26 4.022849000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 TCP 86 62420→80 [FIN, ACK] Seq=81 Ack=503 Win=65664 Len=0 TSval=415148191 TSecr=3194590048
 27 4.037492000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e TCP 86 80→62420 [ACK] Seq=503 Ack=82 Win=28576 Len=0 TSval=3194590063 TSecr=415148191
 28 4.045960000 2a02:26f0:ac:181::1363 -> 2a02:fe0:c071:f00a::f1e TCP 86 [TCP Dup ACK 27#1] 80→62420 [ACK] Seq=503 Ack=82 Win=28576 Len=0 TSval=3194590071 TSecr=415148191
 29 4.046281000 2a02:fe0:c071:f00a::f1e -> 2a02:26f0:ac:181::1363 TCP 74 62420→80 [RST] Seq=82 Win=0 Len=0
```

There are several things I find noteworthy here:

1. It supports [DHCPv6](https://tools.ietf.org/html/rfc3315). Since the DHCPv6
   client runs in user space, this strongly indicates that it's a deliberate
   move by Sony.
2. It performs DNS requests over IPv6. A stub resolver also runs in user space,
   so it's another indication that this is not accidental.
3. It uses IPv6 to call home to the dual-stacked URL
   [http://ena.net.playstation.net/netstart/ps4](http://ena.net.playstation.net/netstart/ps4).
4. The call home URL returns a `403 Forbidden` error. However, it does so when
   accessed using IPv4 as well, so this might not mean much.

For the record, the call home request does not include any personal information
beyond the source IP address and a URL indicating it's a PS4. That said, the
request itself is more than enough for Sony to generate useful statistics on
how many PS4s with IPv6 Internet access there are out there. The following is
the complete call home request made:

```
GET /netstart/ps4 HTTP/1.1
Connection: close
Host: ena.net.playstation.net
```

So far I've not seen it use IPv6 for anything else than what I've described
above. An application like [Netflix](https://netflix.com), which *ought* to use
IPv6 whenever possible, does not. It would appear, therefore, that this is just
small beginnings, perhaps done primarily to gather statistics. Nevertheless, I
am very excited to see that Sony has begun work on implementing IPv6 support
for the PS4.

## Technical details

I first noticed the IPv6 capability after upgrading to [system
software](https://www.playstation.com/en-us/support/system-updates/ps4/)
version 3.50. I can't rule out that it showed up in an earlier update, though,
since I haven't actively looked for it after installing earlier updates.

I tested various different network environments to figure out what exactly the
PS4 supports. It would appear that Sony has done a thorough job:

* It supports assignment of global IPv6 addresses using both
  [SLAAC](https://tools.ietf.org/html/rfc4862) and [DHCPv6
  IA_NA](https://tools.ietf.org/html/rfc3315). When using SLAAC, the Interface
  Identifier appears to be randomly generated. That is, the IID does not embed
  the PS4's MAC address, and it changes every time the PS4 reconnects to the
  network.
* It will learn IPv6 DNS servers from both the [Recursive DNS Server RA
  Option](https://tools.ietf.org/html/rfc6106) and DHCPv6.
* Addresses and/or DNS servers learned from DHCPv6 are preferred over those
  learned from ICMPv6 Router Advertisements (if any).
* It will start a DHCPv6 client only if either the `Managed` or `OtherConfig`
  RA flag is set. If `Managed=1`, it will solicit both IA_NA and DNS
  configuration; otherwise, if `OtherConfig=1`, it will send a DHCPv6
  `Information-request` message to obtain DNS configuration only.

I did find a couple of bugs too:

* It would sometimes attempt to use its link-local address to communicate with
  the DNS server or the HTTP call-home web server, which doesn't work. This
  suggests that there is a bug in the PS4's [default address
  selection](http://tools.ietf.org/html/rfc6724) logic, or that it failed to
  activate its SLAAC- or DHCPv6-assigned addres. Simply re-connecting to thet
  network would usually resolve this issue.
* If address assignment is SLAAC-only, and the advertised prefix is off-link,
  no IPv6 Internet traffic is seen. In this case, the PS4 does not even start
  the DHCPv6 client even though `OtherConfig=1`. This is clearly a bug; there's
  no reason why SLAAC can't work perfectly well with off-link prefixes.

The next time I get a system software update, I'll make sure to re-do all these
tests and report any changes in a new post.

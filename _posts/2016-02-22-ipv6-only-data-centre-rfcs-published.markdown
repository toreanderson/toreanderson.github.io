---
published: true
title: 'IPv6-only data centre RFCs published'
layout: post
---

I'm very pleased to report that my *SIIT-DC*
[RFCs](https://www.ietf.org/rfc.html) were published by the
[IETF](https://www.ietf.org) last week. If you're interested in learning how to
operate an IPv6-only data centre while ensuring that IPv4-only Internet users
will remain able to access the services hosted in it, you should really check
them out.

Start out with [*Stateless IP/ICMP Translation for IPv6 Data Center
Environments* (RFC 7755)](https://tools.ietf.org/html/rfc7755). This document
describes the core functionality of SIIT-DC and the reasons why it was
conceived.

If you think that you can't possibly make your data centre IPv6-only yet
because you still need to support few legacy IPv4-only applications or devices,
continue with [RFC 7756](https://tools.ietf.org/html/rfc7756). This document
describes how the basic SIIT-DC architecture can be extended to support
IPv4-only applications and devices, allowing them to live happily in an
otherwise IPv6-only network.

The third and final document is [*Explicit Address Mappings for Stateless
IP/ICMP Translation* (RFC 7757)](https://tools.ietf.org/html/rfc7757). This
extends the previously existing [SIIT](https://tools.ietf.org/html/rfc6145)
protocol, making it flexible enough to support SIIT-DC. This extension is not
specific to SIIT-DC; other IPv6 transition technologies such as
[464XLAT](https://tools.ietf.org/html/rfc6877) and
[IVI](https://tools.ietf.org/html/rfc6219) also make use of it. Unless you're
implementing an IPv4/IPv6 translation device, you can safely skip [RFC
7757](https://tools.ietf.org/html/rfc7757). That said, if you want a deeper
understanding on how SIIT-DC works, I recommend you take the time to read [RFC
7757](https://tools.ietf.org/html/rfc7757) too. 

## So what is SIIT-DC, exactly?

SIIT-DC is a novel approach to the IPv6 transition that we've developed here at
[Redpill Linpro](http://www.redpill-linpro.com). It facilitates the use of
IPv6-only data centre environments in the transition period where a significant
portion of the Internet remains IPv4-only. One could quite accurately say that
SIIT-DC delivers *«IPv4-as-a-Service»* for data centre operators.

In a nutshell, SIIT-DC works like this: when an IPv4 packet is sent to a
service hosted in a data centre (such as a web site), that packet is
intercepted by a device called an *SIIT-DC Border Relay* (BR) as soon as it
reaches the data centre. The BR translates the IPv4 packet to IPv6, after which
it is forwarded to the IPv6 web server just like any other IPv6 packet. The
server's reply gets routed back to a BR, where it is translated from IPv6 to
IPv4, and forwarded through the IPv4 Internet back to the client. Neither the
client nor the server need to know that translation between IPv4 and IPv6 is
taking place; the IPv4 client thinks it's talking to a regular IPv4 server,
while the IPv6 server thinks it's talking to a regular IPv6 client.

There are several reasons why an operator might find SIIT-DC an appealing
approach. In no particular order:

* It facilitates IPv6 deployment without accumulation of IPv4 technical debt.
  The operator can simply switch from IPv4 to IPv6, rather than committing to
  operate IPv6 in parallel with IPv4 for the unforseeable future (i.e., *dual
  stack*). This greatly reduces complexity and operational overhead.
* It doesn't require the native IPv6 infrastructure to be built in a certain
  way. Any IPv6 network is compatible with SIIT-DC. It does not touch native
  IPv6 traffic from IPv6-enabled users. This means that when the IPv4 protocol
  eventually falls into disuse, no migration project will be necessary -
  SIIT-DC can be safely removed without any impact to the IPv6 infrastructure.
* It maximises the utilisation of the operator's public IPv4 addresses. If all
  the operator has available is a /24, every single of those 256 addresses can
  be used to provide Internet-facing services and applications. No addresses go
  to waste due to them being assigned to routers or backend servers (which do
  not need to communicate with the public Internet). It is no longer necessary
  to waste addresses by rounding up IPv4 LAN prefix sizes to the nearest power
  of two. Never again will it be necessary to expand a server LAN prefix, as it
  will be IPv6-only and thus practically infinitely large.
* Unlike IPv4 NAT, it is completely stateless. Therefore, it scales in
  the same way as a standard IP router: the only metrics that matter are
  packets-per-second and bits-per-second. Its stateless nature makes it trivial
  to deploy; the BRs can be located anywhere in the IPv6 network. It is
  possible to spread the load between multiple BRs using standard techniques
  such as anycast or ECMP. High availability and redundancy are easily
  accomplished with the use of standard IP routing protocols. 
* Unlike some kinds of IPv4 NAT, it doesn't hide the source address of IPv4
  users. Thus, the IPv6-only application servers remain able to perform tasks
  which depend on the client's source address, such as geo-location or abuse
  logging.
* It allows for IPv4-only applications or devices to be hosted in an otherwise
  IPv6-only data centre. This is accomplished through an optional component
  called a *SIIT-DC Edge Relay*. This is what is being described in [RFC
  7756](https://tools.ietf.org/html/rfc7756).

## The history of SIIT-DC

I think it was around the year 2008 that it dawned on me that Redpill Linpro's
IPv4 resources would not last forever. At some point in the future we would
inevitably be prevented from expanding our infrastructure based on IPv4. It was
clear that we needed to come up with a plan on how to deal with that situation
well ahead of time. IPv6 obviously needed to be part of that plan, but exactly
how wasn't clear at all.

Conventional wisdom at the time told us that *dual stack*, i.e., running IPv4
in parallel with IPv6, was the solution. We did some pilot projects, but the
results were discouraging. In particular, these problems quickly became
apparent:

1. It would not prevent us from running out of IPv4. After all, dual stack
   requires just as many IPv4 addresses as single-stack IPv4. 
2. IPv4 would continue to become an ever more entrenched part of our
   infrastructure. Every new IPv4-using service or application would inevitably
   make a future IPv4 sunsetting project even more difficult to pull off.
3. Server and application operators simply didn't *like* running two networking
   protocols in parallel. Dual stack greatly increased complexity: it became
   necessary to duplicate service configuration, firewall rules, monitoring
   targets, and so on, just in order to support both protocols equally well.
   This duplication in turn created lots of new possibilities of things going
   wrong, reducing reliability and uptime. And when something did go wrong,
   troubleshooting the issue required more time. Single stack was therefore
   seen as superior to dual stack.

It was clear that we needed a better approach based on single-stack IPv6, but
we were unable to find an already existing one which solved all of our
problems.

One of the things that we evaluated, though, was [Stateless IP/ICMP Translation
(RFC 6145)](https://tools.ietf.org/html/rfc6145). SIIT looked promising, but it
had some significant shortcomings (which [RFC 7757's Problem Statement
section](https://tools.ietf.org/html/rfc7757#section-2) elaborates on). In its
then-current state, SIIT simply wasn't flexible enough to be up to the task we
had in mind for it. However, we *did* identify a way SIIT could be improved in
order to facilitate our IPv6-only data centre use case. This improvement is
what [RFC 7757](https://tools.ietf.org/html/rfc7757) ended up describing.

I believe the first time I presented the idea of SIIT-DC (under the working
name *«[RFC 6145](https://tools.ietf.org/html/rfc6145) ++»*) in public was at
[IIS.se](https://iis.se)'s [World IPv6 Day
seminar](https://www.iis.se/evenemang/ipv6-i-praktiken/) back in June 2011.
In case you're interested in a little bit of «history in the making», the
[slides](https://fud.no/talks/20110608-IIS.se-IPv6_From_The_Content_Perspective.pdf)
(starting at page 34) and
[video](https://fud.no/talks/20110608-IIS.se-IPv6_From_The_Content_Perspective.webm)
(starting at 34:15) from that event are still available.

A few months later we had a working proof of concept (based on
[TAYGA](http://www.litech.org/tayga/)) running. By January 2012 I had enough
confidence in it to move our corporate home page
[www.redpill-linpro.com](http://www.redpill-linpro.com) to it, where it has
remained since. I didn't ask for permission...but fortunately I didn't have to
ask for forgiveness either - to this day there have been zero complaints!

The solution turned out to work remarkably well, so in keeping with our open
source philosophy we decided to document exactly how it worked so that the
entire Internet community could benefit from it. To that end, my very first
[Internet-Draft](https://www.ietf.org/id-info/),
[draft-anderson-siit-dc-00](https://tools.ietf.org/html/draft-anderson-siit-dc-00),
was submitted to the IETF in November 2012. I must admit I greatly
underestimated the amount of work that would be necessary from that point on...

The document was eventually adopted by the [IPv6 Operations working group
(v6ops)](https://datatracker.ietf.org/wg/v6ops/) and split into three different
documents, each covering relatively independent areas of functionality. Then
began multiple cycles of peer review and feedback by the working group followed
by updates and refinements. I'd especially like to thank Fred Baker, chair of
the v6ops working group, for helping out a lot during the process. For a
newcomer like me, the IETF procedures can certainly appear rather daunting, but
thanks to Fred's guidance it went very smoothly.

One particularly significant event happened in early 2015, when Alberto Leiva
Popper from [NIC México](http://www.nicmexico.mx/) joined in the effort as a
co-author of [RFC 7757](https://tools.ietf.org/html/rfc7757)-to-be (which
describes the specifics of the updated SIIT algorithm). Alberto is the lead
developer of [Jool](http://jool.mx/), an open-source IPv4/IPv6 translator for
the Linux kernel. Thanks to his efforts, [RFC
7757](https://tools.ietf.org/html/rfc7757)-to-be (and, by extension, SIIT-DC)
was quickly implemented in Jool, which really helped move things along. The
IETF considers the availability of *running code* to be of utmost importance
when considering a proposed new Internet standard, and Jool fit the bill
perfectly.

For the record, we decommissioned our old TAYGA-based SIIT-DC BRs in favour of
new ones based on Jool as soon as we could. This was a great success - our Jool
BRs are currently handling IPv4 connectivity for hundreds of IPv6-only services
and applications, and the number is rapidly growing. We're very grateful to
Alberto and NIC México for all the great work they've done with Jool - it's an
absolutely fantastic piece of software. I encourage anyone interested in IPv6
transition to download it and try it out.

In late 2015 the documents reached [IETF
consensus](https://tools.ietf.org/html/rfc7282), after which they were sent to
the [RFC Editor](https://rfc-editor.org). They did a great job with helping
improve the language, fixing inconsistencies, pointing out unclear or ambiguous
sentences, and so on. When that was done, the only remaining thing was to
publish the documents - which, as I mentioned before, happened last week.

It feels great to have crossed the finish line with these documents, and
writing them has certainly been an very interesting exercise. It is also nice
to prove that it is possible for regular operators to provide meaningful
contributions to the IETF - you don't have to be an academic or work for one of
the big network equipment vendors. That said, it has taken considerable effort,
so I certainly look forward to being able to focus fully on my work as a
network engineer again. I promise that's going to result in more good IPv6 news
in 2016...watch this space!

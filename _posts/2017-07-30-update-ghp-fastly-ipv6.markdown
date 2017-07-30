---
published: true
title: "Update: GitHub Pages, Fastly, and IPv6"
layout: post
---

When I created this blog a couple of years ago, I [was disappointed to find
out](/2015/08/29/first-post.html) that the [GitHub
Pages](https://pages.github.com) service (_GHP_) did not support IPv6. This was
due to the fact that GHP's CDN provider [Fastly](https://www.fastly.com) didn't
support IPv6.

To work around this problem I ended up [inserting a dual-stacked HTTP proxy
service](/2015/09/07/working-around-github-pages-and-fastlys-missing-ipv6-support-using-apache-mod_proxy.html)
in front of my (IPv4-only) GHP-hosted blog. While it was  hardly ideal, it did
the trick.

The other day I was pleasantly surprised to find out that I no longer need this
hack: Fastly and GHP now do support IPv6! IPv6 appears to have been enabled for
[_https://toreanderson.github.io_](https://toreanderson.github.io) and all
other GHP sites without requiring explicit _opt-in_. Perfect!

When did it happen? Well, it seems Fastly [announced IPv6 availability on the
31st of March](https://www.fastly.com/blog/ipv6-fastly) (after having had it in
[limited beta since last
summer](https://www.fastly.com/blog/announcing-limited-availability-ipv6)).
Assuming Twitter is a reliable indicator, IPv6 was enabled for GHP specifically
sometime between the [10th of
April](https://twitter.com/ivladdalvi/status/851272956298772481) and the [28th
of May](https://twitter.com/BlurryBird/status/868673483533762560).

I was quite critical of Fastly in those old posts, so it's only fair that I
congratulate them now. Well done, Fastly - I'm very happy to see you get on the
IPv6 bandwagon!

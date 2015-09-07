---
published: false
title: Working around IPv4-only GitHub Pages/Fastly with Apache mod_proxy
layout: post
---
# The problem

As I noted in my previous post, [GitHub Pages](https://pages.github.com/) (just like [GitHub](https://github.com/) itself) does not support IPv6. This is because GitHub Pages' CDN provider, [Fastly](https://www.fastly.com/), doesn't support it:

````
$ host toreanderson.github.io
toreanderson.github.io is an alias for github.map.fastly.net.
github.map.fastly.net has address 185.31.17.133
````

I find this very disappointing. Fastly was founded in 2011, so they are a rather new CDN. Their platform is built on top of [Varnish](https://www.varnish-cache.org/), which has to the best of my knowledge supported IPv6 even before Fastly was founded, so it's not like their lack of IPv6 could be explained by having legacy and difficult to upgrade IPv4-only internal infrastructure. I find it rather ironic that a provider calling themselves ***Fast****ly* fails to support the protocol that was recently [reported by Facebook to yield about 30-40% faster time-to-last-byte web page load times than IPv4 ](https://youtu.be/An7s25FSK0U?t=18m53s). So in case anyone from Fastly is reading this, I suggest you either **a)** start supporting IPv6 ASAP, or **b)** rename your CDN product to something more appropriate, like for example *Slowly*.

# The workaround
---
published: true
title: "Working around GitHub Pages and Fastly's missing IPv6 support using Apache mod_proxy"
layout: post
---
## The problem

As I noted in my [previous post](/2015/08/29/first-post.html), [GitHub
Pages](https://pages.github.com/) (just like [GitHub](https://github.com/)
itself) does not support IPv6. This is because GitHub Pages' CDN provider,
[Fastly](https://www.fastly.com/), doesn't support it:

````console
$ host toreanderson.github.io
toreanderson.github.io is an alias for github.map.fastly.net.
github.map.fastly.net has address 185.31.17.133
````

I find this very disappointing. Fastly was founded in 2011, so they are a
rather new CDN. Their platform is built on top of
[Varnish](https://www.varnish-cache.org/), which has to the best of my
knowledge supported IPv6 even before Fastly was founded, so it's not like their
lack of IPv6 could be explained by having legacy and difficult to upgrade
IPv4-only internal infrastructure. I find it rather ironic that a provider
calling themselves ***Fast****ly* fails to support the protocol that was
recently [reported by Facebook to yield 30-40% faster time-to-last-byte web
page load times than IPv4](https://youtu.be/An7s25FSK0U?t=18m53s). So in case
anyone from Fastly is reading this, I suggest you either ***a)*** start
supporting IPv6 ASAP, or failing that, ***b)*** rename your CDN platform to
something more appropriate, like ***Slow****ly*.

## The workaround

My [employer](http://www.redpill-linpro.com/) is kind enough to provide me with
a virtual machine I can use for personal purposes. This server runs Linux,
[Ubuntu](http://ubuntu.com) Trusty LTS to be specific. It is of course
available over both IPv6 as well as IPv4 (using
[SIIT-DC](https://tools.ietf.org/html/draft-ietf-v6ops-siit-dc)), so the idea
here is to use it to provide a dual-stacked façade, thus concealing the fact
that the GitHub Pages service doesn't support IPv6.

As this server is already hosting my [sorry excuse for a home
page](http://toreanderson.no/), it had already the
[Apache](http://httpd.apache.org) web server software installed. Apache comes
with [mod_proxy](https://httpd.apache.org/docs/current/mod/mod_proxy.html),
which is perfectly suited for what I want to do.

The first order of business is to ensure the `mod_proxy` module is loaded. On
Ubuntu, this is easiest done using the `a2enmod` utility:

````console
# a2enmod proxy_http
Considering dependency proxy for proxy_http:
Enabling module proxy.
Enabling module proxy_http.
To activate the new configuration, you need to run:
  service apache2 restart
# service apache2 restart
 * Restarting web server apache2                              [ OK ]
````

The next order of business is to create a `VirtualHost` definition that uses
`mod_proxy` to forward all incoming HTTP requests to GitHub Pages. I did this
by creating a new file `/etc/apache2/sites-enabled/http_blog.fud.no.conf` with
the contents below, before reloading the configuration with the command
`apache2ctl graceful`.

````apache
<VirtualHost *:80>
	ServerName blog.toreanderson.no
	ServerAlias blog.fud.no
	ProxyPass "/" "http://toreanderson.github.io/"
	ProxyPassReverse "/" "http://toreanderson.github.io/"
</VirtualHost>
````

---
published: true
title: First post
layout: post
---
So I've finally created my own blog, and you've found it, somehow.
Congratulations! Expect only posts about technology - networking, data centres,
open source software, reports from conferences I attend, et cetera.
Essentially, various stuff I play around with both at home and at my workplace
[Redpill Linpro](http://www.redpill-linpro.com).

I guess a sensible thing to discuss in the first post is my choice of blogging
platform. There were several criteria on my wish list:

* I want my content to stay mine, and be trivially portable to another platform
  (including self-hosting) if I so choose. I am therefore rather sceptical to
  «blog in the cloud» solutions such as [Blogger](https://www.blogger.com) and
  [WordPress](https://wordpress.com/).
* I wanted to get started really quickly without spending any time doing web
  design, software installations, database setups, and so on. Installing and
  maintaining my own instance of WordPress or some other CMS - no thanks.
* I prefer to author posts in a simple (yet sufficiently powerful) markup
  language that is well suited to technical content (automatic syntax
  highlighting of quoted code, for example). The markup language should of
  course be editable with [my favourite editor](http://www.vim.org) too, so no
  binary formats!
* I would very much like to use a simple [Git](http://www.git-scm.com) repo as
  the underlying database where all the content is stored.
* I want my content to be available over IPv6. I expect to be writing quite a
  lot about IPv6, so not having the blog available over IPv6 would feel rather
  embarrassing.

So what I ended up with in the end was [GitHub
Pages](https://pages.github.com/), which automatically renders the files stored
in the [backend Git
repo](http://github.com/toreanderson/toreanderson.github.io) through
[Jekyll](http://jekyllrb.com/) to create a simple blog. In addition to that, I
used [Tinypress](https://tinypress.co) in order to bootstrap the initial
contents of the backend Git repo. Tinypress also provides a web interface where
I can create or edit posts, which I think will prove convenient from time to
time.

So far so good. The one thing that's missing, is IPv6 support. The GitHub Pages
service, or more precisely its CDN provider [Fastly](https://www.fastly.com/),
does not appear to support IPv6 yet:

    $ host toreanderson.github.io
    toreanderson.github.io is an alias for github.map.fastly.net.
    github.map.fastly.net has address 185.31.17.133

```
test1
```

```console
test2
```

Given that it's 2015, that's rather disappointing, but I'm guessing I'll find a
way to work around it for now. Most likely I'll set up some IPv6 frontend
system at work that can convert IPv6 traffic to IPv4 and pass it along to the
Fastly back-end. The details of this will most likely be the second post. Stay
tuned.

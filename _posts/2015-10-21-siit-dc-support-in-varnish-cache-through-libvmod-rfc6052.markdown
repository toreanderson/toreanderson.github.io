---
published: true
title: 'SIIT-DC support in Varnish Cache through libvmod-rfc6052'
layout: post
---

Here at [Redpill Linpro](http://www.redpill-linpro.com) we're big fans of the
[Varnish Cache](http://www.varnish-cache.org). We tend to put Varnish in front
of almost every web site that we operate for our customers, which goes a long
way toward ensuring that they respond blazingly fast - even though the
applications themselves might not always be designed with speed or scalability
in mind.

We're also big fans of [IPv6](https://en.wikipedia.org/wiki/IPv6), which we
have deployed throughout our entire network infrastructure. We've also
pioneered a technology called
[SIIT-DC](https://tools.ietf.org/html/draft-ietf-v6ops-siit-dc), which has
undergone peer review in the [IETF](https://www.ietf.org) and will likely be
published as an [RFC](https://en.wikipedia.org/wiki/Request_for_Comments) any
day now. SIIT-DC allows us to operate our data centre applications using
*exclusively* IPv6, while at the same time ensuring that they remain available
from the IPv4 Internet without any performance or functionality loss.

## A quick introduction to SIIT-DC

SIIT-DC works by embedding the 32-bit IPv4 source address of the client into an
IPv6 address. The resulting IPv6 address is located within a 96-bit
*translation prefix*. 96 + 32= 128, the number of bits of an IPv6 address. It
is easiest to explain with an example:

Assume an IPv4-only client with the address `198.51.100.42` makes an HTTP
request to a web site hosted in an IPv6-only data centre. The client's initial
IPv4 packet will be routed to the nearest SIIT-DC *Border Relay*, which will
translate the packet to IPv6. If we assume that the translation prefix in use
is `64:ff9b::/96`, the resulting IPv6 packet will have a source address of
`64:ff9b::c633:642a`. (An alternative way of representing this address is
`64:ff9b::198.51.100.42`, by the way.)

The translated IPv6 packet then gets routed through the IPv6 data centre
network until it reaches the web site's Varnish Cache. Varnish responds to it
as it would with any other native IPv6 packet. The response gets routed to the
nearest SIIT-DC Border Relay, where it gets translated back to IPv4 and finally
routed back to the IPv4-only client. There is full bi-directional connectivity
between the IPv4-only client and the IPv6-only server, allowing the HTTP
request to complete successfully.

That's the gist of it, anyway. If you'd like to learn more about SIIT-DC, you
should start out by watching the [this presentation about
it](https://ripe69.ripe.net/archives/video/186/) held at the
[RIPE69](https://ripe69.ripe.net/) conference in London last November.

## What's the problem, then?

From Varnish's point of view, the translated IPv4 client looks the same as a
native IPv6 one.  SIIT-DC hides the fact that the client is in reality using
IPv4. The implication is that the
[VCL](https://www.varnish-cache.org/docs/3.0/reference/vcl.html) variable
`client.ip` will contain the IPv6 address `64:ff9b::c633:642a`, instead of the
IPv4 address `198.51.100.42`.

If you don't use the `client.ip` variable for anything, then there's no problem
at all. If, on the other, hand you *do* use `client.ip` for something, and that
something expects to work on literal IPv4 addresses, then there's a problem.
For example, a [IP
geolocation](https://en.wikipedia.org/wiki/Geolocation_software) library is
unlikely to return anything useful when given an IPv6 address such as
`64:ff9b::c633:642a` to locate.

## The solution: libvmod-rfc6052

Even though our example `64:ff9b::c633:642a` looks nothing like an IPv4
address, it's important to realise that the original IPv4 address is still
there - it's just hidden in last 32 bits of the IPv6 address, i.e., in the
`0xc633642a` hexadecimal number.

So all we need to do is to extract those 32 bits and transform them back to a
regular IPv4 address. Doing just that is exactly the purpose of
[*libvmod-rfc6052*](https://www.varnish-cache.org/vmod/rfc6052). It is a new
[Varnish Module](https://www.varnish-cache.org/docs/trunk/reference/vmod.html)
that extends VCL with a set of functions that:

* Checks if a *Varnish sockaddr* data structure (VSA) (e.g., `client.ip`)
  contains a so-called *IPv4-embedded IPv6 address* (cf. [RFC6052 section
  2.2](http://tools.ietf.org/html/rfc6052#section-2.2)).
* Extracts the embedded IPv4 address from an IPv6 VSA, returning a new IPv4 VSA
  containing the embedded IPv4 address.
* Performs an in-place substitution of an IPv6 VSA containing an *IPv4-embedded
  IPv6 address* with a new IPv4 VSA containing the embedded IPv4 address.

The following example VCL code shows how these functions can be used to insert
an [X-Forwarded-For](https://en.wikipedia.org/wiki/X-Forwarded-For) HTTP header
into the request. The use of *libvmod-6052* ensures that the backend server
will only ever see native IPv4 and IPv6 addreses.

````
import rfc6052;

sub vcl_init {
    # Set a custom translation prefix (/96 is implied).
    # Default: 64:ff9b::/96 (see RFC6052 section 2.1).
    rfc6052.prefix("2001:db8:46::");
}

sub vcl_recv {
    ###
    ### Alternative A: use rfc6052.extract().
    ### This leaves the "client.ip" variable intact.
    ###

    if(rfc6052.is_v4embedded(client.ip)) {
        # "client.ip" contains an RFC6052 IPv4-embedded IPv6
        # address. Set XFF to the embedded IPv4 address:
        set req.http.X-Forwarded-For = rfc6052.extract(client.ip);
    } else {
        # "client.ip" contained an IPv4 address, or a native
        # (non-RFC6052) IPv6 address. No RFC6052 extraction
        # necessary, we can just set XFF directly:
        set req.http.X-Forwarded-For = client.ip;
    }

    ##############################################################

    ###
    ### Alternative B: Use replace() to change the
    ### value of "client.ip" before setting XFF.
    ###

    rfc6052.replace(client.ip);

    # If "client.ip" originally contained an IPv4-embedded
    # IPv6 address, it will now contain just the IPv4 address.
    # Otherwise, replace() did no changes, and "client.ip"
    # still contains its original value. In any case, we can
    # now be certain that "client.ip" no longer contains an
    # IPv4-embedded IPv6 address.

    set req.http.X-Forwarded-For = client.ip;
}
````

We're naturally providing *libvmod-rfc6052* as
[FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software) software in
the hope that it will be useful to the Varnish and IPv6 communities.

If you try it out, don't hesitate to share your experiences with us. Should you
stumble across any bugs or have any suggestions, head over to the
[libvmod-rfc6052 GitHub repo](https://github.com/toreanderson/libvmod-rfc6052)
and submit an issue.

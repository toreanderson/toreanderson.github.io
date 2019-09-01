---
layout: post
title: A rack switch removal ordeal
description: A rack switch removal ordeal
author: tore
---

I recently needed to remove a couple of decommissioned switches from one of our
data centres. This turned out to be quite an ordeal. The reason? The
ill-conceived way the rack mount brackets used by most data centre switches are
designed. In this post, I will use plenty of pictures to explain why that is,
and propose a simple solution on how the switch manufacturers can improve this
in future.

# Rack switch mounting 101

![Standard rack-mount
switch]({{ site.post_image }}/rack-switch-removal/switch.jpg)

The picture above displays a [Juniper EX
4600](https://www.juniper.net/us/en/products-services/switching/ex-series/ex4600/),
a pretty standard data centre switch for mounting in 19" server racks.

Pay attention to the little «ears» protruding from the sides at the front of the
switch chassis. Those are the rack mount brackets.

![Lab rack - rear view]({{ site.post_image }}/rack-switch-removal/lab-rack.jpg)

This picture shows the rack in our lab. There are seven servers and five
switches in the picture. The switches have all the exact same kind of rack mount
brackets as the switch in the first picture.

The switches are all mounted with their ports facing towards the rear of the
rack. This is typical of data centre installations, due to the fact that the
servers they connect to also have their network ports facing towards the rear of
the rack. Keeping all the network ports facing the same way minimises the amount
of cabling clutter in a rack.

Now you know how data centre switches are mounted. If you have sharp eyes, you
might already have realised what the problem is. If not, read on!

# A failed removal attempt

![Data centre rack - rear
view]({{ site.post_image }}/rack-switch-removal/dc-rack-rear.jpg)

This picture shows the rear of a rack cabinet in one of our data centres. I
needed to remove the pair of [Juniper EX
4500](https://www.juniper.net/documentation/en_US/release-independent/junos/topics/topic-map/ex4500-system-overview.html)s
in the centre. The EX 4500 is an old and end of life model, so they had to go in
order to make room for a set of new [Edge-Core
AS7326-56X](https://www.edge-core.com/productsInfo.php?cls=1&cls2=154&cls3=155&id=544)
we have arriving soon.

![Extraction blocked by
PDU]({{ site.post_image }}/rack-switch-removal/obstruction.jpg)

In case it was not obvious from the previous picture, this ought to illustrate
why I was having difficulties removing these switches from the rack. When I
tried to pull them out, they were blocked by the vertical power distribution
units occupying the space behind them.

Even if the PDUs were not in the way, network cables could also have posed a bit
of a problem. I think that I would have been able to overcome that in this
particular case, though.

Removing the power distribution units is not an option, as that would mean
powering down all the other production equipment located in the rack.

![Data centre rack - front
view]({{ site.post_image }}/rack-switch-removal/dc-rack-front.jpg)

This is the view from the front of the rack. (I had removed all the
[FRUs](https://en.wikipedia.org/wiki/Field-replaceable_unit) from the switches
in order to make them lighter and easier to handle.)

On this side, there are no power distribution units and there are very few
cables in the way. If I could have simply pulled out the switches through the
front of the rack, I would have had no difficulties whatsoever.

The problem is that those damn rack mount brackets prevents me from extracting
the switches that way. As long as the rack mount brackets remain in place, the
switches can only come out one way: through the rear of the rack.

![Attempt to unscrew rack mount
brackets]({{ site.post_image }}/rack-switch-removal/screwdriver.jpg)

The rack mount brackets are not usually part of the switch chassis itself, they
are screwed on. As there was a lot of room at the front of the rack, I was able
to attempt removing them using an angled screwdriver.

Unfortunately, the only thing I accomplished was to destroy the
[Phillips](https://en.wikipedia.org/wiki/List_of_screw_drives#Phillips) screw
head. The screws had most likely been treated with [thread-locking
fluid](https://en.wikipedia.org/wiki/Thread-locking_fluid) at the factory, and
because I had to use an angled screwdriver I was unable to get enough pressure
onto the screw to prevent the screwdriver from slipping - not to mention that I
could not really see what I was doing from where I was standing. (I suspect I
might have succeeded if the screws had been of a more slip-resistant type, e.g.,
[Torx](https://en.wikipedia.org/wiki/Torx).)

At this point, I decided to call it a day.

# Brute force to the rescue!

The next day I brought a few tools one would normally not expect to see in a
data centre.

![Pliers]({{ site.post_image }}/rack-switch-removal/pliers.jpg)

Pliers...

![Hammer]({{ site.post_image }}/rack-switch-removal/hammer.jpg)

...and a hammer. Using these, I was able to bend the rack mount bracket out of
the way.

![Success - left
side]({{ site.post_image }}/rack-switch-removal/success-left.jpg)

With the rack mount bracket on the left side flattened, I could sneak the switch
past the 19" mounting profile.

![Success - right
side]({{ site.post_image }}/rack-switch-removal/success-right.jpg)

Once the left corner of the switch was past the 19" mounting profile, it could
easily be shifted sideways to the left, allowing the right corner of the switch
to sneak past as well (without needing to damage the rack mount bracket on that
side).

![Successful extraction through front of
rack]({{ site.post_image }}/rack-switch-removal/extraction.jpg)

At this point, removing the switch through the front of the rack posed no
challenge at all.

I then repeated the process for the other switch. Success!

# Resulting damage

![Damaged rack mount
brackets]({{ site.post_image }}/rack-switch-removal/damage.jpg)

The process clearly took its toll on the rack mount brackets. They could
probably be bent back to working order, though. I also dented the chassis of one
of the switches a bit.

![Damaged screws]({{ site.post_image }}/rack-switch-removal/screws.jpg)

The screws I tried to unscrew using the angled screwdriver do not look too good
either. However, I believe I could get them out using a [screw
extractor](https://en.wikipedia.org/wiki/Screw_extractor).

Fortunately, these were old switches that we are not putting back in service, so
I do not really care about the damage.

# A plea to the switch manufacturers

To me it seems blindingly obvious that the rack mount brackets that come with
data centre switches should be designed so that they allow for extraction
through the *front* of the rack - sliding *away* from all cables, PDUs and other
obstructions in the crowded rear.

If you are a data centre switch manufacturer reading this, you do not have to
look further than to the server manufacturers for inspiration.

For as long as I have worked in the industry, I have never ever seen a
rack-mountable server that does not get installed through the front of the rack.
This makes removing it a breeze. You just disconnect all the cables, and pull it
straight out. Done!

![Server removal -
rear]({{ site.post_image }}/rack-switch-removal/server-rear.jpg)

Returning to our lab for a demonstration, it would have been impossible to
remove the server in the middle through the rear of the rack, due to all the
cables that occupy the space it would have had to pass through. But since it is
sliding *away* from the cables, there is no problem at all.

![Server removal -
front]({{ site.post_image }}/rack-switch-removal/server-front.jpg)

Out through the front of the rack it slides. Easy!

While you are at it, dear switch manufacturer, you should also note that the
servers in the pictures, like most servers on the market, have rack mount rails
that do not require any tools to be mounted. Everything just clicks into place.
There are no screws that can be dropped and lost below the raised data centre
floor. This is definitively something I would like to see network equipment
manufacturers start doing as well. (If you absolutely must include screws, at
least use Torx instead of Phillips.)

# Final words

From now on I will start discarding any standard rack mount brackets that do not
allow for installation and removal of the equipment through the front of the
rack. It is just not worth the trouble when the time comes to remove it again.

If you are in the process of acquiring new switches for your data centre, do
make sure to ask your vendors if their switches come with sensible rack mount
brackets. Perhaps if enough customers ask, they will actually improve.

In the mean time, though - if you know of any generic replacement rack mount
system that is suitable for switches, [I would love to hear from
you](https://twitter.com/toreanderson)!

*(Note: this is a [repost of an
article](https://www.redpill-linpro.com/techblog/2019/08/06/rack-switch-removal.html)
from the [Redpill Linpro techblog](https://www.redpill-linpro.com/techblog/).)*

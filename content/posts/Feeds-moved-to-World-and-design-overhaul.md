---
title: Feeds moved to World and design overhaul
date: 2019-09-11 12:03:41
tags: ["feeds", "update", "gtk", "gnome", "world", "design", "purism", "python"]
---

It finally happened, Feeds is now part of the [World](https://gitlab.gnome.org/World) group! [Here's the new repository](https://gitlab.gnome.org/World/gfeeds)! But don't worry, I'll keep up with the existing GitLab mirror.

This doesn't change too much in terms of development, but it surely makes the app more discoverable. It also provides [this very nice new URL for Feeds' website](https://world.pages.gitlab.gnome.org/gfeeds/) which is under gnome's domain name (that's pretty cool, uh?).

As for Feeds' development, lately I've begun a process of small cleanups here and there, and most importantly design changes.

![Feeds' new UI](/images/post_pics/Feeds-moved-to-World-and-design-overhaul/feeds-next-window.avif)

Thanks to the help of [Tobias Bernard](https://gitlab.gnome.org/bertob), [Alexander Mikhaylenko](https://gitlab.gnome.org/exalm) and some other awesome people from the [libhandy community](https://matrix.to/#/!nrNOrVsRZxzaDdspgs:talk.puri.sm?via=talk.puri.sm&via=matrix.org&via=librem.one), I brought Feeds back in line with the [GNOME HIG](https://developer.gnome.org/hig/stable/). These are small changes, but surely needed ones to have a more consistent desktop experience.

Some of the features I had planned, detailed in some of my previous posts in this blog, still haven't made it, tho. To be fair, some of them require a decent amount of work, and I'd be nice if someone wanted to help with implementing them.

That's why I'll try to cleanup the code more, using Glade templates where possible, splitting different classes in different files and uncluttering the logic a bit. It's probably one of the most boring things you could do, but if I want people to contribute, it has to happen.

That's it for now, stay tuned.

---
title: 'Feeds 0.8: naming change and how to deal with Flatpak'
date: 2019-09-03 16:34:14
tags: ["feeds", "update", "gtk", "gnome", "python"]
---

[Feeds version 0.8 is coming](https://flathub.org/apps/details/org.gabmus.gfeeds). This release have been a little overdue, since I finally decided to change the name of the app from `gnome-feeds` to just `gfeeds`. This has to do with the use of the GNOME name, and besides even GNOME apps don't use the *GNOME* prefix in their names.

Someone criticized the name `gfeeds`, because it's kind of dumb to have GNOME/GTK app names to start with a *g*, similarly to how dumb it is to have KDE apps start with a *k*. But here's the catch: the *g* in `gfeeds` stands for *Gabriele*, *I pulled a sneaky on you*.

Anyway, the new name should have no problems whatsoever, and besides I learned how to transition to a new appid on Flathub.

It's not as seamless as you would think, but it's easy enough. You'll have to re-submit your app as if it was a new one, and specify that it's just an appid change. There's some infrastructure in place to transition from one appid to another automagically, and it involves [a little extra json file](https://github.com/flathub/org.gabmus.gnome-feeds) in the old repo. [Here you can see my PR for the new appid](https://github.com/flathub/flathub/pull/1141) and the whole discussion around it. Hope it helps.

As for new stuff, let's see what we have:

- More stability, broken feeds are automatically removed and error messages will be shown in an upcoming version (already in `master`).
- OPML file association. Now you can just open your OPMLs from your file manager and have them imported in Feeds.
- If you're offline, feeds won't be refreshed. Instead, an infobar will tell you that you're offline. Saved articles are still accessible.
- CPU utilization is now lower, having removed some unnecessary busy waits from the code.

That's it for now. Again, still a lot of work to do, but little by little Feeds is coming together pretty well.

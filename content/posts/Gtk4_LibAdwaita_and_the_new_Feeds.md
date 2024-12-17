---
title: "Gtk4, LibAdwaita and the new Feeds"
date: 2021-11-21T11:52:10+01:00
tags:
  - whatip
  - giara
  - hydrapaper
  - feeds
  - gtk
  - gtk4
  - libadwaita
  - cpp
image: "/images/post_pics/Gtk4_LibAdwaita_and_the_new_Feeds/feeds.avif"
---

So, something big is coming for my news reader app [Feeds](https://gfeeds.gabmus.org).

Most of this year, as far as personal projects go, I spent at close contact with Gtk4, [libadwaita](https://gitlab.gnome.org/gnome/libadwaita) and the awesome people over at the various matrix chat rooms.

I've spent this time porting [What IP](https://whatip.gabmus.org), [HydraPaper](https://hydrapaper.gabmus.org) and [Giara](https://giara.gabmus.org) to Gtk4+libadwaita.

![What IP, HydraPaper and Giara at their latest iteration, side by side](/images/post_pics/Gtk4_LibAdwaita_and_the_new_Feeds/whatip_hydrapaper_giara.avif)

I'm really happy of the results, and I'm even happier with how this new stack is shaping up.

The porting process for the most part has been relatively painless across the board, with only minor changes (albeit, many of them) needed for the actual porting. Along with the updated stack, I've been able to introduce new features and improvements, for the most part thanks to the new widgets available in libadwaita making my life a lot easier.

What IP being the simpler of the bunch didn't really change much from its original iteration, except for some code cleanups. But hey, the theme is different and it looks amazing!

HydraPaper also stayed pretty much the same, with the exception of a new wallpaper folder selector (that you can see in the image above) in the form of a brand new widget, the mighty Flap! It's a sidebar that can open above other widgets, mostly a glorified overlay, but with extra bells and whistles. On a regular monitor you can open it with the usual button on the headerbar, the same that opened a Popover before, but on a touchscreen you can swipe to open it! Plus, compared to the previous Popover implementation, the new Flap is part of the main window, meaning it can scale to fill the entire vertical space of the window, making this selector/filter much easier to interact with thanks to the increased size.

Giara being one of the more complex apps, received a lot more work and attention. First off, the post views have been re-implemented using ListView, a brand new (still somewhat janky) widget in Gtk4 that allows for better optimized lists, where rows get recycled instead of adding up, with performance quickly grinding to a halt. This change required a lot of work, since my usual terrible implementation of ListBox is 100% incompatible with the way ListView works. But with a lot of help and elbow grease, I managed to land it, making the whole experience of mindlessly scrolling through reddit a little less frustrating (hopefully). Also, thanks to another awesome widget, AdwCarousel, I was able to add support for image galleries. And they support touch gestures, too! Along with these changes, I also added an internal fullscreen image viewer, very much inspired by the one in telegram-desktop.

---

But let's jump to the actual reason why I'm writing this post, _The new Feeds_.

![What the new Feeds will look like](/images/post_pics/Gtk4_LibAdwaita_and_the_new_Feeds/feeds.avif)

You see, I wanted to start porting Feeds to Gtk4 sooner than it ended up happening. Unfortunately, I wasn't able to up until recently. The reason being WebKit. WebKit support for Gtk4 has been broken for a while, and just recently (not all that recently, tho) it got to a point where it was usable enough for me to start the port. At the time of writing this, you still need to enable Gtk4 support in WebKit by using custom build flags, making development possible by building WebKit myself (which, by the way, takes _a lot_ of time), but at the same time making distribution unfeasible. Flatpak being the main way I distribute my apps, it could work in theory, but having a 1GB+ bundle for a simple news reader isn't really a great user experience, so for now I'm holding off this release, waiting for it to become available in the GNOME runtime.

But let's move on to what's actually new in Feeds.

First of all, from a user facing perspective, The whole look of the app is quite different. Apart from the new Adwaita theme in libadwaita, the article list uses the new `navigation-sidebar` style class, with cool rounded corners for the rows and no separators between them, but still providing enough visual separation. Label sizes in the rows have also been tweaked slightly.

Oh, and of course, I added article pictures! The code is based on the custom picture widget I made for Giara, and let me tell you: they make any article that more interesting!

As for visual changes, another big one is the new filter view, again based on the Flap widget.

![](/images/post_pics/Gtk4_LibAdwaita_and_the_new_Feeds/feeds_filters.avif)

It's a similar story to HydraPaper, the old Popover implementation was hard to use and unintuitive. A sidebar is a much more common pattern, plus the added vertical size makes it easier to find the feed you wanna look at.

As for the articles list, you may think ListView would be perfect here as well, and that's what I thought as well. Unfortunately due to some technical issues with how ListView row selection and activation work at this current time, while it does work, it's not really all that convenient. I added the ListView to Feeds, and you can enable it in the preferences, in the advanced section, but I kept the ListBox around as a default, and I also changed its API so that it's the same as the ListView implementation. You can switch between one or the other and try them out for yourself, hopefully at some point I can get rid of the ListBox and have the ListView be the default.

With all the nice changes and cool things this port brought with it, I decided to tackle one of the most annoying issues I've had with Feeds: the actual feed parsing.

Up until recently I've been using the very popular [feedparser](https://github.com/kurtmckee/feedparser/) python library, but I've always been dissatisfied with both its performance and its weird quirks. I came to the conclusion that it's not the kind of library I was looking for.

I tried searching for alternatives, and I broadened my search to any language, but unfortunately I couldn't find something I really liked.

So I decided to write my own! And for the best performance I wrote it in... C++! Maybe some of you were expecting Rust? Maybe one day, but I don't really know Rust right now 😕

This new library is called [Sydndication Domination](https://gitlab.com/gabmus/syndication-domination) or syndom for short (it's a bit cheesy, I know). Syndom is tailor made for Feeds, but of course it can be used for any application.

It's based around an awesome XML parser for C++ called [pugixml](https://pugixml.org/), if you ever need to parse XML I suggest you take a look at it. Syndom is able to parse an RSS or Atom file and extract all the useful information a news reader would need, and most importantly, _it_ does the heavy lifting of trying all the different places a certain information can be found in. It can also parse Opml files for importing feeds, as well as Html files for extracting other useful information from, say a blog post, like the featured image, the feed URL, the title or the description.

The whole thing comes together inside Feeds with python bindings, created using another library called [pybind11](https://github.com/pybind/pybind11/).

Honestly this has probably been the biggest change to ever come to Feeds. The performance difference is enormous, it's like night and day, and I couldn't be happier about it.

So that's all I wanted you to know, hopefully I can release Feeds sooner rather than later as I really, _really_ want people to use this new version rather than the crusty old one.

If you want to try it for yourself [you can download a recent snapshot I made manually from this link](https://cloud.disroot.org/s/2Dj94NxWn6HRAZC). Please do let me know what you think in the comments or [in the matrix room](https://matrix.to/#/#org.gabmus:matrix.org) if you prefer. And feel free to report any bugs you encounter along the way!

---
title: "Swatch: a color palette manager"
date: 2022-02-14T11:31:20+01:00
tags:
  - swatch
  - color
  - app
  - gtk
  - gtk4
  - libadwaita
  - blueprint
image: "/images/post_pics/Swatch_a_color_palette_manager/scrot0.png"
---

Between one thing and the other, I found myself in need for a palette manager: something to let me categorize different palettes and easily pick and name colors depending on their use.

I had a look at some already available options, but nothing seemed to fit the bill, so (as it's often the case) I decided to make my own.

---

I want to make a quick tangent on how easy it has become for me to quickly ~~hack together~~ build a GTK app.

A big part of the reason is reusing existing code and tooling from my other projects, as well as learning the proper way to do things.

Another big reason is the great ecosystem that's been growing around GNOME technologies recently, particularly after the release of GTK4.

First of all, libadwaita is a huge help for building any kind of application. It ships a bunch of widgets and other useful tidbits such as pre-built style classes, implementing the rules defined in the GNOME HIG. This allows for quick building, clean code and great looks out of the box.

But libadwaita has already been praised enough by me and other developers, so that's not really anything new.

The other incredibly useful tool that I'm using for my apps is [Blueprint](https://jwestman.pages.gitlab.gnome.org/blueprint-compiler/) by [James Westman](https://www.jwestman.net/). Without going into too much detail, it's a markup language for building GTK4 UIs in a clean and simple way. You then use the blueprint compiler to compile it to regular XML without ever having to look at it yourself.

---

Going back to Swatch, it's really as simple as it gets: you can create as many palettes as you want, and you can add colors to each palette. You can give custom names to everything, and you can quickly copy the color in hexadecimal (or `rgb()`) format. I also decided to give Swatch two view modes: list and grid, similar to what you'd see in a file manager.

![](/images/post_pics/Swatch_a_color_palette_manager/scrot1.png)

And with that said, this little tool does exactly what I need. Hopefully you can find it useful as well.

[The code is available on GNOME GitLab](https://gitlab.gnome.org/gabmus/swatch) as usual, and you can try it for yourself using the latest flatpak CI build.

As for a proper release, I'm planning to tag one and submit it to Flathub as soon as the GNOME 42 runtime comes out, so stay tuned for that!

---
title: "Man pages with Markdown and Pandoc"
date: 2020-08-31T19:49:06+02:00
tags: ["markdown", "pandoc", "man", "documentation", "manual", "groff"]
---

Man pages are awesome. They genuinely are. In any \*nix system you can just get documentation on your programs or even some programming things (namely a lot of C stuff, this was very useful in University) **offline** by just using a tiny simple command.

Of course writing man pages is a different thing. They are written in *groff*. I am not a fan of it, although I know people who do enjoy using it.

### Markdown ❤ Pandoc

Of course the obvious thing to do is write your docs in **Markdown**, since it's the best simple markup language out there. Fortunately, it's very easy to convert Markdown to groff, and consequently into a man page.

And of course you can just use [Pandoc](https://pandoc.org/) to do that. It's as simple and straight forward as you can imagine, it's just one command away:

```bash
pandoc --standalone --to man mymanpage.1.md -o mymanpage.1
```

It's actually not the first time I've done something like this. The first was with [the handy manuals I wrote for the Glorious GMMK keyboard and Model O mouse](https://gitlab.com/gabmus/glorious-man-pages). Most of the fancy things you'd wanna tweak with these peripherals are done with weird key combinations, and having the paper manuals or even worse PDF copies of those around isn't very practical. So of course I wrote man pages for those.

If you know Markdown, the syntax you have to use is of course very simple, but if you wanna do it right, you may want to know about a couple of dialect forms of Markdown:

### Definition lists

[Definition lists](https://pandoc.org/MANUAL.html#definition-lists) are perfect for describing particular commands or arguments in your man page, and they get rendered just as you think they would in a man page. They are not part of the original Markdown spec, but Pandoc supports them no problem.

They're really easy to use, here's an example:

```markdown
`-h, --help`

:   Shows the help message
```

The backticks are optional but they help escaping weird characters and besides they mark a section that is effectively code.

### Metadata blocks

[Metadata blocks](https://pandoc.org/MANUAL.html#metadata-blocks) are also useful to show the title of the man page. Definitely one of those small touches that you might want to have in your man page, as every proper one has them.

Again, the syntax is fairly straight forward. You want to have this at the beginning of your file and looking something like this:

```markdown
% mymanual(1) Version 0.9.99 | Example manual for this blog post
```

### Example

Finally I want to include an example Markdown man page I recently made for [HydraPaper](https://hydrapaper.gabmus.org), so that you can get a better grasp of what a full file could look like. Of course you can also find this file in HydraPaper's repository if you're interested.

```markdown
% HydraPaper(1) | General Commands Manual

NAME
====

HydraPaper - Wallpaper manager with multi monitor support

SYNOPSIS
========

`hydrapaper [-h] [-c WALLPAPER_PATH [WALLPAPER_PATH ...]] [-m WALLPAPER_MODES [WALLPAPER_MODES ...]] [-r] [-l]`

DESCRIPTION
===========

HydraPaper is a wallpaper manager, specifically designed to work around the lack of functionality of many desktop environments to set a different wallpaper for each monitor in a multi monitor setup. It accomplishes this by scaling and merging different wallpapers into a single one and setting it as spanned.

It currently supports various desktop environments, including GNOME, MATE, Cinnamon and Budgie. Experimental support is included for the sway window manager.

OPTIONS
=======

`-h, --help`

:   Show the help message and exit

`-c, --cli WALLPAPER_PATHS...`

:   Set wallpapers from the command line

`-m, --modes WALLPAPER_MODES...`

:   Specify the modes for the wallpapers (`zoom`, `center_black`, `center_blur`, `fit_black`, `fit_blur`)

`-r, --random`

:   Set wallpapers randomly

`-l, --lockscreen`

:   Set lockscreen wallpapers instead of desktop ones (for supported desktop environments)

BUGS
====

Bugs can be reported and filed at https://gitlab.gnome.org/gabmus/hydrapaper/issues

If you are not using the flatpak version of HydraPaper, or if you are using an otherwise out of date or downstream version of it, please make sure that the bug you want to report hasn't been already fixed or otherwise caused by a downstream patch.
```

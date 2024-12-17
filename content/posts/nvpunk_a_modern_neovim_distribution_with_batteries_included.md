---
title: "Nvpunk: A Modern Neovim Distribution with Batteries Included"
date: 2022-12-06T07:36:27+01:00
tags:
  - neovim
  - nvpunk
  - editor
  - vim
image: "/images/post_pics/nvpunk_a_modern_neovim_distribution_with_batteries_included/cover.jpg"
---

I've been using [neovim](https://neovim.io) for a while now, and for me, it's the single best text editor out there.

But it's got a big problem: it's a bit of a hassle, and you have to know what you're doing. There's a wide ecosystem of plugins that integrate into each other, most of them requiring custom configuration code to work. It took a while, but I managed to put together a pretty good config.

Over time this config, that was initially living in my dotfiles repo, grew larger and more complex. So came the refactorings, then the generalizations, modularization, custom settings systems...

It outgrew its role as a humble user config, it became its own thing. At some point I had to give it its own repo, and along with it, a proper name: **Nvpunk**.

![Screenshot of Nvpunk’s greeter](/images/post_pics/nvpunk_a_modern_neovim_distribution_with_batteries_included/greeter.jpg)

Yeah, that sounds about right.

---

Romantic storytelling aside, let me tell you what Nvpunk is: a complete, out of the box and ready to go neovim distribution.

In other words, it's a fully featured neovim configuration you can use without having to learn how to select, install and manage plugins, and maintain the configuration glue that holds it all together.

Some highlights of what's included:

- LSP support with completions, snippets, diagnostics and formatting
- DAP support for debugging with a familiar UI
- Advanced syntax highlighting with TreeSitter
- Interactive file tree with git support
- Code outline panel
- Fancy greeter with customizable header graphics
- Theme chooser with a selection of popular preconfigured themes
- Categorized right click menu to access some useful features

I've been heavily inspired by other projects like [LunarVim](https://www.lunarvim.org/), [NvChad](https://nvchad.com/) and [AstroNvim](https://astronvim.github.io/), with the difference that Nvpunk tries to offer a ready to go experience, while these distros give you more of a base to build on yourself, still requiring manual configuration to be effective.

Nvpunk still offers some degree of custom configuration, but it's more tailored towards advanced users that know what they're doing, and it's very much optional.

Most of the things you may want to change are configurable via a UI preferences menu, accessible from the greeter.

I understand that Nvpunk may not be for everyone: some may call it bloated, and typically (neo)vim fans like to roll their own config anyway (just like I did), but that's fine. Nvpunk is mostly targeted at people that are looking for a good, feature-rich text editor, but that don't have the time, interest or knowledge to maintain their own config.

You can find out more on [Nvpunk's website](https://nvpunk.gabmus.org) and on the [GitLab repository](https://gitlab.com/gabmus/nvpunk).

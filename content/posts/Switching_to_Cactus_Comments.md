---
title: "Switching to Cactus Comments"
date: 2021-06-16T10:24:30+02:00
tags:
  - comments
  - meta
  - blog
image: "/images/post_pics/Switching_to_Cactus_Comments/cactus.avif"
---

I've been using Commento for a while for comments (without much adoption to be honest), and while I really love the project, and the simplicity of its approach, there's always been one unavoidable thing I never really liked too much: if you want to comment, you have to register to my commento instance.

That's less than ideal, considering that if I did have lots of people who wanted to comment, that'd have been quite a strain on my humble home server, where my commento instance is hosted.

Today I found [this post](https://karmanyaah.malhotra.cc/tech/2021/06/website-things/) on Mastodon (feel free to [follow me there](https://linuxrocks.online/@gabmus) by the way) by Karmanyaah Malhotra about how he reworked his website recently. I always enjoy reading about other people's websites, so I quickly read through it, just to find out about this comment system called [Cactus Comments](https://cactus.chat).

Cactus Comments uses [Matrix](https://matrix.org/) to create a federated comment section on your website. If you have a Matrix account on any instance, you can comment on Cactus! Heck you can even use a full-fledged Matrix client to do so! That's just great.

It's free to use and self hostable as well, so there's really not much of an excuse not to try it out. Plus if you already have a Matrix account you're already halfway there.

I integrated it in my [Ficurinia Hugo theme](https://gitlab.com/gabmus/hugo-ficurinia) without much effort, so there's another bonus point.

And that's it, I'm really happy about this new comment system, it's one less service running on my home server and due to its decentralized and federated nature it will hopefully encourage more people to leave interesting comments and get a conversation going!

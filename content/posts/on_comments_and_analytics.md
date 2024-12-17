---
title: On privacy conscious comments and analytics
date: 2019-08-14 19:40:00
tags: ["blog", "meta", "comments", "commento", "analytics"]
---

This blog is fairly new, and so I still have to set up everything. Comments are obviously a very important part of a blog, especially if you want any kind of feedback on the stuff you're doing.

The lazy solution is pretty obvious: [*Disqus* the privacy nightmare](https://en.wikipedia.org/wiki/Disqus#Criticism,_privacy,_and_security_concerns).

I like the wild idea of respecting my readers' privacy, and so I started looking for alternative solutions.

The one solution I chose to adopt is an open source comment platform called [**Commento**](https://commento.io/). It works just like Disqus, except since it doesn't make money from violating people's privacy, you can't really use it for free.

You can either choose to pay what you want for it (with a minimum of 3 USD) or host it yourself.

I already pay for a VPS for [Tech Pills' website](https://techpills.technology), but it's not subject to heavy load, so a service more running on it wouldn't really hurt.

You can install Commento using `docker-compose`, which is very convenient. I made some changes to the `docker-compose.yml` file to better suit my needs, and put an nginx reverse proxy in front of it to add https into the mix.

After writing some CSS to adapt it to the blog's theme, the new comment box is ready to roll.

As a nice (unexpected) bonus, Commento also provides some basic analytics like total page views and number of comments. And that's all totally anonymous, it's just a faceless number that's not associated to any identity.

And that's about it, if you're looking for a privacy respecting, open source, powerful comment platform, that's also pretty easy to install and offers some bonus analytics on top, Commento is the way to go.

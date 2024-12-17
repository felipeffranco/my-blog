---
title: 'Feeds: saving articles offline and marking stuff as read'
date: 2019-08-20 10:26:53
tags: ["feeds", "gnome", "gtk", "update"]
---

## Recap

[In a previous post](https://gabmus.gitlab.io/2019/08/12/feeds-0-6-and-roadmap-md/) I layed out a roadmap for my feed reader application [**Feeds**](https://flathub.org/apps/details/org.gabmus.gnome-feeds). Here's a little update on how things are going.

Alright, first I want to thank [@Etamuk](https://gitlab.com/etamuk) again. He's being very active with the German translation, and any time I make a commit, soon after I already see he has opened a merge request with the updated translation. That's just impressive, and I'm truly greatful for his awesome contributions!

But now let's get down to business with the new features.

## Saving articles

![](/images/post_pics/Feeds-saving-articles-offline-and-marking-stuff-as-read/save_article.avif)

Every article now has a right click or longpress popover that contains two buttons. The first one is a *Save* button. You can use it to save or ~~unsave~~ delete an article.

Once an article is saved, its content is downloaded and available offline. This allows easy consultation whenever you want and also provides an alternative to browser bookmarks or online "read it later" services.

## Read and unread

![](/images/post_pics/Feeds-saving-articles-offline-and-marking-stuff-as-read/read_unread.avif)

A feature that I should have implemented from the beginning made its way into Feeds just now. Articles you read, are marked as read.

The marking was made in the laziest way possible: it just changes the article opacity to 50%. But in my opinion it works alright. Of course you can manually set an article as read or unread by using the dedicated button in the right click or longpress popover.

## Conclusions

Maybe these sound like easy things to implement, but to be honest due to the structure of the application, and to the way things work to minimize disk usage and make the whole thing more dynamic and faster overall, to implement these features I had to touch up almost every little piece of the application.

It was worth it in the end, and I'm happy of what I accomplished. Hope you like it too when it comes out. I'm thinking of doing a 0.7 release. I don't have the feeling that Feeds is worth a 1.0 at the moment. Maybe I need someone to test it out and see how things are working.

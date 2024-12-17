---
title: Feeds 0.6 and future roadmap
date: 2019-08-12 09:51:13
tags: ["feeds", "roadmap", "update", "gtk", "gnome", "python"]
image: "https://gitlab.com/gabmus/gnome-feeds/raw/0.6/data/icons/org.gabmus.gnome-feeds.svg"
---

[Feeds version 0.6 is out](https://flathub.org/apps/details/org.gabmus.gnome-feeds) with two important changes:

- German translation thanks to [@Etamuk](https://gitlab.com/etamuk)
- Concurrent feeds refresh

About the translation, I know when a project starts becoming interesting, people want to contribute. And translations are in my experience one of the indication of people really getting passionate.

I am really flattered by this contribution and it really means a lot to see this project getting some love.

Moving on to the more technical stuff, concurrent refresh or parallel refresh.

It was surprisingly easy to implement, and that really shows how important having a modular and well-thought-out code structure is.

You see, the feed download and object creation (along with the creation of all the feed items) happens asynchronously already, but adding that feed, and all of its articles to the active lists doesn't. There is this wonderful function `GLib.idle_add(function, *args)` that makes this whole process so easy and elegant. This function does no more than telling the main thread to run `function(*args)` whenever it can, no rush (although to a human it's almost instantaneous). With this method the GUI thread doesn't get interrupted and the UI doesn't freeze, but the function is still ran synchronously to it, avoiding any problems of mutual exclusion to precious variables.

```python
# https://gitlab.com/gabmus/gnome-feeds/blob/0.6/gfeeds/feeds_manager.py#L139
class FeedsManager(metaclass=Singleton):
    # ...
    def refresh(self, *args):
        # ...
        threads_pool = []
        threads_alive = []
        MAX_THREADS = self.confman.conf['max_refresh_threads']
        for f_link in self.confman.conf['feeds']:
            t = threading.Thread(
                group = None,
                target = self._add_feed_async_worker,
                name = None,
                args = (f_link, True)
            )
            threads_pool.append(t)
        while len(threads_pool) > 0:
            if len(threads_alive) < MAX_THREADS:
                t = threads_pool.pop(0)
                t.start()
                threads_alive.append(t)
            for i, t in enumerate(threads_alive):
                if not t.is_alive():
                    threads_alive.pop(i)
            while t.is_alive():
                while Gtk.events_pending():
                    Gtk.main_iteration()
```

This is the wonderful code that manages the parallel refresh. It's fairly straight forward, it keeps a pool of threads to run, and runs n of them at a time, where n is the maximum number of threads set by the user (defaults to a very mild 2).

Apart from making the refresh process faster by itself, it also reduces the impact of problems like one feed taking longer than the others to download or parse. It's not a likely occurrence, but if and when it happens, it doesn't injure the user experience too much. That's because the slow feed is being processed in its own thread, but the whole refresh operation doesn't have to wait on it.

Moving on, I wanted to lay out a roadmap of where I want Feeds to go in the future.

- **Read and unread articles**: currently Feeds doesn't keep track of your actions, and if you did or didn't read an article. This would require some strcuture, like the serialization and deserialization of articles to a skinnier, more targeted data structure (probably something like tsv or json, but the latter is more likely)
- **Keeping history of older articles locally**: rss feeds typically don't contain the whole archive, mostly because the resulting file would be huge and unwieldy. But people may want to be able to re-read articles they saw some time prior. That's why there needs to be an option to store them up to the user's preference. Again, this needs serialization/deserialization as described in the bullet point above
- **Adblocking**: this speaks for itself. Modern websites are a mess, disabling JavaScript helps, but it's neither a soltion nor a silver bullet
- **Saving articles**: kinda like bookmarks, but not quite. Let's say you add this awesome blog to your feeds, and you read a super interesting article about how to make feeds refreshing concurrent with just a bunch of lines of code. You surely want to save that! I mean, it could be useful someday right? That's a likely usecase for me. Also, you'd wanna keep it offline as well, just in case you need it when you're on a plane or something
- **Suggestions**: many online news readers give you suggestions on what to follow. It's an interesing feature and I'd want to add that. People are so addicted and accustomed to social media websites that this is what they expect news consumption to be.
- **Integration with GNOME Online Accounts**: mostly to be able to sync with services like Nextcloud News. Would be a hell of a feature.

And that's it folks. This is the list of what I'd want to accomplish with Feeds. Not all of these features are easy, and some are prone to make the whole app slower, and that's one thing I 100% want to avoid.

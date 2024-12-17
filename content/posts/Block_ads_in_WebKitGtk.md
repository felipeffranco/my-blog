---
title: "Block ads in WebKitGtk"
date: 2021-11-27T19:08:53+01:00
tags:
  - gtk
  - webkit
  - webview
  - webkitgtk
  - webkit2gtk
  - adblock
  - tutorial
  - python
image: "/images/post_pics/Block_ads_in_WebKitGtk/block_ads_webkit.avif"
---

One of the most requested features (and for a good reason) in Feeds has always been ad blocking.

I never came around to implement it, mostly because I couldn't find a guide or any similar resource, except for [GNOME Web's source code](https://gitlab.gnome.org/GNOME/Epiphany), and that's not exactly easy to navigate. At least, it isn't for me.

Today I decided to come back to it, and I found that it's actually a rather easy task! That's why I wanted to document this process, in hope that someone might find it useful one day.

First off, you'll need one or more **blocklists**, in JSON format. I used [EasyList](https://easylist.to/), in particular [here's the link to the JSON block list](https://easylist-downloads.adblockplus.org/easylist_min_content_blocker.json). Blocklists should be updated frequently, so it's advisable to have some sort of mechanism to download and update them at runtime automatically.

Let's now jump to the code, which will be Python in this case, but hopefully can be easily adapted to other languages.

I'll use some closures for convenience.

```python3
# have a function that downloads text
def download_text(url: str) -> str:
    # ...

# create the filter store somewhere in your code, ideally you should
# only have one in your application
my_filter_store = WebKit2.UserContentFilterStore.new('some/cache/path')

# a name that will be used to save/retrieve blocklists from the store
BLOCKLIST_ID = 'blocklist'

def apply_adblock(
        webview: WebKit2.WebView,
        filter_store: WebKit2.UserContentFilterStore,
        blocklist_url: str
) -> None:
    # 0: Retrieve the WebKit2.UserContentManager from the WebView
    content_manager = webview.get_user_content_manager()

    def save_blocklist_cb(caller, res, *args):
        # 6: Once saving is done we can retrieve the newly created
        #    WebKit2.UserContentFilter with save_finish and finally add th
        #    filter to the WebKit2.UserContentManager
        try:
            filter = filter_store.save_finish(res)
            content_manager.add_filter(filter)
        except GLib.Error:
            print('Error saving blocklist')

    def download_blocklist_cb(blocklist: str):
        # 5: Now that we have the blocklist (as a string containing a json),
        #    we'll need to convert it to GLib.Bytes, then save it in the store.
        #    Yes, we need to save it even if we want to refresh it every time,
        #    this is just the way this works.
        #    This is also async and when it's done it will call continue on to
        #    save_blocklist_cb
        filter_store.save(
            BLOCKLIST_ID, GLib.Bytes.new(blocklist.encode()), None,
            save_blocklist_cb
        )

    def download_blocklist():
        # 4: Download the blocklist and continue on to download_blocklist_cb
        try:
            res = download_text(blocklist_url)
            GLib.idle_add(download_blocklist_cb, res)
        except:
            print('Error downloading the blocklist')

    def filter_load_cb(caller, res, *args):
        # 2: load_finish will either succeed, and return a
        #    WebKit2.UserContentFilter object or raise a GLib.Error in case
        #    it cannot find the blocklist in the store (ie: on the first run)
        try:
            filter = filter_store.load_finish(res)
            content_manager.add_filter(filter)
        except GLib.Error:
            # 3: If loading the blocklist from the store fails, download the
            #    blocklist (async to avoid blocking of course) and save it
            print('blocklist store not found, downloading...')
            Thread(target=download_blocklist, daemon=True).start()

    # 1: try to load the blocklist from the store, this is async and will call
    #    filter_load_cb when it's done
    filter_store.load(BLOCKLIST_ID, None, filter_load_cb, None)
```

I decided to comment the example code above instead of writing disconnected descriptions here, hopefully it makes sense.

This said, if you want to look at the actual code that I'm using in Feeds, [you can find it here](https://gitlab.gnome.org/World/gfeeds/-/blob/0a3d3da66d7d4ede155db24fb0f83f03b47f5489/gfeeds/webview.py#L78) (this is a snapshot to today's current latest commit, in the future you may want to look at newer code). It contains some logic to automatically refresh or even remove the blocklist.

Let me know what you think in the comments, and feel free to point out any errors, or if something is unclear.

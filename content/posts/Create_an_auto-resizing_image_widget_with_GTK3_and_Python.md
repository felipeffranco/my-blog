---
title: "Create an auto resizing image widget with GTK3 and Python"
tags:
  - python
  - gtk
  - gtk3
  - cairo
  - drawing
  - area
  - gtkdrawingarea
  - pixbuf
date: 2020-10-03T10:23:54+02:00
---

One of the most common things you might wanna do with images, that is making them automatically resize to adapt to the available space they have, is not possible with `Gtk.Image`.

{{< video "/images/post_pics/Create_an_auto_resizing_image_widget_with_GTK3_and_Python/adaptive_picture_demo.mp4">}}

So after some hacking, I seem to have found a solution that works:

```python
from gi.repository import Gtk, GdkPixbuf, Gdk


class PictureView(Gtk.DrawingArea):
    def __init__(self, path, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.path = path
        self.pixbuf = GdkPixbuf.Pixbuf.new_from_file(self.path)
        self.img_surface = Gdk.cairo_surface_create_from_pixbuf(
            self.pixbuf, 1, None
        )

    def get_useful_height(self):
        aw = self.get_allocated_width()
        pw = self.pixbuf.get_width()
        ph = self.pixbuf.get_height()
        return aw/pw * ph

    def get_scale_factor(self):
        return self.get_allocated_width() / self.pixbuf.get_width()

    def do_draw(self, context):
        sf = self.get_scale_factor()
        context.scale(sf, sf)
        context.set_source_surface(self.img_surface, 0, 0)
        context.paint()
        height = self.get_useful_height(self.get_allocated_width())
        self.set_size_request(-1, height)
```

This code is actually simpler than I imagined, and that's a good thing. Let's see what's going on here.

I'm doing all of this in a self contained class I unoriginally called *PictureView*, of course you can rename it to your liking. It takes a path to a picture as an argument.

I'm subclassing `Gtk.DrawingArea` to be able to draw arbitrary stuff with cairo.

The first thing is creating a base `GdkPixbuf.Pixbuf` to store the original image, create a cairo surface from that image and then tell the widget what to do inside the `do_draw` method.

This will in turn find out the size at which it wants to be rendered.

For the width, I want all of it, or in other words all of the allocated width that the widget has.

As for the height, I simply calculate the target height (indicated as `allocated_height` in the formula below) by solving a simple proportion between the picture width and the allocated width:

```
pixbuf_width / allocated_width = pixbuf_height / allocated_height
```

Once I have this useful data, I set the widget size request with the new values I found (I leave the width as -1 so that it doesn't change).

Then, I scale the whole cairo context with the `scale` method, calculating the scale factor as `widget_width / pixbuf_width`, and finally set the cairo context source to the image surface I created in the constructor.

Oh, and don't forget to call `cairo.paint()` as well.

All in all this is pretty straight forward, but you need to know what you're doing, and I certainly did not up to about a couple of hour ago.

Hopefully if you need to do this, you will find this blog post and avoid wasting two hours of your time trying to figure this out from sparse C code floating around the internet.

---

As a final note, I'm being told that in GTK4 GtkPicture is able to do this, but since I'm still stuck with GTK3 (as you probably are as well if you're reading this), this is the best solution I could find.

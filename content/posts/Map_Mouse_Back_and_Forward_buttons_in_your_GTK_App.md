---
title: "Map Mouse Back and Forward Buttons in Your GTK App"
date: 2020-10-08T11:39:57+02:00
tags:
  - gtk
  - gtk3
  - python
  - mouse
  - button
  - eventhandler
---

The back and forward buttons on your mouse are incredibly useful, at least for me. I use them all the time!

I couldn't find a proper way to map it on my app until I asked on the GTK Matrix chatroom, there [baedert](https://github.com/baedert) sent me a couple of links to how he did it in his app corebird.

I translated the code to Python and decided to make a blog post to archive the procedure.

First, here's a function that creates the "gesture" and assigns it to a widget and a callback function

```python
def add_mouse_button_accel(widget, function):
    gesture = Gtk.GestureMultiPress.new(widget)
    gesture.set_button(0)
    gesture.set_propagation_phase(Gtk.PropagationPhase.CAPTURE)
    gesture.connect('pressed', function)
    return gesture
```

As you may have noticed, this function returns the created gesture. You may think there is no need for it and ideally you'd be right.

There's just a small issue: due to something that I could only identify as a bug in GTK, you need to keep the `Gtk.GestureMultiPress` object around, otherwise it gets destroyed and the button clicks are no longer registered.

Once the gesture is returned, you can assign it to something that stays in memory (or you can modify this function and do something like `widget.mouse_accel_gesture = gesture` and be done with it).

As for the function associated with the mouse click, here's a template you can use:

```python
def on_mouse_event(gesture, n_press, x, y):
    btn = gesture.get_current_button()
    if btn == 8:
        # Handle the Back button here
    elif btn == 9:
        # Handle the Forward button here
```

There you go, hope it helps. If you have any trouble feel free to leave a comment or contact me (contact info can be found [in the about page](/pages/about))

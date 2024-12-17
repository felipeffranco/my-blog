---
title: "Raspberry Pi Pico pinout in your terminal"
date: 2021-03-09T13:14:27+01:00
tags:
  - raspberrypi pico
  - electronics
  - terminal
image: "/images/post_pics/Raspberry_Pi_Pico_pinout_in_your_terminal/screenshot.avif"
---

I'm playing around with the new [Raspberry Pi Pico](https://www.raspberrypi.org/products/raspberry-pi-pico), and I quickly realized that I'd really love to have a quick pinout reference in my terminal.

I thought of making a man page, but I would have lost the color (I don't know how to use arbitrary colors in groff), so I just created a simple text file with ascii escape sequences for colors and called it a day. It works, and it's pretty!

I'm uploading it here for your convenience (and mine as well), you should be able to grab it from your \*NIX box by just typing:

```
curl https://gabmus.org/pico_pinout
```

If you want to save it in a file, just add `> some_file_name` to the above.

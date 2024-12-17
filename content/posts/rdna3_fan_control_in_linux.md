---
title: "RDNA3 Fan Control in Linux - Why are my GPU fans not spinning under load?"
date: 2024-01-20T08:42:01+01:00
tags:
  - amdgpu
  - gpu
  - linux
  - radeon
---

I've recently upgraded my GPU from a [5700XT](/posts/early-adopter-experience-with-the-new-radeon-rx-5700-xt-on-arch-linux/) to a 7800XT, and of course using a new GPU close to launch on Linux would reveal some paper cuts.

Turns out my GPU fans weren't spinning, even under load. It helps that it's winter and I have some very effective case fans, but it didn't really make sense that at 88°C junction temperature the fans wouldn't spin.

First, you might think of reaching for [CoreCtrl](https://gitlab.com/corectrl/corectrl) to tweak the fan curve, but you'll be disappointed to find no fan control settings for your new GPU.

Turns out RDNA3 GPUs, unlike previous Radeon cards, don't have manual fan control at the firmware level. What they do have is manual fan curve control.

The funny thing is that for whatever reason the default fan curve (at least for my particular card) looks like this:

```
$ cat /sys/class/drm/card1/device/gpu_od/fan_ctrl/fan_curve
OD_FAN_CURVE:
0: 0C 0%
1: 0C 0%
2: 0C 0%
3: 0C 0%
4: 0C 0%
OD_RANGE:
FAN_CURVE(hotspot temp): 25C 100C
FAN_CURVE(fan speed): 15% 100%
```

Basically 0% at any temperature, which shouldn't really even be allowed if you look at the valid ranges just below.

After scouring the internet I managed to find how to actually tweak these values, and I made a simple script to set my custom fan curve. The syntax is `node_index temperature fan_speed_percent`, and finally `c` as in "commit" to confirm the settings.

```bash
#!/bin/bash

if [[ "$USER" != "root" ]]; then
    echo "You need to run this as root"
    exit 1
fi

# you might need to adjust this, in my case it's card1,
# for you it might be card0 or something else entirely
cd /sys/class/drm/card1/device/gpu_od/fan_ctrl

echo "0 30 20" > fan_curve
echo "1 50 25" > fan_curve
echo "2 60 50" > fan_curve
echo "3 70 60" > fan_curve
echo "4 80 100" > fan_curve
echo "c" > fan_curve
```

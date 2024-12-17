---
title: Early adopter experience with the new Radeon RX 5700XT on Arch Linux
tags:
  - gpu
  - linux
  - arch
  - radeon
  - amdgpu
  - mesa
  - vulkan
date: 2019-08-31 09:04:26
image: "/images/post_pics/Early-adopter-experience-with-the-new-Radeon-RX-5700-XT-on-Arch-Linux/navi.avif"
---

So I recently got myself a shiny new AMD Radeon RX 5700 XT GPU (that's a mouthful).

Let's see how it does on Linux.

## Installation

First, I use Arch (btw) and that makes things a lot easier, at least for now. As of writing this, Navi support is still not in the mainline kernel, but it should come with Linux 5.3.

Because of the AUR and many other fun things, this isn't much of a problem in Arch.

Don't be surprised when you install the GPU in your PC and don't see Xorg or anything resembling a GUI popping up.

First, you need to add a new repo to your system. Put the following in `/etc/pacman.conf`, just above the `[core]` repository:

```
[mesa-git]
Server = https://pkgbuild.com/~lcarlier/$repo/$arch
SigLevel = Optional
```

Now, let's install some packages:

```bash
sudo pacman -Sy amdvlk clang-git libclc-git libdrm-git llvm-git llvm-libs-git mesa-git opencl-mesa-git vulkan-mesa-layer-git vulkan-radeon-git xf86-video-amdgpu-git lib32-mesa-git lib32-mesa-git lib32-vulkan-radeon-git lib32-vulkan-mesa-layer-git
```

This will cover most of the stuff needed to make the GPU work correctly.

Now we need the new kernel modules. For this, you have two routes: install `amdgpu-dkms` from the AUR, **or** install `linux-git` from the AUR. I personally suggest you to go with `linux-git` as the dkms driver is not official as far as I know and doesn't cover all the stuff that's needed to make the GPU work 100% (like sensors support).

Whatever route you choose, there's still one more thing you need to install from the AUR: the `linux-firmware-agd5f-radeon-navi10` package. This is a package I made myself containing [the latest firmware for Navi 10 GPUs right from the driver developer](https://people.freedesktop.org/~agd5f/radeon_ucode/navi10/).

So, to summarize, here's a copy-paste command you can run:

```bash
yay -S linux-git linux-firmware-agd5f-radeon-navi10
```

Quick note, I tried installing `mesa-aco-git`, even just to give it a try, but unfortunately I wasn't able to get Xorg (or Wayland for that matter) running with it. Maybe in the future, we'll see.

## Gaming performance

First, all of the games I'm gonna talk about were ran at 1440p. They were all tested in GNOME Wayland, and the ones showing problems were also tested in GNOME Xorg and XFCE.

So, performance is of course great, especially in native games. Here's a list of games I maxed out without any hiccup or visible problem:

- **Rocket League**
- **Dark Souls 3**
- **Road Redemption**
- **CS:GO**
- **DOOM 2016**
- **The Witcher 3**
- **Fallout 4** (except for a mouse problem unrelated to the GPU)
- **Tabletop Simulator**
- **For The King**
- **Keep Talking and Nobody Explodes**
- **Xonotic**

Small note on The Witcher 3: it runs pretty much flawlessly, but I experienced some occasional frame rate drops, especially early on when I loaded the game. This usually goes away after 10 minutes or so of gameplay.

I had various problems with other games, and pretty weird ones, too. I think they're worth documenting.

### IMPORTANT UPDATE

The games that are crossed out below don't actually have anything wrong with them. I am just dumb enough to have used the **non git versions of the mesa 32 bit libraries**. I updated the install command above, just to make sure nobody gets the same problems.

- ~~**Distance**: the game launches, but performance is terrible (2 fps in the menu at 1440p). Obviously there's something wrong, as the game is far from being _that_ heavy.~~
- ~~**Screencheat**: about 7 fps in game, 12 or so in the menu. Very similar to the problem with Distance.~~
- ~~**Hover: Revolt of Gamers**: As above, performance is unexplainably terrible even in the main menu.~~
- **Rise of the Tomb Raider**: game seems to work fine, launches and the menu is perfectly responsive, but 5 seconds or so after I get in game, the whole game freezes into an unrecoverable state.
- **Quake Champions**: Similar to Rise of the Tomb Raider, menu works perfectly, almost instant freeze as soon as I get in game. But Quake Champions is far from being a technically well engineered game.
- **Enter the Gungeon**: Perfectly playable, but some of the graphics show some small amount of glitches.
- **No Man's Sky**: Games works almost perfectly, but the framerate drops heavily inside menus.

~~An interesting fact: in the games I've tested, where the framerate is unexplainably low, `sensors` shows the power draw of the GPU to be stuck at 30W (same as idle). Other games that give no problems like The Witcher 3 manage to raise the power draw between 150W and 300W. I later confirmed that (of course) the clock speeds (monitored via `sudo watch cat /sys/kernel/debug/dri/0/amdgpu_pm_info`) are directly proportional to the power draw.~~

~~This probably shows something's wrong with the driver. It made me wonder if I could force a clock speed on the GPU manually to see if those games work fine. I didn't want to go that route since it would kinda count as an overclock and I'm still not confident enough in my abilities or in the driver's.~~

What I did try to do was force the higher pstates (power states) on the GPU to see if that would work. Of course, it doesn't. Following the [AMDGPU page on the Arch Wiki](https://wiki.archlinux.org/index.php/AMDGPU#Overclocking) I tried these commands:

```bash
sudo -i # better be root for this
echo "manual" > /sys/class/drm/card0/device/power_dpm_force_performance_level
echo "2" > /sys/class/drm/card0/device/pp_dpm_mclk # set highest pstate 2 on the memory
echo "5 6 7" > /sys/class/drm/card0/device/pp_dpm_sclk # set higher pstates 5, 6 and 7 on the actual gpu
```

This didn't work at all, and showed some rather interesting errors in dmesg, similar to this one: `Failed to send message 0x20, response 0xfffffffb param 0x5`. Of course searching for these messages gave me zero results.

**About OpenCL**: Unsurprisingly Clover doesn't work, ~~but so doesn't the AMDGPU-PRO OpenCL implementation. Still need to investigate that further, I didn't get into much detail with OpenCL. Will do soon after I have these more serious problems sorted out.~~ OpenCL now works using the `opencl-amdgpu-pro-pal` package from AUR.

## Conclusions

It's not an entirely bad thing to be an early adopter. I mean, I am starting to understand how the driver works (kinda?) and digging into lower level stuff with the kernel and the GPU than I've ever had.

This said, there's still much I don't understand. I am willing to learn and help. If you have more experience with this stuff and want to add your voice to the conversation, don't hesitate to comment or contact me personally.

Will keep this blog updated with any news regarding making this GPU work.

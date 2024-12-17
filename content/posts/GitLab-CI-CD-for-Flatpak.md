---
title: GitLab CI/CD for Flatpak
date: 2019-08-31 12:24:16
tags: ["gitlab", "ci", "cd", "flatpak", "gnome", "yml", "tutorial"]
---

Thanks to [this document](https://gitlab.gnome.org/GNOME/Initiatives/wikis/DevOps-with-Flatpak), and a quick look at [nautilus's git repo](https://gitlab.gnome.org/GNOME/nautilus/tree/master) I was able to automatically generate fresh flatpaks directly from GitLab by using the integrated CI/CD system.

My current setup isn't perfect but it manages to automate flatpak builds. This is incredibly useful, as when people open issues and I try to fix them, I can just tell them to install the lastest flatpak from the CI/CD and test the fixes.

Also, for power users and curious minds I imagine it could be compelling to be able to install the latest development snapshot without jumping through many hoops.

Anyway, let's get down to business, here's the `.gitlab-ci.yml` file to let the magic happen:

```yaml
include: 'https://gitlab.gnome.org/GNOME/citemplates/raw/master/flatpak/flatpak_ci_initiative.yml'

variables:
    BUNDLE: "gfeeds.flatpak"

flatpak:
    image: 'registry.gitlab.gnome.org/gnome/gnome-runtime-images/gnome:3.32'
    variables:
        MANIFEST_PATH: "dist/flatpak/org.gabmus.gfeeds.json"
        MESON_ARGS: "-Dprofile=Devel"
        FLATPAK_MODULE: "gfeeds"
        RUNTIME_REPO: "https://flathub.org/repo/flathub.flatpakrepo"
        APP_ID: "org.gabmus.gfeeds"
    extends: .flatpak

review:
    stage: deploy
    dependencies:
        - 'flatpak'
    extends: '.review'

stop_review:
    stage: deploy
    extends: '.stop_review'
```

Hope you find a good use for it.

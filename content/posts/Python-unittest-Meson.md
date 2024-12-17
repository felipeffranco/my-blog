---
title: Python unittest + Meson
date: 2020-05-11 11:27:41
tags: ["python", "unittest", "unit", "test", "testing", "meson", "gtk", "ninja"]
---

Alright, I started writing some unit tests for [Feeds](https://gabmus.gitlab.io/gnome-feeds) and I wanted to integrate them into meson, so that I can just run `meson test` or `ninja test` during build and have them run automagically.

As an added bonus, they *should* also run when creating a flatpak package, so that I don't accidentally push a broken built to users.

Here's the very simplistic way I accomplished this task:

- have your tests in a `tests` directory in the root of your project
- add this to your `meson.build`

```meson
envdata = environment()
python_paths = [join_paths(meson.current_build_dir(), '..')]
envdata.append('PYTHONPATH', python_paths)
envdata.append('TESTS_BUILDDIR', meson.current_build_dir())
test(
  'unit-tests',
  import('python').find_installation('python3'),
  args: ['-m', 'unittest', 'tests'],
  env: envdata
)
```

While this works locally, I'm still having problems with running them during the flatpak build in GitLab CI, so I'll try to solve that as well and update this post accordingly.

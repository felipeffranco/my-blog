---
title: "Making a D-Bus Daemon with Python and Flatpak"
date: 2021-01-02T09:42:43+01:00
tags:
  - gtk
  - gnome
  - dbus
  - python
  - hydrapaper
  - meson
  - flatpak
  - tutorial
---

*I learned most of this stuff from [this awesome series of articles by Larry Price](https://larry-price.com/blog/categories/dbus/). Give it a read, it's worth it.*

## Why

D-Bus daemons are quite useful tools. What would you need them for? Well, I decided to add one to [HydraPaper](https://hydrapaper.gabmus.org) to do two jobs:

1. change the wallpaper periodically
2. listen for monitor configuration changes and adapt to it, so that when you plug or unplug a monitor, the wallpaper doesn't get all messed up

There could be other reasons, like provide an always active "server" component to your application, or if you need to send notifications to the user even when your main app isn't running.

Of course possibilities are limitless, it's just another way to create software. **Using D-Bus to create this daemon allows your graphical application to communicate with the main application in an easy and predictable way**.

If you want to read more about D-Bus, I suggest you give a look at [this nicely written article by Patrick Louis](https://venam.nixers.net/blog/unix/2020/07/06/dbus-polkit.html).

## How

Now what languages are we gonna use? Ha! Trick question! If you know me (or can read the title) you know we'll be using Python. Besides I'm pretty sure you can use whatever language you prefer, but today that's what we're gonna use.

Before we begin, it's good practice to choose a unique id for our daemon using the [reverse domain name notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation). For the HydraPaper Daemon I chose `org.gabmus.hydrapaper.Daemon`. It's important that your name is unique, so it's a good practice to name it something like `ext.yourPersonalHandle.yourApplicationName.Daemon`. Another good idea is if you have a registered domain like I do for `gabmus.org`, to just use that, followed by the name of your app and some other indication that this service will be a daemon. [You can read more about valid names here](https://dbus.freedesktop.org/doc/dbus-specification.html#message-protocol-names).

Once you have decided this id, just put it in a constant, along with the same but separated by slashes as if it was a path, like this:

```python
UID         =  'org.gabmus.myapp.Daemon'
UID_AS_PATH = '/org/gabmus/myapp/Daemon'  # notice the leading slash
```

Let's now start with the basics: we need a class extending `dbus.service.Object`. This will be our **D-Bus Object**, and some of the methods inside of it will be exposed as **D-Bus methods**.

```python
import dbus
import dbus.service  # yes, you need to import this as well


UID         =  'org.gabmus.hydrapaper.Daemon'
UID_AS_PATH = '/org/gabmus/hydrapaper/Daemon'


class MyappDaemon(dbus.service.Object):
    def __init__(self, bus_name):
        super().__init__(
            bus_name, UID_AS_PATH
        )
```

Now before moving on, I decided to include the final part here: bootstrapping our main loop and all of the things we need to get this bad boy running, so that you can actually try things out and see what's going on instead of just blindly copy-pasting.

You'll need these additional imports:

```python
from dbus.mainloop.glib import DBusGMainLoop
from gi.repository import GLib
```

Let's move **to the bottom of the file** and create a main function, just to do things cleanly:

```python
def main():
    DBusGMainLoop(set_as_default=True)
    try:
        bus_name = dbus.service.BusName(
            UID, bus=dbus.SessionBus(), do_not_queue=True
        )
    except dbus.exceptions.NameExistsException:
        print(f'Service with id {UID} is already running')
        exit(1)
    loop = GLib.MainLoop()
    daemon = MyappDaemon(bus_name)
    try:
        loop.run()
    except KeyboardInterrupt:
        print('KeyboardInterrupt received')
    except Exception as e:
        print('Unhandled exception: `{}`'.format(str(e)))
    finally:
        loop.quit()


if __name__ == '__main__':
    main()
```

Alright, let's resume from where we left.

We have a D-Bus object, and the thing can run doing nothing now, awesome.

Let's give this object a method we can invoke from outside.

To do this we'll create a method in our class and decorate it with `@dbus.service.method`. This decorator takes some parameters:

- `dbus_interface`: the interface we want this method to be attached to (interfaces are another nesting level for D-Bus, honestly I think they're overkill for this kind of use, so we're just going to use our previously declared `UID` as the interface, it's gonna work).
- `in_signature`: what kind of parameters our method requires
- `out_signature`: what kind of output our method returns

For the signatures, you can read more about data types you can use [in this documentation page](https://dbus.freedesktop.org/doc/dbus-python/tutorial.html#data-types).

Let's create a simple `hello` method that takes a string and returns another string.

```python
class MyappDaemon(dbus.service.Object):
    # ...
    @dbus.service.method(
        dbus_interface=UID,
        in_signature='s', out_signature='s'
    )
    def hello(self, your_name: str) -> str:
        return f'Hi there, {your_name}!'
```

That's it! Easy, wasn't it?

Now if like me you want to have some action to run every x seconds or something, you can just use the standard threads and sleep model of Python, not much else you need as far as D-Bus goes.

Now, let's see what we need to do to package this new Daemon in a Flatpak and have it being recognized by the system as proper and activatable D-Bus service.

## Plumbing

First off, we'll be using meson, so I assume you already are using it for your base application.

Assuming we have our daemon inside a sub-folder called `myapp_daemon`. For packages reasons I suggest you call the actual daemon Python file something like `myappd.in.py`. Notice the trailing `d`, it means daemon. Another thing: **make sure to give this file executable permissions!** (`chmod +x myapp_daemon/myappd.in.py`)

At the beginning of `myappd.in.py` we're going to insert our shebang like this:

```python
#!@PYTHON@
```

In our main `meson.build` file, the one in the root of the project, let's put the following somewhere you feel is appropriate (skip anything that is already there of course):

```meson
prefix = get_option('prefix')
datadir = get_option('datadir')
libexecdir = join_paths(prefix, get_option('libexecdir'))
etcdir = get_option('sysconfdir')
dbus_service_dir = dependency(
    'dbus-1'
).get_pkgconfig_variable(
    'session_bus_services_dir',
    define_variable: ['datadir', join_paths(prefix, datadir)]
)
python = import('python')
py_installation = python.find_installation('python3')
if not py_installation.found()
    error('No valid python3 binary found')
endif

subdir('myapp_daemon')
subdir('data')
```

Now, let's create `myapp_daemon/meson.build`, and put this inside:

```meson
daemon_conf = configuration_data()
daemon_conf.set('PYTHON', py_installation.path())

configure_file(
    input: meson.project_name() + 'd.in.py',
    output: meson.project_name() + 'd',
    install: true,
    install_dir: libexecdir,
    configuration: daemon_conf
)
```

That's it for the daemon itself, but we still need some other files to let Flatpak (or your host operating system for that matter if you install this with a regular package manager) recognize this as an activatable service.

*Note: an activatable service means a service that will be started automagically as soon as an application tries to connect to it.*

For this, I like to work inside of a new folder called `data` inside the project root.

Inside `data` let's create:

`org.gabmus.myapp.Daemon.desktop.in` containing:

```desktop
[Desktop Entry]
Name=org.gabmus.myapp.Daemon
Comment=Daemon for myapp
Icon=org.gabmus.myapp.Daemon
Exec=@libexecdir@/myappd
X-GNOME-Autostart-Delay=10
StartupNotify=false
NoDisplay=true
Type=Application
```

`org.gabmus.myapp.Daemon.service.in` containing:

```desktop
[D-BUS Service]
Name=org.gabmus.myapp.Daemon
Exec=@libexecdir@/myappd
```

`meson.build` (if it exists already, just add to it) containing:

```meson
data_conf = configuration_data()
data_conf.set('libexecdir', libexecdir)

configure_file(
    input: 'org.gabmus.myapp.Daemon.desktop.in',
    output: 'org.gabmus.myapp.Daemon.desktop',
    configuration: data_conf,
    install: true,
    install_dir: join_paths(etcdir, 'xdg', 'autostart')
)

configure_file(
    input: 'org.gabmus.myapp.Daemon.service.in',
    output: 'org.gabmus.myapp.Daemon.service',
    configuration: data_conf,
    install: true,
    install_dir: dbus_service_dir
)
```

Finally, we need to tell Flatpak that the `org.gabmus.myapp.Daemon` name is owned by our application. To do this let's add the following to our Flatpak manifest, inside the `finish-args` array:

```json
"--own-name=org.gabmus.myapp",
"--own-name=org.gabmus.myapp.Daemon"
```

Great! Now in theory everything should be in place, of course let me know if anything is broken or doesn't work, the comment section is there to be used!

For the final section of this small tutorial, let's see how to call the method we created.

## Summon the daemon

For this last section, I'll just leave you with some very brief example code in Python, hopefully it's easy enough to follow:

```python
import dbus

def summon_hello(name):
    try:
        bus = dbus.SessionBus()
        obj = bus.get_object(
            'org.gabmus.myapp.Daemon',
            '/org/gabmus/myapp/Daemon'
        )
        interface = dbus.Interface(
            obj, dbus_interface='org.gabmus.myapp.Daemon'
        )
        return interface.hello(name)
    except dbus.exceptions.DBusException:
        print('Failed to communicate with `org.gabmus.myapp.Daemon`!')
        return ''
```

---

Hope you can find this tutorial useful. Mind you, I'm not an expert in this matter, I just did this and failed to find a single tutorial summarizing all of what I needed, so I decided to make one myself.

If you find that anything doesn't work as expected (maybe I missed a step or two?) please let me know in the comments, or contact me directly by using one of the contacts you can find [in the About page](/pages/about).

---
title: Command line options with Relm4
tags:
  - rust
  - gtk
  - gtk4
  - terminal
  - relm4
date: 2024-02-07T07:32:14+01:00
---

I've been using [Relm4](https://relm4.org/) for a project and it's been pretty great. Relm4 is basically a wrapper around the GTK API that offers a more declarative layer of abstraction for building applications.

The only problem I find is that the project seems a little bit niche. Don't get me wrong, it seems very polished and so far I haven't found any bugs, so that's exceptional considering the scope of the project. The real problem with it being niche is that of course not every use case can be fully covered and documented, so some stuff you just have to figure out for yourself.

This was the case for me with command line options.

You might know that GTK offers a way to manage command line options out of the box, but Relm4 simply doesn't wrap that particular API. After a bit of research I found a good way to make it all work and here's how.

So given a struct named `MyAppComponent` being your root Relm4 component:

```rust
let app = gtk4::Application::builder()
    // set other properties as needed...
    .flags(gtk4::gio::ApplicationFlags::HANDLES_COMMAND_LINE)
    .build();

// don't know if this really MUST be static, but that's what works for me
static BROKER: relm4::MessageBroker<MyAppComponent::Input> = relm4::MessageBroker::new();
// this is just an example, here's the documentation for the Gio.Application API
// https://docs.gtk.org/gio/method.Application.add_main_option.html
app.add_main_option(
    "foo",
    gtk4::glib::Char::try_from('f').unwrap(),
    gtk4::glib::OptionFlags::IN_MAIN,
    gtk4::glib::OptionArg::None,
    "foo option description here",
    None
);
// add as many options as you want

let sender = BROKER.sender(); // we'll move this to the signal handler below
app.connect_command_line(move |this, cmdline| {
    this.activate();
    // very basic example, what I actually did was parse the
    // ApplicationCommandLine object and save my flags in an ad-hoc struct
    // but this should also just work
    sender.emit(MyAppComponent::Input::HandleCommandLine(cmdline));
    0
});
let relm_app = relm4::RelmApp::from_app(app).with_broker(&BROKER);
relm_app.run::<MyAppComponent>(MyAppComponent::Init {
    // your init data here
});
```

All you need now is a handler for the `HandleCommandLine` input signal in your app component and you're pretty much all set.

This is very much a simplified example of what I'm actually doing, but it should serve as a good base to build on top of. Being that this was previously completely undocumented, at least it's a starting point.

Feel free to comment if you have found a better solution or if somehow I missed some critical piece of documentation that explains the actual way to do this.

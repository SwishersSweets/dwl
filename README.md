# MY PERSONAL BUILD OF DWL
### Patches:
    swallow
    gaps
    bar (added tllist dependency)
### Keybinds
ModKey = super/logo/meta/windows
ModKey + Return spawns a terminal
ModKey + D Spawns Fuzzel (app launcher)
ModKey + Shift + Q kills the current focused window
ModKKey + Shift + E exits dwl
ModKey + Tab Switches the focused tab to the master

## Building dwl

dwl has the following dependencies:
```
Typed Linked List (tllist)
libinput
wayland
wlroots (compiled with the libinput backend)
xkbcommon
wayland-protocols (compile-time only)
pkg-config (compile-time only)
```
If you enable X11 support:
```
libxcb
libxcb-wm
wlroots (compiled with X11 support)
Xwayland (runtime only)
```

Simply install these (and their `-devel` versions if your distro has separate
development packages) and run `make`.  If you wish to build against a Git
version of wlroots, check out the [wlroots-next branch].

To enable XWayland, you should uncomment its flags in `config.mk`.

## Configuration

All configuration is done by editing `config.h` and recompiling, in the same
manner as dwm. There is no way to separately restart the window manager in
Wayland without restarting the entire display server, so any changes will take
effect the next time dwl is executed.

As in the dwm community, we encourage users to share patches they have created.
Check out the dwl [patches repository] and [patches wiki]!

## Running dwl

dwl can be run on any of the backends supported by wlroots. This means you can
run it as a separate window inside either an X11 or Wayland session, as well
as directly from a VT console. Depending on your distro's setup, you may need
to add your user to the `video` and `input` groups before you can run dwl on
a VT. If you are using `elogind` or `systemd-logind` you need to install
polkit; otherwise you need to add yourself in the `seat` group and
enable/start the seatd daemon.

When dwl is run with no arguments, it will launch the server and begin handling
any shortcuts configured in `config.h`. There is no status bar or other
decoration initially; these are instead clients that can be run within
the Wayland session.
Do note that the background color is black.

If you would like to run a script or command automatically at startup, you can
specify the command using the `-s` option. This command will be executed as a
shell command using `/bin/sh -c`.  It serves a similar function to `.xinitrc`,
but differs in that the display server will not shut down when this process
terminates. Instead, dwl will send this process a SIGTERM at shutdown and wait
for it to terminate (if it hasn't already). This makes it ideal for execing into
a user service manager like [s6], [anopa], [runit], [dinit], or [`systemd --user`].

Note: The `-s` command is run as a *child process* of dwl, which means that it
does not have the ability to affect the environment of dwl or of any processes
that it spawns. If you need to set environment variables that affect the entire
dwl session, these must be set prior to running dwl. For example, Wayland
requires a valid `XDG_RUNTIME_DIR`, which is usually set up by a session manager
such as `elogind` or `systemd-logind`.  If your system doesn't do this
automatically, you will need to configure it prior to launching `dwl`, e.g.:

    export XDG_RUNTIME_DIR=/tmp/xdg-runtime-$(id -u)
    mkdir -p $XDG_RUNTIME_DIR
    dwl

### Status information

Information about selected layouts, current window title, app-id, and
selected/occupied/urgent tags is written to the stdin of the `-s` command (see
the `printstatus()` function for details).  This information can be used to
populate an external status bar with a script that parses the information.
Failing to read this information will cause dwl to block, so if you do want to
run a startup command that does not consume the status information, you can
close standard input with the `<&-` shell redirection, for example:

    dwl -s 'foot --server <&-'

If your startup command is a shell script, you can achieve the same inside the
script with the line

    exec <&-

To get a list of status bars that work with dwl consult our [wiki].

## Replacements for X applications

You can find a [list of useful resources on our wiki].

## Acknowledgements

dwl began by extending the TinyWL example provided (CC0) by the sway/wlroots
developers. This was made possible in many cases by looking at how sway
accomplished something, then trying to do the same in as suckless a way as
possible.

Many thanks to suckless.org and the dwm developers and community for the
inspiration, and to the various contributors to the project, including:

- **Devin J. Pohly for creating and nurturing the fledgling project**
- Alexander Courtis for the XWayland implementation
- Guido Cella for the layer-shell protocol implementation, patch maintenance,
  and for helping to keep the project running
- Stivvo for output management and fullscreen support, and patch maintenance

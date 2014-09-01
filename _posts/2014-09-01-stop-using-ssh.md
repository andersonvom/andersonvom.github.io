---
layout: post
title: Stop Using SSH
---

Actually, don't do that quite yet, but be aware of an interesting alternative
that may come in handy a lot of times, especially when your connection is flaky
and unreliable.  When SSHing into a remote machine, things will be just fine as
long as your connection is snappy and you're not getting too much output from
the server.  Once you get disconnected from your VPN, you move between networks
(WiFi or mobile), you start lagging behind due to network usage/issues, or you
get A LOT of output from your remote server, SSH just doesn't respond properly.

[Mosh][] (short for Mobile Shell) is a remote terminal application that is
bootstrapped on SSH but runs standalone afterwards to try and solve the
problems listed above.  It will SSH into the remote machine and start a UPD
connection between your local and remote server.  From this point on, it will
try to keep your current screen in sync, without caring too much about lost
packages or text that is long gone in your scroll.  It will also try to predict
your local echo, so that you see what you're typing without having to wait for
the request to go to the remote server and back.


### Installation

This is the hard part:

    sudo apt-get install mosh  # Debian-based distros
    brew install mobile-shell  # Mac

If you use a different system, see more info [here][installation]. Moving on...


### Usage

Using it is pretty straightforward and very similar to SSH:

    # This will connect to example.com as user on port 22
    mosh [user@]example.com


... but not without its caveats:

    # Depending on how you install it, you may have to specify the server path
    # I'm having this problem when connecting to Macs
    mosh --server=/usr/local/bin/mosh-server example.com

    # If you want to connect to a different SSH port, -p is not the option
    # It changes the UDP post for mosh, instead you'll use:
    mosh --ssh="ssh -p 2222" example.com


### Issues

This are still evolving and there are some annoying things that still happen
when using Mosh.

* You will lose your scroll: this should be [solved][issue] in Mosh 1.3, but
  for now, you won't be able to scroll up unless you're using tmux/screen in
  the remote server as well.
* You have to have more open ports: unfortunately Mosh connects via UDP on
  ports 60000+, so you have to have these ports open as well as SSH port on
  your remote server.


[Mosh]: https://mosh.mit.edu/#
  "Visit Mosh website"
[installation]: https://mosh.mit.edu/#getting
  "How to install Mosh in other systems"
[issue]: https://github.com/keithw/mosh/issues/2
  "Possible solution for the scroll problem with Mosh"

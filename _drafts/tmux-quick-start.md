---
layout: post
title: tmux for pair programming
---

As I mentioned in a [previous post][vim-tips], I use vim and [tmux][] a
lot when pair programming, and while I spend most of the time on vim
itself, tmux also plays a big role, even if in a more passive way.

I use tmux merely as a container for my terminal windows and as a means
to retrieving my setup back in case I accidentaly close my terminal
application or I need to update and restart it. 

The shortcuts I end up using 95% of the time are:


    CTRL+b c            # Create window
    CTRL+b ,            # rename window
    CTRL+b <n/p/l/num>  # change to next/previous/last/number window
    CTRL+b d            # leave tmux session
    tmux attach         # rejoin previous tmux session
    
    # and more rarely, usually for viewing logs
    CTRL+b %            # split window vertically
    CTRL+b "            # split window horizontally


**PRO Tip**: if you pair program daily, keeping your window numbers or
your shortcuts the same will make your pair be greatly thankful! ;)


[vim-tips]: {% post_url 2013-12-16-vim-tips %}
  "Read 'Vim Most Used Commands'"
[tmux]: "http://"
  "Visit tmux website"

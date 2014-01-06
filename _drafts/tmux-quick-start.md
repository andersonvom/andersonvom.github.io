---
layout: post
title: tmux for pair programming
---

#### tmux

As I mentioned in a [previous post](/2013-12-16-vim-tips), I use vim and tmux a
lot when pair programming, and while I spend most of the time on vim itself,
tmux also plays a big role, even if in a more passive way.

I use tmux merely as a container for my windows, so the only commands
I'll end up using 95% of the time are:


    CTRL+b c            # Create window
    CTRL+b ,            # rename window
    CTRL+b <n/p/l/num>  # change to next/previous/last/number window
    CTRL+b d            # leave tmux session
    tmux attach         # rejoin previous tmux session
    
    # and more rarely, usually for viewing logs
    CTRL+b %            # split window vertically
    CTRL+b "            # split window horizontally


Keeping your window numbers the same will make your pair be greatly
thankful! ;)

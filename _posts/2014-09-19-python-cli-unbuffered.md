---
layout: post
title: Python CLI Unbuffered
---

Suppose you just inherited a python script that does some
complex/expensive task and prints logging information to STDOUT.  I say
inherited because if you are writing your own script to do anything, you
should really be using the [logging][] module instead of just printing
stuff to the terminal.

Now printing messages to directly to the terminal has a few
disadvantages, but that's beyond the scope of this article, and the
bottom line is that your have a python script and you want to redirect
its output to a file for later processing or just because you don't want
to risk losing part of the information because it's larger than your
terminal buffer.  One thing you can do is use [tee][] for that:

    python script.py | tee -a script.log

While that works fine, you may run into the following inconvenience: if
your script does not output a lot of text, it'll get buffered and you'll
only see it in chunks.  Python does that for you so that it won't be
really slow if you're writing a LOT of text to `STDOUT`. :D

You can see the buffering result by using the following code in your
`script.py` and redirecting the output to `script.log` as showed above.

    import time

    for i in range(10):
        print i
        time.sleep(5)

You'll notice that you have to wait the full 50 seconds to see anything
on the screen, which is not ideal for such a short amount of text.  So,
if you know you're not printing a lot of text and you need immediate
feedback, you can bypass that:

    python -u script.py | tee -a script.log

That makes python treat `STDOUT`, `STDERR`, and `STDIN` completely
unbuffered.  This will significantly slow down your execution if you're
printing a lot of text, but will come in really handy if the amount of
text is not too high and you need to see what the script is doing right
away.


[logging]: https://docs.python.org/2/library/logging.html
  "logging — Logging facility for Python"
[tee]: http://pubs.opengroup.org/onlinepubs/9699919799/utilities/tee.html
  "tee - duplicate standard input"

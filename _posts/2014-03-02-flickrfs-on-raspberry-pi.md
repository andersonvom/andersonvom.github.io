---
layout: post
title: Running flickrfs on your Raspberry Pi
---

This is a quick and dirty guide to get you started with [flickrfs][] on
your [Raspberry Pi][rpi].  flickrfs allows you to synchronize the photos
on your [Flickr][] account with your hard drive, so you can quickly
upload and edit your photos without having to go through their web
interface and in a much faster way (if you're comfortable with using a
terminal).  If you're not the type of person who uses CLIs, you can
still take advantage of all the syncing or just have a very convenient
way of downloading all your photos to your hard drive.

This guide assumes you're familiar with using your terminal to open an
SSH connection to your raspberry pi. =)  Once in your RPi, just do the
following steps:


    sudo apt-get update
    sudo apt-get install -y flickrfs
    sudo vim /etc/group
        # Add your user (defaults to pi) to the fuse group
        fuse:x:108:pi
    sudo grpconv

    # If you don't already have a text-base browser, you also want to
    # install one:
    sudo apt-get install lynx


Save and group file, quit vim, log out, log back in so that the
group changes take effect, and run flickrfs for the first time by typing
`flickrfs /path/to/your/favorite/location`.  If you SSH'ed into your
RPi, this will fail.  Don't worry, it just tried to open your browser
and crashed since you're not in an X environment.  Once you're back in
your shell prompt again, edit flickrfs configuration file:


    vim ~/.flickrfs/config.txt
        # change the browser line to look like this:
        browser:/usr/bin/lynx
        # if you use another browser, use its path here


You're now ready to run flickrfs again.  This time you'll have to
authorize it to access your Flickr account and your CLI browser will be
opened for you to do this.  If you're using lynx, here's what you need
to know:


    Use the UP and DOWN arrow keys to move around the page
    Hit ENTER to "click" a link or submit a form
    When prompted for cookies, type Y or A to accept them
    Type Q to quit


And you're done! \o/


[flickrfs]: https://sites.google.com/site/manishrjain/flickrfs
  "Visit flickrfs project website"
[rpi]: http://smile.amazon.com/gp/product/B009SQQF9C/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B009SQQF9C&linkCode=as2&tag=andersonvom-20
  "Get yourself a Raspberry Pi"
[flickr]: http://flickr.com/
  "Visit Flickr website"

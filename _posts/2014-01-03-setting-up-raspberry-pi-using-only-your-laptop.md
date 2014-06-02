---
layout: post
title: Setting Up Your Raspberry Pi Using Only Your Laptop
---

So you just got your [Raspberry Pi][rasp-pi] and want to get started but
remembered you don't have an extra keyboard, mouse, or even a spare
monitor to actually get something done?  Well, that's my case where I
only have my laptop available (nope, I don't own a TV).

Luckily there's a way around: the latest Debian Wheezy has SSH enabled
by default and you can just SSH into your pi once you're done! Here's
how to go about it:


* Download the latest version of [Raspbian][download-rasp-pi].  At the
  time of this writing, it was [2013-12-20-wheezy][wheezy-torrent].

* Insert your sd-card, find out its location, and unmount it:  
  `diskutil list`  
  `diskutil unmountDisk /dev/<sdcard-location>`

* Unzip the file and burn the image (.img) you just downloaded. Don't
  worry if it takes a longe time, depending on the speed of your sdcard,
  it may take anywhere between 20 minutes and 2 hours.  
  `sudo dd bs=1m if=<your-file.img>.img of=/dev/<sdcard-location>`  

* Boot your Pi and search for its IP address so you can SSH into it:  
  `arp -a`

* SSH into your brand new raspbian:  
  `ssh pi@<domain-name | ip-address>`

* Run the configuration tool just to make sure you have good defaults:  
  `raspi-config`


That's it!  Now you can go enjoy [your raspberry pi][buy-rasp-pi] via
SSH or VNC.


[rasp-pi]: http://www.raspberrypi.org/
  "Go to Raspberry Pi website"
[download-rasp-pi]: http://downloads.raspberrypi.org/raspbian_latest.torrent
  "Download the latest version of Raspbian"
[wheezy-torrent]: /files/2013-12-20-wheezy-raspbian.zip.torrent
  "Download Wheezy for Raspberry Pi via torrent"
[buy-rasp-pi]: http://smile.amazon.com/gp/product/B009SQQF9C/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B009SQQF9C&linkCode=as2&tag=andersonvom-20
  "Buy a Raspberry Pi on Amazon.com"

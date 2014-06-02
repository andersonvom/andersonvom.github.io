---
layout: post
title: Sharing External Hard Drive on a Raspberry Pi
---

This is a quick and dirty guide to setting up your external hard drive
with your raspberry pi as a file server.

First off, you have to mount your drive. This can be achieved with the
following:


    sudo apt-get install ntfs-3g  # if your drive is NTFS
    sudo mkdir /media/hd
    sudo mount -t ntfs-3g -o rw,defaults /dev/sda1 /media/hd


You should probably add your external hard drive to your `/etc/fstab`
file using these options here:


    /dev/sda1    /media/hd    ntfs-3g rw,defaults    0    1


Now that you have your hard drive set up, onto setting up samba to share
it on your network:


    sudo apt-get install samba samba-common-bin
    sudo smbpasswd -a pi
    # Type the password for the user `pi`

    sudo vi /etc/samba/smb.conf
        # You can use all defaults and
        # just change the following options
        [global]
        security = user
        socket options = TCP_NODELAY

        # Add this group to the end of the file
        [pi-hd]
        comment = Pi External Hard Drive
        path = /media/hd
        writeable = Yes
        browseable = Yes
        valid users = pi

    sudo service samba restart
    sudo testparm -s
    # You should see the group you created listed here


If everything worked, you should be able to connect to your shared
folder using the user `pi` and the password you typed before.

It's worth mentioning that, while you may be able to share your hard
drive's contents plugging it directly into the RPi, it will most likely
die from time to time - or even fail to mount completely - if you're
using it for other processing intensive things (e.g. using [xbmc][],
running a web server, etc).  You'll benefit a lot from buying a [USB
hub][usb-hub] and plugging your hard drive and you RPi in it!


[xbmc]: http://xbmc.org/
  "Visit XBMC web site"
[usb-hub]: http://smile.amazon.com/gp/product/B003Z4G3I6/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B003Z4G3I6&linkCode=as2&tag=andersonvom-20
  "Plugable 7 Port High Speed USB 2.0 Hub with 3A Power Adapter"

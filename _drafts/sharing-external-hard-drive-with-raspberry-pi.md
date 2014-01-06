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

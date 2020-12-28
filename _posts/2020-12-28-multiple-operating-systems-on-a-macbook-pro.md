---
layout: post
title: Multiple operating systems on a MacBook Pro
---

Booting from multiple operating systems on a mac can be a bit of a pain. This
is a quick guide to be able to boot into OSX/Linux/Windows 10:

Resize disk via `Disk Utility`: go to `Partition` and resize the partition
where OSX is. Optional step: create partitions for Linux, Windows, and maybe an
extra one to easily transfer files between OSes.

Note: `Disk Utility` will convert the partitioning scheme from [GPT][gpt] to
[Hybrid MBR][h-mbr]. Windows 10 install won't be happy with this (see #2 and
#3).

Temporarily [disable System Integrity Protection][sip] (SIP): reboot holding
`Cmd+R`, open a terminal, and type `csrutil disable`. This is necessary for the
next step, otherwise the changes will keep being reverted.

Install [gdisk][gdisk] and convert the partitioning scheme from `Hybrid MBR`
back to `GPT`:

{% highlight bash %}
brew install gdisk
sudo gdisk /dev/disk0

# type the following inside gdisk. in case the menu changes, type `?` before
# each of them to double check they actually point to the correct menu entry
x   # extra functionality (experts only)
n   # create a new protective MBR
w   # write table to disk and exit
{% endhighlight %}

Install Windows 10 and/or your linux distro of choice. Depending on your
distro, installing linux last will include Windows in the Grub configuration
automatically. This may be useful if you intend to use Grub as the boot loader.

While Grub may pick up the Windows configuration, in order to boot into OSX,
you'll have to hold the `option` key while rebooting. If you don't like this
behavior, after installing your linux distro, install [rEFInd][refind] (e.g.
`sudo apt install refind`). `rEFInd` will scan all drives for any bootable info
(e.g. EFI partitions, linux kernels, bootable USB sticks, etc) and allow you to
choose one at runtime.

Profit.

[sip]: https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection
[gdisk]: https://www.rodsbooks.com/gdisk/
[gpt]: http://www.rodsbooks.com/gdisk/whatsgpt.html
[h-mbr]: http://www.rodsbooks.com/gdisk/hybrid.html
[refind]: http://www.rodsbooks.com/efi-bootloaders/refind.html

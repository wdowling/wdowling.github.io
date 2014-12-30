---
layout: post
title: Installing OpenBSD on a Macbook Pro 7,1
---

### TEST POST ###

After using my Macbook Pro 7,1 (mid-2010) for several years, it began to show the usual signs
of wear and tear. The boot up time was already over a minute to reach even the login screen and even 
after logging in the load time to generate a usable desktop was laughable. Starting applications 
would take on average between 15 - 20 seconds each time. 
OWC came to the rescue with an offer to increase RAM to 8GB and swapping the SATA drive for an SSD.
This change made a huge difference. Startup time is down to 10seconds and as soon as I am logged 
in there is no longer the lag in opening any application. I highly recommend a hardware upgrade!

Since I had 480GB of SSD to play with I decided to try to dualboot another OS. I have been
interested in moving away from Linux towards something more UNIX-like but the opportunity never
came about until now.

I had tested the various illumos based distributions on VirtualBox -(OpenIndiana and OmniOS), and although
it was great to see the Solaris kernel based  distribution alive (almost re: OpenIndiana), the lack of hardware
support and the reliance on Solaris closed binaries was not to my liking. I wanted an OS
that did not have the proprietry baggage of a previous history. So after a quick scope of the land
I decided to try out OpenBSD. I had not used it in many years and it looked I was attempting to install
it on hardware with limited support but that is all part of the fun. It would also give me an excuse to
follow the mailing lists and learn more about UNIX internals.

#### Pre-Installation

Since I liked using OSX, I wanted to dual-boot both operating systems. Dual booting can be achieved 
using the rEFInd Boot Manager - http://www.rodsbooks.com/refind/. Apple hardware uses EFI instead of
the traditional BIOS for booting. The installation on OSX is simple:

`
$ unzip refind-bin-0.8.4.zip
$ cd refind-bin-0.8.4
$ sudo ./install.sh`

This will create an efi directory on  / which you should not need to touch. 

It would be a good idea to install any OSX updates needed and do a full backup with Time Machine
or a cloning method of your choice. 

#### Installation and Configuration

Download install56.fs from your nearest OpenBSD mirror and dd it to a USB drive:

`# diskutil unmountDisk /dev/disk1`
`# dd if=./install56.fs of=./dev/rdisk1 bs=1m`
`# reboot`

Once the rEFInd menu loads you should be able to see the Puffy logo alongside the Apple. Select the Puffy and hit enter. 

The first thing to do is to break out to the shell prompt and partition the disks. Once the disks are partitioned continue by typing 'install' and going through the install process. Once the partition layout process appears choose Custom Layout and you should be able to see the partitions you already created. Make sure to assign mount points and save. Use sd0 for package locations.
 
#### Post-Installation

On reboot OpenBSD comes up without too much of an issue. The WiFi card is not supported so I used a cable instead. The first thing to do is to install some utilities to get a basic desktop up and running.

`# echo "PKG_PATH=ftp://ftp.heanet.ie/pub/OpenBSD/5.6/`machine -a`/" >> ~/.profile`
`# echo "export PKG_PATH" >> ~/.profile`
`# pkg_add -i -v  vim mutt xfce`
`$ echo 'exec startxfce4' >> .xinitrc`

Running `startx` starts up XFCE and gives you a taste of an OpenBSD desktop.

#### Thoughts

So what works?

Installing OpenBSD on Apple hardware is never going to be easy. This generation of hardware does
not make it any easier. The biggest issues are the NVidia Geforce 320M graphics card and the Broadcom BCM4331 wireless card. Neither provide good support and documentation for creating Open Source drivers. The Xenocara project attempts to cover most NVidia cards with the 'nv' driver but unfortunately this particular card is not supported. There is currently no sign of Broadcom support in the near future so to get around this I used a mini Edimax WiFi USB adapter using the urtwn driver. This works without any problems.

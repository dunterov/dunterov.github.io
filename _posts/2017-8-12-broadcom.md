---
layout: post
title: Fedora 26 + Broadcom Limited BCM43142 WiFi adapter
date: 2017-08-12 14:00:01 +0300
categories: linux fedora broadcom
---
Hi! If you are lucky guy just like me and you have Broadcom Limited BCM43142 and also you love Fedora Linux, this post for you.

### What's wrong?

Nothing, except that default fedora's installation haven't proper module for that excellent hardware.
So, first thing is to get know which model do we have:
```
$ lspci -k
02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)
	Subsystem: Lenovo Device 0611
```
Ok, we have '02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)' and no single module in use.
To fix that you need to connect to the internet via wired connection (I really hate this part), and do the following:
* go to [RPM Fusion' site](https://rpmfusion.org/Configuration)
* Download and install [this](https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-26.noarch.rpm) (for Fedora 26)
* And [this](https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-26.noarch.rpm) (also for Fedora 26)
* run
```
$ sudo dnf update
$ sudo dnf install akmod-wl
```
* Reboot

After reboot your wifi-module will work as expected. Check it:
```
$ lspci -k
02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)
	Subsystem: Lenovo Device 0611
	Kernel driver in use: wl
	Kernel modules: wl
```
Hope this post help someone.

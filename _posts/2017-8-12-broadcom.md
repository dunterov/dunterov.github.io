---
layout: post
title: Fedora 26 + Broadcom Limited BCM43142 WiFi adapter
date: 2017-08-12 14:00:01 +0300
categories: linux fedora broadcom
---

<div style="background-color: #eee; padding: 1em;">
  🗄️ <strong>Please note</strong>: <em>This is an older post - the info might be obsolete, but the links are still valid.</em>
</div>
Hi! If you are lucky guy just like me and you have Broadcom Limited BCM43142 and also you love Fedora Linux, this post for you.

### What's wrong?

Nothing, except that default Fedora's installation have no proper module for that excellent piece of hardware.
So, first thing is to get know which model do we have:

```
$ lspci -k
02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)
	Subsystem: Lenovo Device 0611
```

Ok, we have `02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)` and no single module in use.
To fix that you need to connect to the internet via wired connection (I really hate this part), and do the following:

* Go to [RPM Fusion' site](https://rpmfusion.org/Configuration)
* Download and install [this](https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-26.noarch.rpm) (for Fedora 26)
* And [this](https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-26.noarch.rpm) (also for Fedora 26)
* run
	```
	$ sudo dnf update
	$ sudo dnf install akmod-wl
	```
* Reboot

After reboot your WIFI module will work as expected. Check:

```
$ lspci -k
02:00.0 Network controller: Broadcom Limited BCM43142 802.11b/g/n (rev 01)
	Subsystem: Lenovo Device 0611
	Kernel driver in use: wl
	Kernel modules: wl
```

Hope this post help someone.

> ✅ *Content reviewed as of 19 June 2025*
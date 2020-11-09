---
published: true
title: 'HP Z32 «ghost» USB device causing automatic wake from suspend'
layout: post
---

I recently upgraded my forced home office with an [HP Z32](https://www8.hp.com/emea_middle_east/en/monitors/product-details/18269284) 4K UHD monitor. The display panel itself is everything I hoped it would be, a terrific improvement over my ageing FHD monitor.

That said, I have noticed an apparent bug in the monitor's built-in USB hub. When the upstream USB-C port is connected to my laptop (a [HP EliteBook 820 G4](https://support.hp.com/emea_middle_east-en/product/HP-EliteBook-820-G4-Notebook-PC/11122281/model/11122282)) using the included USB-C↔USB-C cable, the Linux kernel detects a *«ghost»* device that cannot be properly enabled. After each power-on or resume from suspend, the following is logged:

```
[ 1081.785820] kernel: usb usb2-port3: Cannot enable. Maybe the USB cable is bad?
[ 1085.857814] kernel: usb usb2-port3: Cannot enable. Maybe the USB cable is bad?
[ 1090.121887] kernel: usb usb2-port3: Cannot enable. Maybe the USB cable is bad?
[ 1090.121980] kernel: usb usb2-port3: attempt power cycle
[ 1094.505745] kernel: usb usb2-port3: Cannot enable. Maybe the USB cable is bad?
[ 1098.578533] kernel: usb usb2-port3: Cannot enable. Maybe the USB cable is bad?
[ 1098.578716] kernel: usb usb2-port3: unable to enumerate USB device
```

The message appear even when there are no USB devices connected to the external downstream ports on the monitor, so whatever this *«ghost»* device is, it is probably something connected to an internal port.

Annoyingly enough, the *«ghost»* device causes the laptop to wake from suspend by itself immediately after suspending it in the first place. That is, if I suspend the laptop, it will *ostensibly* go to sleep, but wake up again after a second or two. If I at this point quickly suspend it again, it will stay asleep.

In order for the second suspend attempt to be successful, it must be issued *before* the `unable to enumerate USB device` message appears. That gives me a window of approximately 15 seconds after the automatic wake-up.

A workaround is to use a USB-A↔USB-C cable to connect the laptop to the monitor instead. This makes suspend work reliably, and the error messages from the kernel no longer appear. However, this workaround does of course preclude the use of display output and charging via USB-C, features I certainly hope to be able to use when I upgrade my laptop next year.

Windows 10 is able to reliably suspend the laptop regardless of which USB cable is used, so there is probably a way to work around the problem in software too. I will update this post if I figure something out.

In any case, if you have a HP Z-series monitor and are having the same issue (or even if you are *not*), I would be very happy to hear from you! Send me an e-mail or a Tweet; my contact details are found at the bottom of the page.

For what it is worth, I also [wrote a post about this issue](https://h30434.www3.hp.com/t5/Desktop-Video-Display-and-Touch/Ghost-device-connected-to-Z32-built-in-USB-hub-causing/m-p/7847876) on the HP support forum.

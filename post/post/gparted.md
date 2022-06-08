---
layout: post
title:  "Resize the main partion with gparted"
date:   2021-08-27 09:08:52 -0600
categories: Os
tags: Linux
---
# Create a GParted live bootable USB drive
I\'ve tied to resize my main partion, I had to unmount it to do it.
[https://gparted.org/liveusb.php](https://gparted.org/liveusb.php)
You just have to follow the steps.
# Boot on the USB drive
You have to change the boot order. 
I can access to the bios settings without typing on every keys who start with F*.
I have an option to do it at startup: UEFI Firmware settings.
Now go to Boot and set the USB drive at the top to boot on it first.
# Gparted
Select the first choise to boot on it.
Launch GParted and you are ready to resize the partitions.
Be carefull you can loose your datas 
    





---
layout: post
title:  Add windows to grub 
date:   2021-08-27 23:54:52 -0600
categories: Os
tags: Linux
---
# Files
I\'t write DO NOT EDIT THIS FILE
But I\'ve done it, it was useless, I think I had to do it only if I was on MBR.  
I added this content in this file [Techbrown](https://www.techbrown.com/add-windows-10-boot-entry-to-grub-centos-rhel/)
```/boot/grub/grub.cfg```

This file was used to enable os-prober
```/etc/default/grub```
# Install OS-Prober
sudo pacman -S os-prober

Run it:
sudo os-prober

Output:
/dev/sda2@/efi/Microsoft/Boot/bootmgfw.efi:Windows Boot Manager:Windows:efi

# Enable os-prober in grub
edit /etc/default/grub
add GRUB_DISABLE_OS_PROBER=false

# Update grub
sudo grub-mkconfig -o /boot/grub/grub.cfg











---
layout: post
title:  "Get a shell when Arch Linux is broken"
date:   2021-07-06 22:55:56 -0600
categories: Os
tags: Linux
---


# Get a shell when arch don\'t boot

## Boot on a USB Live

Go to the boot menu and select the USB key with arch

Select Arch Linux install medium

## Loadkeys

I will set my keyboard in french:

```
loadkeys fr
```

## Mount the partition

with fdisk -l locate the right device 

I have:

-  /dev/nvme0n1p1 for the boot
- /dev/nvme0n1p2 for the system
- /dev/nvme0n1p3 for the swap

I will mount the system:

```
mount /dev/nvme0n1p2 /mnt
```

## chrooted

```none
arch-chroot /mnt /bin/bash
```


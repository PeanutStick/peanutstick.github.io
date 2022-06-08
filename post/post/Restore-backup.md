---
layout: post
title:  "Restore a backup with timeshift"
date:   2021-07-11 17:59:52 -0600
categories: Fix

tags: Linux
---
# Restore a backup with timeshift 
## mount the partitions
```
mount /dev/nvme0n1p2 /mnt
mount /dev/nvme0n1p3 /mnt/home
```
## Chroot
```
arch-chroot /mnt /bin/bash
```
## restore
```
timeshift --restore
```

Select snapshot:

0    >  2021-07-08_20-00-02  D W
1    >  2021-07-09_21-00-02  D
2    >  2021-07-10_00-00-37  O
3    >  2021-07-10_15-00-35  O
4    >  2021-07-10_16-48-33  O
5    >  2021-07-11_10-00-02  D
6    >  2021-07-11_12-23-57  O

I will take the last one: 
6
Re-install GRUB2 bootloader?
N
Do you know the risk ?
Y
And now you have to wait until it\'s done.

if you see ```Restore completed``` you can continue.
## unmount & reboot
```
exit
umount -R /mnt
reboot
```

---
layout: post 
title: "Add a partition in linux"
date: 2021-07-10 19:06:52 -0600 
categories: Os 
tags: Linux 
---

# Create the new partition

## Create the partition 
chose the hard drive:
```
fdisk -l
```

And them create the partition:
```
cfdisk /dev/sda
``` 

Once it's done you have to set the type.
```
mkfs.ext4 /dev/sda2
```
For me it's sda2 because I already use sda1 for the downloads, the sda2 will be for my games.
## Create the directory

Create the directory for the partition:
```
mkdir /home/peanutstick/games
```
And give the own:
```
sudo chown peanutstick $HOME/games -R
```
## Mount the partition
```
mount /dev/sda2 /home/peanutstick/games
```

## Set it in fstab
If you don't do if the changes will not be saved after a reboot.

```
sudo vim /etc/fstab
```
   
Now add this:
```
/dev/sda2 /home/peanutstick/games ext4 defaults 0 0
```



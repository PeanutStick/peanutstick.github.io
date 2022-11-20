---
layout: post
title:  "Arch Linux installation guide"
date:   2021-06-30 20:15:52 -0600
categories: Os
tags: Linux
---

# Install Arch on nvme drive, UEFI system

## Change layout

I will set the layout in French, because I am French.

```
loadkeys fr
```



## Set password

This is the first step for me, because without password I can't use SSH.

```
passwd
```

## Connect via SSH

I will use ssh to copy past the commands,
To get you'r IP addresse.

```
ip a
```

look like this for me: 192.168.0.25

On your other computer open a terminal or putty of what you want to connect via SSH.



## Partitions

to see your hard drive.

```
fdisk -l
```

Delete them.

```
cfdisk /dev/nvme0n1
```

```
cfdisk /dev/sda
```

Create 3 partition, 

1: 512Mb EFI 

2: 20Gb File system (root)

2: 80Gb File system (home)

And Format them:

This one for EFI

```
mkfs.fat -F32 /dev/nvme0n1p1
```

The two other like this:

```
mkfs.ext4 /dev/nvme0n1p2
```

```
mkfs.ext4 /dev/nvme0n1p3
```

Them mount the root partition

```
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/home
mount /dev/nvme0n1p3 /mnt/home
```

## Install the system

```
pacstrap -i /mnt base linux linux-firmware sudo nano
```

### Generate fstab file

```
genfstab -U -p /mnt >> /mnt/etc/fstab
```

### Chroot to the installed system

```
arch-chroot /mnt /bin/bash
```
### Set root password
To set a root password 
```
passwd
```
### Create a new user

```
useradd peanutstick -m
```
set the password
```
passwd peanutstick
```

### Set locale

uncomment the right line, for me it's fr_FR.UTF-8 UTF-8, because I am French!

```
nano /etc/locale.gen
```

now generate it.

```
locale-gen
```

Create a locale.conf file.

```
echo "LANG=fr_FR.UTF-8 UTF-8" > /etc/locale.conf
```

### Set the time zone

```
ln -sf /usr/share/zoneinfo/
```

Now select your time zone

```
ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime
```

### Set local time

```
hwclock --systohc --utc
```

To check it:

```
date
```

### Set hostname

```
echo peanutstick > /etc/hostname
```

Also add the name in /etc/hosts

```
nano /etc/hosts
```

it should look like this:

```
127.0.0.1       localhost
::1             localhost
127.0.1.1       peanutstick
```

### Enable network

Install the network manager:

```
pacman -S networkmanager
```

Enable it:

```
systemctl enable NetworkManager
```

If you don't do this part, your computer will not be able to connect to the network and you will not be able to download the packet.

### Install GRUB

Install Grub and efibootmgr

```
pacman -S grub efibootmgr
```

 install the bootlader:

```
mkdir /boot/efi
mount /dev/nvme0n1p1 /boot/efi
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi --removable
grub-mkconfig -o /boot/grub/grub.cfg
```

### Reboot

```
exit
umount -R /mnt
reboot
```

## Change boot order

don't pull out the USD stick, and select the last option to change the settings.

Set the right boot order, to boot on your hdd or ssd.  

## Disable speaker

My computer do a big BIP when I press tab or do something wrong:

```
rmmod pcspkr
```

To make it persistent:

```
nano /etc/modprobe.d/nobeep.conf
```

And write:

```
blacklist pcspkr
```

## Permanent KEYMAP

```
nano /etc/vconsole.conf
```

I will write:

```
KEYMAP=fr
```

## Add User to Sudoers

As root:

```
nano /etc/sudoers
```

add this to the end:

```
peanutstick ALL=(ALL) NOPASSWD:ALL
```

Be careful, the user can be root by just typing sudo su, sudo /bin/bash and more 

## Install sshd

```
pacman -S openssh
```



### Swap File

You probably have noticed that I have not created a Swap partition.

It's because this guy in this tutorial recommend to  use swap file: https://averagelinuxuser.com/linux-swap/

Create a Swap file of `3G` or whatever your RAM size is:

```
fallocate -l 3G /swapfile
```

Change its access rules, format and enable it:

```
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

Also, add this Swap file to the `/etc/fstab`:

```
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

And check if the Swap file is working:

```
free -m
```

### Install X window system and audio

```
pacman -S pulseaudio pulseaudio-alsa xorg xorg-xinit xorg-server
```

Enter to select all.

### Install desktop environment

Install xorg:

```
sudo pacman -S xorg xorg-server
```

This is for mate.

```
sudo pacman -S mate mate-extra lightdm lightdm-gtk-greeter
```

But I preffer Xfce:
```
sudo pacman -S --needed xfce4 mousepad parole ristretto thunar-archive-plugin thunar-media-tags-plugin xfce4-battery-plugin xfce4-datetime-plugin xfce4-mount-plugin xfce4-netload-plugin xfce4-notifyd xfce4-pulseaudio-plugin xfce4-screensaver xfce4-taskmanager xfce4-wavelan-plugin xfce4-weather-plugin xfce4-whiskermenu-plugin xfce4-xkb-plugin file-roller network-manager-applet leafpad epdfview galculator lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings capitaine-cursors arc-gtk-theme xdg-user-dirs-gtk
```

Enable the **lightDM** service to start on boot.

```
sudo systemctl enable lightdm
```

Finally, reboot your ArchLinux system.

```
sudo reboot
```


---
layout: post
title:  How to gain some GiB on Arch Linux
date:   2022-02-20 20:15:52 -0600
categories: Tips
tags: Linux
---

# See remaining space 
```sh
➜  ~ df -h

FS                 Size    Used  Free   Used% mount 
dev                3,8G       0  3,8G   0% /dev
run                3,9G    1,4M  3,9G   1% /run
/dev/nvme0n1p2      39G     27G   11G  72% /
tmpfs              3,9G     11M  3,9G   1% /dev/shm
tmpfs              3,9G    1,6M  3,9G   1% /tmp
/dev/nvme0n1p3      78G     42G   32G  58% /home
/dev/sda1          328G    179G  133G  58% /hdd
/dev/sda6          394G    306G   68G  82% /home/peanutstick/games
/dev/sda7           99G     42G   58G  42% /iso
tmpfs              783M     84K  783M   1% /run/user/1000
```

# Scan a filesystem
Install ncdu
```sh
pacman -S ncdu
```

Then use it.
Place yourself in the file system you whant to scan.
```sh
➜  ~ pwd
/home/peanutstick
➜  ~ sudo ncdu -x
```
![](https://i.imgur.com/UqlskCY.png)

# Remove a package
```sh
sudo pacman -Rsn <Package name>
```
# Remove pacman cache
1O GiB is gone for the cashe
![](https://cdn.discordapp.com/attachments/937315899139031075/944896129181548584/unknown.png)
 To remove all uninstalled packages
```sh
sudo pacman -Sc
```
# List 10 biggest package yay
```sh
yay -Ps

==> Yay version v11.1.0.r5.g19d42ae
===========================================
==> Nombre total de paquets installés :1560
==> Foreign installed packages: 63
==> Installation explicite des paquets : 328
==> Taille totale occupée par les paquets : 13.3 GiB
===========================================
==> Dix plus gros paquets :
ghidra: 928.1 MiB
burpsuite: 504.2 MiB
wine-staging: 502.2 MiB
rust: 496.6 MiB
nvidia-utils: 437.6 MiB
go: 432.3 MiB
metasploit: 330.8 MiB
dotnet-sdk-bin: 303.5 MiB
noto-fonts-cjk: 297.8 MiB
visual-studio-code-bin: 275.2 MiB
===========================================
:: Interrogation de AUR...
 -> Paquets AUR orphelins :  agg
 -> Paquets AUR marqués comme obsolètes :  optimus-manager  responder
```
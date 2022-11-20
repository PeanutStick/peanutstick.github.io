---
layout: post
title:  Fix the blur with picom
date:   2021-07-18 14:40:52 -0600
categories: Fix
tags: Linux
---
# Fix the blur with picom
## uninstall picom
The blur is avalaible only for the git version,
So if you have it from pacman you have to remove it.
```
sudo pacman -R picom
```
## Install picom
This is the git:
[https://aur.archlinux.org/packages/picom-git/](https://aur.archlinux.org/packages/picom-git/)
```
git clone https://aur.archlinux.org/picom-git.git
cd picon-git
makepkg -si
```
## Start it with i3
I have to add experimental-backends in my config.
`.config/i3/config`
```
exec --no-startup-id picom -b --experimental-backends
```
## Picom conf
I have nothing to add on it but you can modify things like you want.
`.config/picom/picom.conf`







---
layout: post
title:  "Installing AUR packages"
date:   2021-07-04 05:45:52 -0600
categories: Os
tags: Linux
---

## Installing AUR packages
## Install git
```
sudo pacman -S git
```
## Install base devel
```
sudo pacman -S base-devel
```
## Find the package

Visit AUR: https://aur.archlinux.org/ 

find the git clone URL of the chosen package.

it should look like this: https://aur.archlinux.org/opencv3-opt.git

## Create directory

If it's your first package you should create a directory for the AUR packages

```
mkdir ~/AUR
```

## Build it

`git clone [the package]`, `cd [the package]`, `makepkg -si`



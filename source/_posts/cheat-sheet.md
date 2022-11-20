---
layout: post
title:  Cheat Sheet of the commands I use
date:   2021-08-08 09:15:52 -0600
categories: Os
tags: Linux
---
# Intro
It\'s mostly usefull for me.
I will put some commands here and delet them later if I can remember them.

## Screen
### Set resolution as auto
```
xrandr --output eDP-1 --auto
```
### Display on second monitor
```
xrandr --output HDMI-1-0 --auto --right-of eDP-1
```
### Switch intel/NVIDIA GPU
```
optimus-manager --switch integrated, nvidia, hybrid, intel
```
## Audio
### Control pannel
```
pavucontrol
```
### Music
mpd (start the server)
ncmpcpp 
## Wifi
### Connect to a network
nmcli dev wifi connect "NAME" password "PASS"

### Show UUID
nmcli con show (not usefull, I can see it with polybar)

## Battery
### TLP
tlp-stat
/etc/tlp.conf







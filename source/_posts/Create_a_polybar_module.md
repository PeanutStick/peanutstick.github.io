---
layout: post
title: Create a polybar module
date:   2022-01-12 16:09:01 -0600
categories: Programing
tags: Linux
---
# Why
I had xfce4 panel, and I miss some informations like the remaining battery.

# Code

## Config polybar
In the config file `./config.polybar/config`.
```zsh
[module/discharge]
type = custom/script
exec = ~/.config/polybar/discharge.sh
format-underline = ${colors.white}
```
You also have to tell where gonna be the output.
In the config file:
```zsh
modules-right = filesystem xbacklight alsa pulseaudio wlan eth battery discharge  date powermenu
```
You have to add the name of your module, but it can be where you want.

## Script
In `./config/polybar/discharge.sh`
`
```zsh
#! /bin/bash

a=$(upower -i `upower -e | grep 'BAT'` |grep "time to")
echo $a
```
![](https://i.imgur.com/w5PMEHr.png)
It's ugly, I will change the text later.




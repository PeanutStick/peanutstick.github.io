---
layout: post
title:  Setup_Xfce_with_i3
date:   2021-12-23 22:17:00 -0600
categories: Os
tags: Linux
---

I have a basic install of Xfce.
![](https://i.imgur.com/PyLz5tn.jpg)

I also can open a new session with only i3.
![]()

# Install nitrogen
`sudo pacman -S nitrogen`
It will repalce the background, for the wallpaper.
I don't know what I'm currently using for my current setup with i3,
but it's not nitrogen.

# Remove Xfce wm
Go to session & startup.
on the second tab you have to set `i3` at startup.
on the third tab, select never for `xfwm4` and `xfcedesktop`. 
And remove all shortcuts in xfce, because you gonna have some problems with thoses of i3.
And reboot.

# And voila
![](https://i.imgur.com/23pApqs.png)





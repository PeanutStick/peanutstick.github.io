---
layout: post
title: Replacing xfce4 bar by Polybar
date:   2022-01-11 17:57:59 -0600
categories: Os
tags: Linux
---
# Why 

Because I don't use the menu of xfce and the the display of my desktops is a bit ugly.
![](https://i.imgur.com/xPw69k0.png)
I have `dmenu` to run everythings I need.
I would like to use `pywal` on my `polybar`.

It can look a bit stupid because if I don't use the xfce4 bar and WM why do I still keep it?
It's because with my last setup (I3 + polybar) I had 2 hours of battery max, now I'm at 6.

# How

## Install Polybar 

I already have polybar.

```zsh
sh ~/.config/polybar/launch.sh 
```
If yiu have an error you have to recompile polybar.

This is how it's look like.
![](https://i.imgur.com/htPX5vB.png)
This is the instalation guide.
https://wiki.archlinux.org/title/Polybar#Installation


## Set polybar at startup
I use BSPWM so I have to edit my bspwmrc.
`~/.config/bspwm/bspwmrc`
```zsh
$HOME/.config/polybar/launch.sh
```
For me it's not working, I have to lunch it via xfce4.
I have to open `xfce4 setting manager` and `sessions and startup`.



## Config polybar

### wal
I want it to work with wal, if I change my walpaper every thing have to change.

This is how you can get the colors, it's not needed here..
```zsh
source "$HOME/.cache/wal/colors.sh"
echo $color0
```
`#596398`

instead of `background = #222` you have to set `background = ${xrdb:color2}`
I'm testing it with `polybar example`.

```
background-alt = ${xrdb:color2}
background = ${xrdb:color1}
foreground = ${xrdb:color0}f
foreground-alt = ${xrdb:color0}
primary = ${xrdb:color5}
secondary = ${xrdb:color5}
alert = ${xrdb:color7}
```
### Shape

## Remove xfce4 panel
I don't see how I can disable it at startup.
I can add other panels but I can't remove the last one,
so I made an empty and tinny panel with 100% opacity, he is in the corner of my screen.
I feel like I'm not doing it right xD.
### Isues

After a reboot, et was broken, dmenu, bspwm, nitrogen and plybar was gone.
And the shortcuts don't work too, I had to open a terminal to fix it.
Right click on the desktop > terminal > kill xfce4wm > statr bspwm.
This is how it's look like.
![](https://i.imgur.com/ZoN960v.png)

I fix my problem with polybar, I had something wrong in the config file.
now I have to fix netrogen.
I'm gonna reboot to see if it really work.
And still not working.
### bspwm dmenu shortcuts

Right click on the desktop > terminal > kill xfce4wm > statr bspwm.
dmenu and the shortcuts start with dmenu.

### nitrogen
I had to kill xfce4 desktop, now nitrogen can run

### Picom

The blur was gone too, I have to set in at startup.

### polybar

```zsh
polybar: error while loading shared libraries: libjsoncpp.so.24: cannot open shared object file: No such file or directory
```
I have to rebuild it.
```zsh
git clone https://aur.archlinux.org/polybar.git && cd polybar && makepkg -si
```
This is how it should look like. 
![](https://i.imgur.com/eU8LCln.jpg)
I'm gonna reboot.
And polybar don't want to start, I should start it with an another startup file.
And if it really don't work I'm gonna create a service and enable it.
Like the arch wiki said I had do modify the bspwmrc.

`~.config/bspwm/bspwmrc`
```zsh
$HOME/.config/polybar/launch.sh
```
### Minor Problems

Now I have an another problem, it's working fine but it's ugly.
nitrogen restore the same wallpaper, no matter what, and the polybar don't fetch the colors.
![](https://i.imgur.com/hQZEKbW.jpg)
I've created a commmand to set a random wallpaper.

`/usr/local/bin/rwalp`
```zsh
ran=$(find ~/Pictures -type f \( -iname \*.jpg -o -iname \*.png \) | shuf -n 1)
nitrogen --set-scaled "$ran"
wal -i "$ran"
pywalfox update
```
Now I have to set rwalp at startup, I'm gonna use the bspwmrc file.
The background don't change, but everything else is ok, and I can change the background if I reload bspwm with CTRL+ALR+r, I love it.






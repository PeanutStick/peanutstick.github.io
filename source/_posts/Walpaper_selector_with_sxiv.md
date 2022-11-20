---
layout: post
title: Wallpaper selector with sxiv
date:   2022-01-21 19:35:56 -0600
categories: Tips
tags: Linux
---
# What it does

It will change my walpaper and colors of my polybar theme and firefox.

# What do I use

nitrogen for the wallpaper,
pywal for my color scheme,
pywal for for the colors of firefox.

# Walp
I have a script to change everything but without sxiv.
You have to copy this code somewhere in your path, mine is in `/usr/local/bin/walp`.
You can type `echo $PATH` to see more options.

```zsh
➜  ~ cat $(where walp)
nitrogen --set-scaled "$1"
wal -i "$1"
pywalfox update
```
# sxiv

You can type this command to see the images in your current folder.
```zsh
sxiv -t -a * 
```
I'm gonna use this one to select my future walpaper.

```zsh
walp $(sxiv -t -a -o ~/Pictures/*)
```
You have to select the walpaper with the key `m` and press `q` to leave.
It's working well but I was to start it via dmenu because I'm too lazy to type the command.

# dmenu

I like the command walp, I will modify it.
```zsh
if [ -z $1];
then
       walp $(sxiv -t -a -o ~/Pictures/*)
fi 
nitrogen --set-scaled "$1"
wal -i "$1"
pywalfox update
```
![](https://i.ibb.co/MsCcW1c/test3-1.gif)



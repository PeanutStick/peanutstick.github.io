---
layout: post
title:  How do I setup nitrogen & pywal
date:   2021-12-24 20:15:52 -0600
categories: Os
tags: Linux
---

# What I want
I want to change my background and the color of my terminal with one command.

# Requirement
## Nitrogen
`sudo pacman -S nitrogen`
## pyWal
You can found it on the AUR.
I'm gonna use yay to install it.
you can also use wal for discord and spotify.
`yay -Ss pywal`
Let's install it.
`yay -S python-pywal`

# Alias
## command for nitrogent
`nitrogen --set-auto image.png`

## Command for pywal
`wal -i image.png`

## Script it
The name of the alias will be `walp`.
In my .zshrc
```zsh
walp(){
nitrogen --set-auto "$1"
wal -i "$1"
}
```
usage:
walp image.jpg
![](https://i.imgur.com/eXQTxb7.jpg)

![](https://i.imgur.com/tncVkzY.png)

# Set nitrogen at startup
I'm going to edit `.config/i3/config` because I'm using i3
```zsh
exec --no-startup-id nitrogen --restore
```

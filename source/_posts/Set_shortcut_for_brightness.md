---
layout: post
title:  Set shortcut with i3 for brightness.
date:   2021-07-31 12:23:52 -0600
categories: Os
tags: Linux
---
# Set shortcut with i3 for brightness.
## Install blight for AUR
```
git clone https://aur.archlinux.org/blight.git
cd blight
makepkg -si
```
The doc: [https://github.com/RPigott/blight](https://github.com/RPigott/blight)
## Set the shortcut
```
cd ~/.config/i3
vim config
```
I added this on this file:

```
bindsym $mod+F5 exec blight set -5%
bindsym $mod+F6 exec blight set +5%
```
Now reload I3 with mod+shift+r
Decrease brightness: mod+F5
Increase brightness: mod+F6


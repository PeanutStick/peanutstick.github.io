---
layout: post
title: Switch from i3 to bspwm
date:   2021-12-31 18:37:52 -0600
categories: Os
tags: Linux
---
# Why bspwm.
Because I had some issues with discord, it was slow and i3 is pretty old.
I wanted to try something else.

# How I installled bspwm.
## Install bspwm
```zsh
sudo pacman -S bspwm 
```
## Create the config files
```zsh
install -Dm755 /usr/share/doc/bspwm/examples/bspwmrc ~/.config/bspwm/bspwmrc
install -Dm644 /usr/share/doc/bspwm/examples/sxhkdrc ~/.config/sxhkd/sxhkdrc
```
sxhkdrc is used to create the shortcuts.

I had to launch bspwm and disable i3.
I've done it in the xfce pannel instead of '/etc/X11/xinit/xinitrc', because it was not working and I like how I can do it with xfce.

# Sxhkd config
``` 
#
# wm independent hotkeys
#

# terminal emulator
super + Return
	kitty

# program launcher
super + d
	dmenu_run

# make sxhkd reload its configuration files:
super + Escape
	pkill -USR1 -x sxhkd

#
# bspwm hotkeys
#

# quit/restart bspwm
super + alt + {q,r}
	bspc {quit,wm -r}

# close and kill
super + {_,shift + }w
	bspc node -{c,k}

# alternate between the tiled and monocle layout
super + m
	bspc desktop -l next

# send the newest marked node to the newest preselected node
super + y
	bspc node newest.marked.local -n newest.!automatic.local

# swap the current node and the biggest window

#
# state/flags
#

# set the window state
super + {t,shift + t,s,f}
	bspc node -t {tiled,pseudo_tiled,floating,fullscreen}


#
# focus/swap
#

# focus the node in the given direction
super + {_,shift + }{Left,Down,Up,Right}
	bspc node -{f,s} {west,south,north,east}

# focus the node for the given path jump
super + {p,b,comma,period}
	bspc node -f @{parent,brother,first,second}



# focus the last node/desktop
super + {grave,Tab}
	bspc {node,desktop} -f last



super + {_,shift + }{1-9,0}
	bspc {node -d, desktop -f} '^{1-9,10}'

#super + {_,shift + }{1-9,0}
#	bspc node -d '^{1-9,10}'


super + {ampersand,eacute,quotedbl,apostrophe,parenleft,section,egrave,exclam,ccedilla}
        bspc desktop -f '^{1-9,10}'
#	bscp desktop -f {1-9,10}

alt + shift + {Left,Right,Up,Down}
	dir={west,east,north,south}; \
	bspc node -s "$dir.local" --follow \
	    || bspc node -m "$dir" --follow

#
# preselect
#

# preselect the direction
super + ctrl + {Left,Down,Up,Right}
	bspc node -p {west,south,north,east}

# preselect the ratio
super + ctrl + {1-9}
	bspc node -o 0.{1-9}

# cancel the preselection for the focused node
super + ctrl + space
	bspc node -p cancel


#
# move/resize
#

# expand a window by moving one of its side outward
super + alt + {Left,Down,Up,Right}
	bspc node -z {Left -20 0,bottom 0 20,top 0 -20,right 20 0}

# contract a window by moving one of its side inward
super + alt + shift + {Left,Down,Up,Right}
	bspc node -z {right -20 0,top 0 20,bottom 0 -20,left 20 0}

# move a floating window
super + {Left,Down,Up,Right}
	bspc node -v {-20 0,0 20,0 -20,20 0}

```
# Bspwm config

```
#! /bin/sh

pgrep -x sxhkd > /dev/null || sxhkd &

bspc monitor -d 1 2 3 4 5 

bspc config border_width         2
bspc config window_gap          12

bspc config split_ratio          0.52
bspc config borderless_monocle   true
bspc config gapless_monocle      true

bspc rule -a Gimp desktop='^8' state=floating follow=on
bspc rule -a Chromium desktop='^2'
bspc rule -a mplayer2 state=floating
bspc rule -a Kupfer.py focus=on
bspc rule -a Screenkey manage=off

```






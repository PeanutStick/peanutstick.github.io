---
layout: post
title:  Set Polybar on 2 screens
date:   2021-08-07 20:15:52 -0600
categories: Os
tags: Linux
---
# Config
In `~/.config/polybar/config`
Wat is your bar, by default it\'s `exemple`, but mine is `Bar`.
You should add monitor.
```
[bar/Bar]

monitor = ${env:MONITOR:} 
```
# launch.sh
In `~/.config/polybar/launch.sh`
Add this.

```
if type "xrandr"; then
  for m in $(xrandr --query | grep " connected" | cut -d" " -f1); do
    MONITOR=$m polybar --reload Bar &
  done
else
  polybar --reload Bar &
fi
```
To avoid 2 bars on the main screen comment the line  you used to launch it.
```
#polybar Bar 2>&1 | tee -a /tmp/polybar1.log & disown
```



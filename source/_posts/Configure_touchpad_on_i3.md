---
layout: post
title: Configure the touchpad [I3]
date:   2021-07-15 18:16:52 -0600
categories: Os
tags: Linux
---
# Configure the touchpad [I3]
## Config:
Let's use xorg server and create the file:
`sudo mkdir /etc/X11/xorg.conf.d/90-touchpad.conf`
Now edit it:
```
Section "InputClass"
        Identifier "touchpad"
        MatchIsTouchpad "on"
        Driver "libinput"
        Option "Tapping" "on"
	Option "TappingButtonMap" "lrm"   # lrm = right click lmr = middle click
	Option "NaturalScrolling" "on"
        Option "ScrollMethod" "twofinger" # 2 fingers to scroll
EndSection
```

And reboot.











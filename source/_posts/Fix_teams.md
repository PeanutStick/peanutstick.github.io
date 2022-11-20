---
layout: post
title:  Fix my spyware is stuck in the login loop (Microsoft teams) 
date:   2021-12-27 14:15:52 -0600
categories: Fix
tags: Linux
---

# Problem
My problem is in the title, I can't use teams because I'm stuck in the login loop.
It's the same with the apps From the AUR, teams and teams for linux.

# Solution 1

Clear the config file. 
Not working for me. 
```zsh
rm -r ~/.config/teams-for-linux
rm -r ~/.config/Microsoft/ 
```
# Try via the browser
Nop still the same

# Change the date

I's the solution for me, it's weird because I had the right date/time.
On discord (an another spyware) the time was wrong too.

The NTP was inactive.
```zsh
timedatectl set-ntp true
systemctl start systemd-timesyncd.service
systemctl enable systemd-timesyncd.service
timedatectl set-timezone Europe/Paris 
```
I set Europe/Paris because I'm french.



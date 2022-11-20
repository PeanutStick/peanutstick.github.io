---
layout: post
title:  Run non steam game with proton on lutris
date:   2021-09-14 20:40:52 -0600
categories: Os
tags: Linux, Gaming
---

# Install Steam and proton.
On steam:
Setings > Steam Play and toggle the option “Enable Steam Play for Supported Titles”.

Now you can select the last version of Proton and download it via steam.

# Copy Proton folder.

You have to copy the proton folder:
`.steam/root/steamapps/common/`
And past it wher you want, for me it's here:
`.local/share/lutris/runners/wine/`

It's look like this for me:
```cp .steam/root/steamapps/common/Proton\ 6.3 .local/share/lutris/runners/wine/Proton\ 6.3 -R```
# Configure lutris

In the Runners section eddit wine:

## Runner options

Wine version: Custom
Custom Wine executable: `/home/username/.local/share/lutris/runners/Proton 6.3/dist/bin/wine`
## System options

Disable lutris Runtime: `Yes`
Run in a terminal: `Yes`
Environment variables: `Add`
Key: `STEAM_COMPAT_DATA_PATH`
Value: `/path/to/your/games`
Pre-launch script: `/home/peanutstick/.local/share/lutris/runners/Proton 6.3/proton`
Wait for pre-launch script completion: `Yes`
Save

Now your games who run with wine will use proton.











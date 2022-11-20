---
layout: post
title: set up smb
date:   2022-01-15 22:01:27 -0600
categories: Service
tags: Linux
---
# Installation
sudo pacman -S samba gvfs gvfs-smb # install relevant samba packages

sudo nano `/etc/samba/smb.conf` (if it's empty get a default smb.conf from Samba Git Repository and paste it into.

Under [global] set:
workgroup = WORKGROUP

sudo systemctl start smbd nmbd # start samba now

# Clients
## Nautilus
It's easy to use, you have to click to 'another location' and connect to a server:
`smb://IP`

You gonna see your shared files.
Now if you want to browse them via CLI:
`/run/user/<username>/gvfs/<share point>`

## smbclient

```zsh
smbclient -L <server> # To list the shares
smbclient //serveur/partage # To connect to the share
```




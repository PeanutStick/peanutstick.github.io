---
layout: post
title:  "Set up port knocking server to hide an open port"
date:   2021-10-30 17:15:52 -0600
categories: Security
tags: Linux
---
# What is Port Knocking ?
Port knocking is a simple method to grant remote access without leaving a port constantly open. 
This preserves your server from port scanning and script kiddie attacks.
# Server Setup
## Install the package
Debian:    
```
apt install knockd
``` 
## Configuration
`/etc/knockd.conf`

I want to hide the ssh.    
You have to change the sequence, because the script kiddies will try this sequence first.    

```
[options]
      logfile = /var/log/knockd.log

[SSH]
      sequence    = 7000,8000,9000
      seq_timeout = 20
      command     = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
      tcpflags    = syn
      cmd_timeout   = 10
      stop_command  = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
```
You have some exemple here.    

[ubuntu.com](https://help.ubuntu.com/community/PortKnocking)
## Enable the deamon
`/etc/default/knockd`
Uncomment the START_KNOCKD=1 line to enable the daemon.    
```
################################################ 
# 
# knockd's default file, for generic sys config 
# 
################################################ 

# control if we start knockd at init or not 
# 1 = start 
# anything else = don't start 
START_KNOCKD=1 

# command line options 
KNOCKD_OPTS="-i eth0" 
```
# Client side
## Linux

You should install knockd on the client too.    
```knock -v 192.168.1.250 7000 8000 9000```

## Windows

Download this: [SourceForge-KnockKnock](https://sourceforge.net/projects/knockknock/)    
You sould read the README.txt    
KnockKnock.zip - archive containing all files    
|- Shortcut to KnockKnock - Shortcut to knock client    
|    
|- Shortcut to KnockMonitor - Shortcut to knock server    

---
layout: post
title:  "Set up a firewall with ufw"
date:   2021-11-01 13:05:52 -0600
categories:  Security
tags:  Security
---

# Install

```apt install ufw```

# Config

Doc: [https://help.ubuntu.com/community/UFW](ubuntu.com)

The best things to do is disable all the port and enable only what you need.    
```sudo ufw default deny```    
Be carefull, if you are connected via ssh you should enable the port first.    
```sudo ufw allow 22```    
It's 22 for the exemple, but you have to change the port to avoid attacks from Chinese bots.    
    
To see your open port you can use netstat.    
```sudo netstat -tulpn```    
And then allow what you need.    
    
If the config is done you can enable the service.    
```sudo ufw enable```    




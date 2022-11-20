---
layout: post
title:  CTF Tr0ll
date:   2021-10-01 17:15:52 -0600
categories: CTF
tags: Linux 
---

# Discovery
[https://www.vulnhub.com/entry/tr0ll-1,100/](https://www.vulnhub.com/entry/tr0ll-1,100/)

Tr0ll was inspired by the constant trolling of the machines within the `OSCP labs`.
The goal is simple, gain root and get `Proof.txt` from the `/root directory`.
Not for the easily frustrated! Fair warning, there be trolls ahead!
Difficulty: Beginner ; Type: boot2root

# Scanning
## netdiscover
sudo netdiscover
![](https://i.imgur.com/eXGJ3Sq.png)

## nmap
nmap -sV -p- 192.168.0.24
![](https://i.imgur.com/VNTKVkg.png)

## Port 80
![](https://i.imgur.com/PdbHzaR.png)
There is nothing intresting in the source code of this page.
Let\'s see what is in the `robots.txt`.
```
User-agent:*
Disallow: /secret

```
![](https://i.imgur.com/PMu5eIT.png)
...yes

## Dirbuster
![](https://i.imgur.com/VIgbvJ5.png)
Too many errors for me, I will search for something else.

## Nikto
![](https://i.imgur.com/DtyYOiE.png)
Nothing intresting on the port 80, I already know for the secret and robot.txt.










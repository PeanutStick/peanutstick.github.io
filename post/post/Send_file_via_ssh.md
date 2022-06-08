---
layout: post
title:  Send a file via ssh
date:   2021-07-13 20:15:52 -0600
categories: Service
tags: Linux
---
# Send a file via ssh
## Command
The file you want to send:
```/var/www/html/index.html```

The destination:
``` user@IP:/home/peanutstick/Document/index.html```
So the command will be:
```scp /var/www/html/index.html user@IP:/home/peanutstick/index.html```

You can add ```-P```  to specify the port.
It take too much time to write and if it's always the same destination directory you can write a fonction.

## Fonction
In the ```.bashrc```.
```
send() {
scp -P 8765 /home/peanutstick/Documents/markdown/"$1" 
peanutstick@65.21.51.64:/var/www/html/peanutstick.com/source/_posts/"$1"
}
```
Usage:
```send My_post.md```
You can rename send like you want.
The $1 will be My_post.md.

I use it now because I was writing my post directly from the VPS because I'm too lazy to open filezilla and click click everywhere.
The problem by writing it directly on the VPS via ssh is if you are incative too much time, you will loose the connection and the work will be not saved.  






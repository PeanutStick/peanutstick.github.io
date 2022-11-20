---
layout: post
title: CTF commands and files
date:   2022-01-17 15:55:38 -0600
categories: CTF
tags: Linux
---
# Revers shell
## Python
https://docs.j7k6.org/wordpress-malicious-plugin-reverse-shell-metasploit/
## Shell
https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php

# Spawn tty
https://netsec.ws/?p=337
```
python -c 'import pty; pty.spawn("/bin/sh")'
```
```zsh
echo os.system('/bin/bash')
```
```zsh
/bin/sh -i
```
```zsh
perl —e 'exec "/bin/sh";'
```
```zsh
perl: exec "/bin/sh";
```
```zsh
ruby: exec "/bin/sh"
```
```zsh
lua: os.execute('/bin/sh')
```
```zsh
(From within IRB)
```
```zsh
exec "/bin/sh"
```
```zsh
(From within vi)
```
```zsh
:!bash
```
```zsh
(From within vi)
```
```zsh
:set shell=/bin/bash:shell
```
```zsh
(From within nmap)
```
```zsh
!sh
```
# Python server
```zsh
python -m SimpleHTTPServer 80
```
# Enumeration
## running process
https://github.com/DominicBreuker/pspy/releases/tag/v1.2.0

## linenum
https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

## Unix privesc
https://pentestmonkey.net/tools/audit/unix-privesc-check

## Linprivchecker.py
https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py
## Websites
'https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_-_linux.html'
 
## Commands
```zsh
cat /home/*/.bash_his*   
cat /etc/passwd
```

# Privesc
```zsh
sudo -l
```
```zsh
find / -perm -u=s -type f 2>/dev/null
```
```zsh
find / -user root -perm -4000 -print 2>/dev/null
```
```zsh
find / -user root -perm -4000 -exec ls -ldb {} \;
```
```zsh
find . -perm /4000 
find . -perm /2000 
find . -perm /6000 
```
# directory traversal
https://www.netspi.com/blog/technical/web-application-penetration-testing/directory-traversal-file-inclusion-proc-file-system/
```zsh
/proc/sched_debug
```
```zsh
/proc/mounts
```
```zsh
/proc/net/arp
```
```zsh
/proc/net/route
```
```zsh
/proc/net/tcp and /proc/net/udp
```
```zsh
/proc/net/fib_trie
```
```zsh
/proc/version
```
```zsh
/proc/[PID]/cmdline
```
```zsh
/proc/[PID]/environ
```
```zsh
/proc/[PID]/cwd
```
```zsh
/proc/[PID]/fd/[#]
```






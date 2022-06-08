---
layout: post
title:  "wine: could not load kernel32.dll, status c0000135"
date:   2021-07-11 10:50:52 -0600
categories: Fix
tags: Linux
---
# wine: could not load kernel32.dll, status c0000135
The wine and winecfg command wasn't working, darksouls on steam wasn\'t able to start.
So I tryed to fix it and there is my solution.
```sudo rm ~/.wine -R```
And now run ```winecfg```




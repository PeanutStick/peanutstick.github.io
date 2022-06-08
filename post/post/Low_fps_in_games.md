---
layout: post
title:  My games are unplayable
date:   2021-08-01 11:15:52 -0600
categories: Fix
tags: Linux
---
# The problem
My games was unplayable of very slow.
My NVIDIA GPU was at 100% but seam like he burn resources for nothing.
I used prime-run to run them, then I change for optimus-manager but it was the same.

# The solution
Swith from NVIDIA to hybrid.
```
optimus-manager --switch hybrid
```
Set hybrid at startup
```
optimus-manager --set-startup hybrid
```






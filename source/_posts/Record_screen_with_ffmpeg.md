---
layout: post
title: Record screen with ffmpeg
date:   2022-01-18 18:56:09 -0600
categories: Tips
tags: Linux
---
ffmpeg -video_size 1920x1080 -framerate 60 / # Resolution + framerate
-f x11grab -i :0.0+0,0 / # screen
-f pulse -i alsa_output.pci-0000_00_1f.3.analog-stereo.monitor test3.mkv # Audio

You can have the audio input with:
```zsh
pacmd list-sources  
```



```zsh
ffmpeg -video_size 1920x1080 -framerate 60 -f x11grab -i :0.0+0,0 -f pulse -i alsa_output.pci-0000_00_1f.3.analog-stereo.monitor test3.mkv  
```

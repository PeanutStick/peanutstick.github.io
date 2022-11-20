---
layout: post
title:  Create a locale stream
date:   2022-01-09 12:18:22 -0600
categories: Service
tags: Linux
---
# Why

Because we can't use the music bot on discord anymore.
And a youtuber released a video yesterday where he show how to steal a stream on youtube, it's Bugswriter.
I don't want to steal a stream, I just want to use ffmpeg like a chad and play with it.

# How

I want to use `ffmpeg` without ffserver, because ffserver has been removed on 2018-01-06. 
And a webserver, I tried with python SimpleHTTPServer but I can't use it because I have the error 501.
`message Unsupported method ('POST')`
SO I'm gonna use `nginx`

# What I'v done

Create the web server.
Upload the stream:

```zsh
ffmpeg -i weeb_music.webm -f mp3 /usr/share/nginx/html/stream
```
I don't stream to http://localhost:80/stream because it's not working well.
And if I can do it, everyone can do it too and it's not good.
Maybe it's possible to upload a revershell via ffmpeg and I will be fucked.
So you can only read via the web server.

# Problems

When I start the stream, he upload it very fast (50x).
The output is not a stream but juste the file.
![](https://i.imgur.com/qaqvaU3.png)

If the file is loaded at 100% in my browser he will stop the download.
But if it's the half of the music the browser wont load the rest of the stream. 

# What I should try

Reduce the speed.
Don't erase the buffer because youtube don't do it to,maybe I can create an index.html and embed the stream.
Don't use a file but a real stream, like the output of my browser or a video/music player.
Use the stream of MCP server??
I have to open the ports of my box, not the 80 or 8080 because of the bots.
Read the stream with vlc and ffplayer.




























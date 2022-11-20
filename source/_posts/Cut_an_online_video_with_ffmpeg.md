---
layout: post
title: Cut an online video with ffmpeg
date:   2022-01-10 20:15:52 -0600
categories: Other
tags: Linux
---
# Why ffmpeg

This tool can decode a video and re-encode it. 
You can remove the audio or the video, change the codec or use it to stream a file.
![](https://i.imgur.com/E6nBYs4.png)

# Trick
You can input a youtube video without downloading it, to do this you need `youtube-dl`.
```zsh
youtube-dl -f best --get-url https://www.youtube.com/watch?v=H8w_Q57RQJc

https://rr3---sn-n4g-ouxs.googlevideo.com/videoplayback?expire=1641865369&ei=OYzcYZ7yKNuMxN8P7feTIA&ip=93.23.130.72&id=o-AO5ZLYd00XMxzT1PjKh0Grtrh-mtnzmQcTys0s1Fx-UI&itag=18&source=youtube&requiressl=yes&mh=Q-&mm=31%2C29&mn=sn-n4g-ouxs%2Csn-n4g-jqbed&ms=au%2Crdu&mv=m&mvi=3&pl=21&initcwndbps=1763750&vprv=1&mime=video%2Fmp4&ns=R-N0c7jT-8ZOyFPbWVfuYpAG&gir=yes&clen=139964871&ratebypass=yes&dur=6538.507&lmt=1540303910458790&mt=1641842946&fvip=5&fexp=24001373%2C24007246&c=WEB&txp=5531432&n=tXjNIa-Hi2X8YEz&sparams=expire%2Cei%2Cip%2Cid%2Citag%2Csource%2Crequiressl%2Cvprv%2Cmime%2Cns%2Cgir%2Cclen%2Cratebypass%2Cdur%2Clmt&sig=AOq0QJ8wRgIhAPRj1ix2t8yS_B3M3QQaY0dCz4l-AaDkvF4r-sutJ50_AiEAk_E3dott1d4QWuDUqQkcSPtBRzIQi-rcNVeOV4Fg53E%3D&lsparams=mh%2Cmm%2Cmn%2Cms%2Cmv%2Cmvi%2Cpl%2Cinitcwndbps&lsig=AG3C_xAwRQIgJInz5xNfosh2ocbFn41qMlAjCzdgTG0Xu3FjciwFCTkCIQCl7Jn3sVBFAmEnfXFmdH4k35tRXbMBB406wbAnyOLElQ%3D%3D
```
If you click on the big link you can see the video without the youtube things.

now ffmpeg can read the video with this link.

The command to get the youtube link is too big, so I'm gonna wrap it into a variable.

```zsh
link=$(youtube-dl -f best --get-url https://www.youtube.com/watch?v=H8w_Q57RQJc)

ffmpeg -i $link output.mp3
``` 
# My idea

I wanted to download every song in one youtube video, it was a pretty bad idea...
For exemple I wanted the full Album of VA-11 HALL-A.
![](https://i.ibb.co/Vx6gzhy/VA-11yt.png)
I had to enter every title and every time code one by one.
I made the script in one afternoon and I took some time to understand how the programe work.

So, to download a specific moment of the video you have to use `-ss` with `ffmpeg` to indicate when it start,
and use `t` for the duration or `to` for the next time code.

```zsh
ffmpeg -i "$vid" \
	-ss 0 -to 120 "output.mp3"
```
So with it you can pick the song you like in theres lists on youtube.

# My script
This is my script, you have to guess how to use it, lol.
It's made by a pro so it will be easy ( ͡° ͜ʖ ͡°).
```zsh
vid=$(youtube-dl -f best --get-url $1)
listtime=("0")
listtitle=()
i=0
while true
do
	echo -e "The end: "
	read time
	echo -e "The title: "
	read title
	title=str="${title// /_}"
	listtime+=("$time")
	listtitle+=("$title")
	echo -e "1: Continue "
	echo -e "2: Start download"
	echo -n ": "
	read choix	
case $choix in
		1)
		clear
		;;
		2)
		echo -n "bye"
		clear
		break
		;;	
esac
done
i=0
while [ $i -ne ${#listtitle[@]} ]
do
	title=${listtitle[$i]} 
	start=${listtime[$i]}
	((i++))
	end=${listtime[$i]}
	title=${title:4}
	ffmpeg -i "$vid" \
		-ss $start -to $end "${title}.mp3"
done
```
# End
Don't do what I've done, it's useless, it's faster to download the musics on 1337 or somewhere else.
you can use it if you like a song and want to pick this one amoug every songs.




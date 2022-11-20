---
layout: post
title: Script to create my article page.
date:   2022-01-11 15:30:34 -0600
categories: Programing
tags: Linux
---
# Why
I wanted to automate the creation of the base page for my articles.
My script ask for the `title` and the `categorie` only.
He fill the date automaticly and that's good, because sometimes I don't change it.

# Script
It's basic.

```zsh
#!/bin/bash
items=("CTF" "Fix" "Os" "Other" "Programing" "Security" "Service" "Tips")
i=1
date=$(date '+%Y-%m-%d %H:%M:%S')
echo -n "Title: "
read title
echo $title
titlemd=${title// /_}.md
clear
for elements in "${items[@]}"
do
	echo "$i) $elements"
	((i++))
done
echo -e "What is the categorie?"
echo -n ": "
read choix
#echo ${items[$choix-1]}
#echo $date

echo "---" > $titlemd
echo "layout: post" >> $titlemd
echo "title: $title" >> $titlemd
echo "date:   $date -0600" >> $titlemd
echo "categories: ${items[$choix-1]}" >> $titlemd
echo "tags: Linux" >> $titlemd
echo "---" >> $titlemd
```



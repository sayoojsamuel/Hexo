---
title: cat flag
tags: 'Troll, bash, cat flag, permissions'
categories:
  - Writeups
date: 2018-12-08 20:34:34
---
Writeup from hxp CTF'18
# hxp CTF 2018: cat flag

**Category:** Tro
**Challenge Points:** 25 
**Solves:** 230+

**Service:** nc 116.203.19.157 13370

**Description:**
```
Welcome to the hxp CTF 2018!

hxp would like to to reward loyal customers with a special offer. *

FREE SHELLS!!!

PS: Just cat flag and no hacks please :/

* Only while supplies last, limited to one per household
```

Oh, and a nice welcome message as soon as we connect! 

{% asset_img shell.png Welcomeshell %}

### The challenge

The objective was crystal clear. `cat flag`. The first thing I did was `ls -l` and got flag file with `222` permission. 

{% asset_img ls.png ls %}

Seemed pretty easy first, but the command were heavily limitted to 
>cat
>ls
>sh

Although basic `echo` and `cd` was working pretty well. 

### After hours
To my luck, `read` was the clandestine command I was looking for.
```bash
read -r f < flag
cat $f
```
Put that right into the shell and grab the flag. Simple :)
{% asset_img flag.png Flag %}

## Flag
>hxp{You_escaped_from_ANSI}

I still don't know why I spent hours going through the /tmp and /usr files. 


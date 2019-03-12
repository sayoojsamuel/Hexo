---
title: Marty0ska1
tags: 'Crypto, DLP, Pohlig-Hellman Algorithm, CTF'
categories:
  - Writeups
date: 2018-09-14 20:34:34
---
Writeup from SEC-T CTF'18
# SEC-T CTF 2018: Marty0ska1

**Category:** Crypto
**Challenge Points:** 51 
**Solves:** 60+

**Service:** nc crypto.sect.ctf.rocks 2222

Really bad they took down the website and services soon after the CTF, I coudn't complete my write up. So as you read, don't expect the flag, though the method remains the same.

``` bash
nc crypto.sect.ctf.rocks 2222
```

As you are connected to the service, you are given three numbers: `p`, `g`, and `g^x`. They demand `x`.

### Discrete Logarithmic Problem 
A stright DLP challenge. Things to notice, `g`=2, and `p` is *factorizable*.  Tada!! it is Pohlig-Hellman. Use a sage script to solve the challenge.
Let y=g^x

``` sage
R = IntegerModRing(p)
x = discrete_log(R(y), R(g))
print x
```
This will give the flag in less than two seconds. And that it.Submit x, to get the flag.

## Flag
**SECT{Ru$$ian_D0LLZ_h0lDs_TH3_S3cR3T}

### Further Reading
Tum CTF 2016 [tacos](http://mslc.ctf.su/wp/tum-ctf-2016-tacos-crypto-300/)


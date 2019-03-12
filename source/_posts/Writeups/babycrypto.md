---
title: babycrypto
tags: 'Xor, yeet, , CTF'
categories:
  - Writeups
date: 2018-09-16 22:34:34
---
Writeup from CSAW QUALS CTF'18
# CSAW CTF QUALS 2018: Babycrypto

**Category:** Crypto
**Challenge Points:** 50 
**Solves:** 295
**Description:**
```
yeeeeeeeeeeeeeeeeeeeeeeeeeeeeeet

single yeet yeeted with single yeet == 0

yeeet

what is yeet?

yeet is yeet
```
**Attachments:** ciphertext.txt
`PRO TIP`:Tell you what, the smallest crypto will always be XOR in CSAW!  And `a XOR a == 0` ==>
```
|single yeet yeeted with single yeet == 0
```
And that's the hint! to suggest it is a XOR challenge. So, we start with the attachment file, which includes a `base64` encoded ciphertext. 
Perform a single byte xor, and you'll find the flag in character 255.


``` bash
Leon is a programmer who aspires to create programs that help people do less. He wants to put automation first, and scalability alongside. He dreams of a world where the endless and the infinite become realities to mankind, and where the true value of life is preserved.flag{diffie-hellman-g0ph3rzraOY1Jal4cHaFY9SWRyAQ6aH}
```
## Complete Script
``` python
import string
def xor(a,b):
    return chr(ord(i)^ord(j) for i,j in zip(a,b))

ct = open('ciphertext.txt','rb').read().decode('base64')
for i in range(256):
    pt = xor(ct,chr(i)*len(ct))
    if all(c in string.printable for c in pt): print pt
```

## Flag
**flag{diffie-hellman-g0ph3rzraOY1Jal4cHaFY9SWRyAQ6aH}**


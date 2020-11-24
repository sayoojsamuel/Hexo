---
title: flatcrypto
tags: 'zlib, compression, CTF'
categories:
  - Writeups
date: 2018-09-16 23:34:34
---
Writeup from CSAW QUALS CTF'18
# CSAW CTF QUALS 2018: Flatcrypto

**Category:** Crypto
**Challenge Points:** 100 
**Solves:** 184
**Description:**
```
no logos or branding for this bug

Take your pick nc crypto.chal.csaw.io 8040 nc crypto.chal.csaw.io 8041 nc crypto.chal.csaw.io 8042 nc crypto.chal.csaw.io 8043

flag is not in flag format. flag is PROBLEM_KEY
```

**Attachments:** serv-distribute.py
``` python
import zlib
import os

from Crypto.Cipher import AES
from Crypto.Util import Counter

ENCRYPT_KEY = bytes.fromhex('0000000000000000000000000000000000000000000000000000000000000000')
# Determine this key.
# Character set: lowercase letters and underscore
PROBLEM_KEY = 'not_the_flag'

def encrypt(data, ctr):
    return AES.new(ENCRYPT_KEY, AES.MODE_CTR, counter=ctr).encrypt(zlib.compress(data))

    while True:
        f = input("Encrypting service\n")
        if len(f) < 20:
            continue
        enc = encrypt(bytes((PROBLEM_KEY + f).encode('utf-8')), Counter.new(64, prefix=os.urandom(8)))
        print("%s%s" %(enc, chr(len(enc))))
```

Again, it's another sipmple challenge based on AES_CTR.  For all those noobies, note that AES Counter mode does not have **`Padding`**.  And this challenge is specifically based on that. If we have a close look, it is the PROBLEM_KEY which we need to figure out. Now see that,
``` python
enc = encrypt(bytes((PROBLEM_KEY + f).encode('utf-8')),    |Counter.new(64, prefix=os.urandom(8)))
```
is where we target out exploit.  Our input `f` is appended to the PROBLEM_KEY, compressed and then encrypted without padding.   This zlib compression is a huge vuln, and we forge a custom input such that the plaintext is compressed before encryption. Thus, it results in reduced length of the ciphertext.

It is mentioned that the character set is lowercase ascii and underscore.
I have created a bruteforce script, in which, the brute starts with character 'a' till 'z' multiplied 20 times.
The length of the ciphertext decreases when the **brute_char** matches with the **last byte** of the PRODUCT_KEY. For the second last byte, we repeat the **brute_char+lastbyte** 20 times. Similarly the third last byte, which follows as
**brute_char+secondlast+lastbyte** 20 times.

This will eventually give us the flag, which is not in the flag format.

## Complete Script
``` python
from pwn import *
context.log_level="error"
import string
chars = string.ascii_lowercase + "_"
flag = ""    

for j in range(30):
    print "flag:",flag
    for i in chars:
        io = remote("crypto.chal.csaw.io",8040)
        print io.recv()
        io.sendline((i+flag)*20)
        print (i+flag)
        out = io.recvline()
        l = ord(out[-2])
        print "len=",l, i
        if l<35:                  #<== You have to edit this manually
            print i
            flag=i+flag
            break
        io.close()
                                                                                
```
## Flag
**crime_doesnt_have_a_logo**

### Plaid CTF Compression 
A note to all the readers, that this challenge was the exact copy of the 2013 Plaid CTF challenge - Compression.  Dosen't know why they have to give the exact same question.  Follow the writeup for the challenge for detailed explanation.

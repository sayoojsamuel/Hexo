---
title: tile-mate
tags: 'XOR, Python, Custom, Hash, Delhi, , Bsides, CTF'
categories:
  - Writeups
date: 2018-10-26 20:19:34
---
Writeup from Bsides CTF'18
# Bsides CTF 2018: Tile Mate

**Category:** Crypto
**Challenge Points:** 100 
**Solves:** 35
**Description:**
``` bash
David and Toni rides scooter. Everyone knows except them!
```

**Attachments:** tile_mate.tar.xz

This is an easy `XOR` challenge, with a custom plaintext substitution before the actual `XOR` process. 
Again, the attachment contains two files
1. encrypt.py
2. ci.pher.text

``` python
from itertools import cycle as scooter
from secret import FLAG, KEY
from hashlib import sha384

assert FLAG.islower()
assert len(KEY) == 10

def drive(Helmet, Petrol):
    return ''.join(chr(ord(David)^ord(Toni)) for David,Toni in zip(Helmet,scooter(Petrol)))
f = lambda x: sha384(x).digest()[(ord(x)+7)%48]
encrypted = drive(map(f,FLAG),KEY.decode('hex')).encode('hex')
open('ci.pher.text','wb').write(encrypted)
```

Hmm, it is pretty obvious `drive()` is the **Repeated Key `XOR`** function, although the function looks cool! (Atleast the 'Helmet,scooter(Petrol)' part :p)..
`f` being another function which returns the `x+7`th index character from the sha384 hash of `x`.
The ciphertext is simply the XOR(f(pt),key).

## Solution
``` python
assert FLAG.islower()
assert len(KEY) == 10
```
This suggests that the **flag** is composed of lowercase characters (digits and punctuation included) and the **key** length is 5 bytes (hex 10). 

#### Key Recovery
Since the flag format is `flag{`, we can recover the **key** by XORing f("`flag{`") with `ciphertext`.
``` python
hashed_flag = ''.join(map(f,"flag{"))
key = xor(hashed_flag,ct)
hashed_pt = xor(ct,key)
```

Now this `hashed_pt` is nothing but the hash of the `flag`.

#### Getting to the Flag
```
f = lambda x: sha384(x).digest()[(ord(x)+7)%48]
```
This custom hash is not reversible, but we can still create a map with all possible characters, cause that's just 90 possibilities ( Remember the lowercase?)
``` python
chars = string.ascii_lowercase+string.digits+string.punctuation
table = dict(zip(map(f,chars),chars))

flag=""
for i in hashed_pt: flag+=table[i]
print flag
```
The `table` contains the dict of hash, and character as key, value pair.
For characters in `hashed_pt`, we find the preimage of the hash from the table. And you hold the flag for the challenge, TADA!!!


## Complete Script
``` python
from hashlib import sha384
from itertools import cycle
import string

def xor(msg,key):
    return ''.join(chr(ord(i)^ord(j)) for i,j in zip(msg,cycle(key)))

f = lambda x: sha384(x).digest()[(ord(x)+7)%48]
ct = open('ci.pher.text','rb').read().decode('hex')
hashed_flag = ''.join(map(f,"flag{"))
key = xor(hashed_flag,ct)
hashed_pt = xor(ct,key)

chars = string.ascii_lowercase+string.digits+string.punctuation
table = dict(zip(map(f,chars),chars))

flag=""
for i in hashed_pt: flag+=table[i]
print flag
```

## Flag
**flag{cr1b_dr4g_w1th_u1tr4_c00l_sc00ter!}**

### Author's Note 
Okay, this is the second challenge I authored for BSides CTF'18 being organised at Delhi, the first one being [pyQueue](https://sayoojsamuel.github.io/2018/10/26/pyQueue/).

## 4 Line exploit
Just a show-off, 
``` python
from hashlib import sha384;from itertools import cycle;import string
xor,f,ct,chars=lambda (msg,key): ''.join(chr(ord(i)^ord(j)) for i,j in zip(msg,cycle(key))),lambda x: sha384(x).digest()[(ord(x)+7)%48],open('ci.pher.text','rb').read().decode('hex'),string.ascii_lowercase+string.digits+string.punctuation
hashed_pt,flag,table=xor((ct,xor((''.join(map(f,"flag{")),ct)))),"",dict(zip(map(f,chars),chars))
for i in hashed_pt: flag+=table[i];print flag
```

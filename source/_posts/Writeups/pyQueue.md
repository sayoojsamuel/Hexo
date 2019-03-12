---
title: pyQueue
tags: 'Bruteforce, Python, Queue, AES, ECB, Bsides, CTF'
categories:
  - Writeups
date: 2018-10-26 17:19:34
---
Writeup from Bsides CTF'18
# Bsides CTF 2018: pyQueue

**Category:** Crypto
**Challenge Points:** 150 
**Solves:** 43
**Description:**
``` bash
Easy Right?
```

**Attachments:** pyQueue.tar.xz

The attachment contains two files
1. encrypt.py
2. ci.pher.text

Let's analyse the `encrypt.py`
``` python

key = AES_Key()
ct=""
MAC = 0
for List in slice(FLAG):
    for block in List:
        cipher = AES.new(key.shuffle(), AES.MODE_ECB)
        ct+= cipher.encrypt(block)
        MAC ^= int(ct[-16:].encode('hex'),16)

MAC ^= int(key.shuffle().encode('hex'),16)

open("ci.pher.text",'wb').write(str(MAC) +":"+ ct.encode('hex'))

```
Well, it's an AES challenge on first sight. Three points to note, 
1. Each time an AES instance is created, a new **key** is being used as a resultant of `key.shuffle()`
2. **MAC** is generated as the `XOR` of all the `ct` blocks. At the end, **MAC** is xored with **key** after being shuffled for one last time.
``` python
MAC ^= int(key.shuffle().encode('hex'),16)
```
3. **ci.pher.text** is of the format *MAC:CipherText* 

A peek into the Key generation, 
``` python
class AES_Key:
    def __init__(self):
        self.key=list(os.urandom(16))

    def enqueue(self):
        self.key+=get_random_bytes(1)

    def dequeue(self):
        self.key=self.key[1:]

    def size(self):
        return len(self.key)

    def shuffle(self):
        self.dequeue()
        self.enqueue()
        assert self.size()==AES.block_size
        return "".join(self.key)

```
This reveals that `shuffle()` method just removes the first byte of the 16 byte **key** and appends a random byte at the end. The complete setup was implemented as a Queue :P. This makes it a whole lot easier to bruteforce!

## Solution
#### Key Recovery
Now, to recover the **key**, we just have to `XOR` the **MAC** with all the `ct` blocks. The resultant is the key.shuffled() value.
``` python
# Calculate key
last_key = int(MAC)
for i in ct:
    last_key ^= int(i.encode('hex'), 16)
last_key = long_to_bytes(last_key)
```

#### BruteForce
All you need to do now is to bruteforce over 256 possible character. That is, remove the last byte of `assumed key` and add the bruteforce character at the beginning. The **key** pair for the `plaintext` where most of the starting characters are printable is what we are looking for.
``` python
def brute():
    key = last_key
    pt = ""
    for i in range(2,-1,-1):
        key = key[:-1]
        for ch in range(256):
            cipher = AES.new(chr(ch)+key,AES.MODE_ECB)
            recover = cipher.decrypt(ct[i])
            if(all(c in printable for c in recover[:10])):
                pt = recover + pt
                key = chr(ch)+key
                break
    return pt
```


## Complete Script
``` python
from Crypto.Cipher import AES
from Crypto.Util.number import *
from string import printable

def split_by(msg,step):
    return [msg[i:i+step] for i in range(0,len(msg),16)]

data = open("ci.pher.text",'rb').read()
MAC,ct = data.split(":")

ct = split_by(ct.decode('hex'),16)

# Calculate key
last_key = int(MAC)
for i in ct:
    last_key ^= int(i.encode('hex'), 16)
last_key = long_to_bytes(last_key)

# Brute Joy!!!
key = last_key

def brute():
    key = last_key
    pt = ""
    for i in range(2,-1,-1):
        key = key[:-1]
        for ch in range(256):
            cipher = AES.new(chr(ch)+key,AES.MODE_ECB)
            recover = cipher.decrypt(ct[i])
            if(all(c in printable for c in recover[:10])):
                pt = recover + pt
                key = chr(ch)+key
                break
    return pt

print brute()
                    
```
## Flag
**flag{H4il_bUggi3s!_qu3u3_k3y_2_h0t_4_w4rmUp}**

### Author's Note 
I authored this challenge for Bsides CTF'18. I know that these kinda bruteforce challenges makes it hard for pro's to play with, but will be pretty interesting for the beginners. I'm just saying :p
Also, check out the writeup of [Tile-Mate](https://sayoojsamuel.github.io/2018/10/26/tile-mate/) (Crypto 100) challenge here.


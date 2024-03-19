---
title: HacksCTF 2024 Writeups
author: Aathish S
pubDatetime: 2024-01-20T16:55:12.000+00:00
slug: hacksctf2024
featured: false
draft: false
tags:
  - hacksctf2024
  - OSINT
  - cryptography
  - forensics
  - Aathish
  - miscellaneous
  - frenzyvjn

description: "This write-up covers the solutions to various challenges in the HacksCTF competition, including reverse engineering, cryptography, forensics, and more. Follow the steps, to unravel the mysteries and find the flags for each challenge."
---

# HacksCTF

## Author : Aathish S

[Linkedin](https://linkedin.com/in/aathishs/ "Linkedin") | [Github](https://github.com/Aathish04 "Github") | [Portfolio](https://aathish04.github.io/ "Portfolio")

# OhNo

Given text was pasted into a search engine that led to Spammmimic.
Spammimic was then used to decode the file.
We used the below decoder
https://www.spammimic.com/decode.cgi

**flag is `hacks{that_was_easY}`**

# Figurines

SHERLOCKED hint in the file name pushed us towards Sherlock holmes and arthur conan doyles books.

Helpful hints from maintainers pushed us towards the dancing men cipher, with which we decoded the flag.

We used the below tool
https://www.dcode.fr/dancing-men-cipher

We got mori4rity

flag is `hacks{mori4rity}`

# Breaking Bad

The Given PDF could be decrypted by entering the ip address calculated from the hints in the question.

On entering that, a string in base 85 is given, which when decoded, yeilds a string encrypted using the periodic table cipher.

On solving, the final flag is `hacks{UARERADIUM}`

# story-2

the values given were reminiscent of diffie hellman values p, g a and b. We were stuck at p since we expected it to be a prime, but it was 10.

An online calculator was used to find the value of the secret key, which was 4. The geogiraphical location 44,44 was a district in russia that turned out to be the key.

# Perfect Square

A python script was written to iteratively base64 decode the files:

```
coded_string = """"""

with open("input.txt") as f:
    coded_string = f.read()
import base64

while True:
    coded_string = base64.b64decode(coded_string)
    print(len(coded_string))
    a = input()
    if a == "p":
        print(coded_string)
    else:
        continue
```

**flag is `hacks{s0_many_64s} `**

# magic-magic

RUnning the file command on the .cad file informs us that it's actually xz compressed data. We change the file extension, and extract the folder to yield a flag.iso file. We run the file command again to realise that it is actually just ascii text, which yields: <+ohcAo(mg+DGm>3Zr\*E@r$.49f$lVA3k;a?Ys4h+\`MQP@8pqjDLD

Applying the base85 conversion, we obtain the sentence:
The flag is : `hacks{M3t@dAt&_man!pu(aTi*n}`

# SimpleCrypto

We extracted a file `ctf.txt` from the given jpg using steghide and the passphrase "8", which was infinity rotated.

The text inside ctf.txt was : pbbxa://lzqdm.owwotm.kwu/lzqdm/nwtlmza/1TIe3vvWzCzY7LV49fvLe6IMA8ydYID0A?cax=apizqvo

This was then decrypted with a caesar shift cipher with a shift of 8 to yield the url: https://drive.google.com/drive/folders/1LAw3nnOrUrQ7DN49xnDw6AES8qvQAV0S?usp=sharing

This led to a file unzipme.zip, and the password to it was partially revealed in the chall question "hostis**\***", which was bruteforced to yield hostisangry.

**flag is ```hacks{y0u_jus4_unz11p3d_me}```**

# jarr

This chall was solved by opening up the jar file using jadx-gui and viewing the asset files, which yielded a file named: asset.txt with the contents: `hacks{1nt3g3rs_G0ne_Wr0ng}`

# aussie

This challenge was a program written in aussieplusplus

one of our teammates had prior experience with the language and decoded it with an online tool at https://aussieplusplus.vercel.app/#code

# guesswhereami

the given ip address was looked up in shodan, and the open port had http running, so it was visited through a browser. THe page gave info on where exactly the camera was located (a town in victoria) and entering that as the flag worked.

# quickmath

A python script was written to perform the mathematical operations quickly and send over a response:

```py
from pwn import *
HOST = "0.cloud.chals.io"
PORT = 33312

def conn(): #connect to server
   r = remote(HOST, PORT)
   print(r.recvuntil(b''))
#    r.recvline()
   return r

r = conn()
count = 1

while True:
       question = r.recvline()
       print(str(question))
       break_question = question.lstrip(b"Solve: ").strip().split(b" ")
       break_question = [i.decode("utf-8") for i in break_question]
       first = int(break_question[0])
       second = int(break_question[2])
       print(first,second)
       if break_question[1] == '-':
           result = str(first - second)
           print(result)
           r.sendline(result.encode())

       if break_question[1] == '*':
           result = str(first * second)
           print(result)
           r.sendline(result.encode())

       if break_question[1] == '+':
           result = str(first + second)
           print(result)
           r.sendline(result.encode())

       if break_question[1] == '/':
           result = str(first / second)
           print(result)
           r.sendline(result.encode())
       print(r.recvline())
       count += 1
flag = r.recvline()
print(flag)

```

# India

Here, we were given a chips.jpeg image. So I tried running it thought google lens and image search and nothing useful came. So, I started looking at the language of those chips packets. So, it was Spanish. So ye, we came to a conclusion its either Argentina or Spain. Then organizers gave hint (hint : South America). So it was confirmed it was an South American Country i.e., Argentina. We then started bruteforcing Argentinian rivers. We couldn't get it right because our flag format was wrong. So after fixing it.
**flag is ` hacks{coloradoriver}`**

# Dinner with Salsa (Dance)

Here, we were given a phonebook.docx. So here, we tried calling all numbers and looked for patterns. Then we realized last 3 digits of few numbers end with 001, 002, 003, 004. We were stuck.We looked at the country codes, these looked like disk positions of mexican cipher .Then we tried concatenating the first seven digits of these number in order. and tried running it through mexican army cipher wheel with country code as disk positions.
We used the below decoder.
https://www.dcode.fr/mexican-army-cipher-wheel
And YES, we got the answer.
**flag is `hacks{TORTILLAHACKED}`**

# Trans-dimensional Message

![transdimessage](https://hackmd.io/_uploads/SylSsgYtT.png)
So here, we referred to previous writeups because I remember doing similar question earlier
Below is the CTF we referred.
https://samanthactf.medium.com/utctf-march-12-14-2021-84f60d372fdf

My friend used photopea
Filter -> Distort -> Shear option

AFTER FIXING
![image](https://hackmd.io/_uploads/HkwjTgYKT.png)

**flag is `hacks{th4t5_a_d00zy}`**

# gif gif (coming soon)

**flag is `hacks{P4YTm_KAR0}`**

### About Author

Aathish S

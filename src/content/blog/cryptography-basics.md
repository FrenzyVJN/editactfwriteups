---
title: Cryptography Basics
author: DrunkenCloud
pubDatetime: 2024-01-10T16:55:12.000+00:00
slug: cryptography-basics
featured: false
draft: false
tags:
  - editactf2023
  - cryptography
  - basics
  - drunkencloud
description:
  "Unlock the secrets of encryption with Cryptography! Delve into the art of coding and decoding messages. Learn how a simple understanding of underlying principles can crack even the most complex encryption schemes. Explore tools like CyberChef, a versatile platform with a graphical user interface, making cryptographic CTF challenges a breeze."
---
# **EditaCTF**

![EditaCTF](https://hackmd.io/_uploads/ry2OKgqHp.png)

# Cryptography

Cryptography is the reason we can use banking apps, transmit sensitive information over the web, and in general protect our privacy. However, a large part of CTFs is breaking widely used encryption schemes which are improperly implemented. The math may seem daunting, but more often than not, a simple understanding of the underlying principles will allow you to find flaws and crack the code.

The word “cryptography” technically means the art of writing codes. When it comes to digital forensics, it’s a method you can use to understand how data is constructed for your analysis.


---


We are going to introduce you to one of the greatest tools on cryptographic CTF challenges: [CyberChef](https://gchq.github.io/CyberChef)

CyberChef has many many types of encryption and encoding algorithms built within it and it all comes in a nice GUI way. Want to base64 encode some data? Drag and drop the 'To Base64' tab. Want to further encode it in hex? 'Drag and drop the 'To Hex'. You can mix and match to do some magic.

![cyberchef](https://hackmd.io/_uploads/HJOV6iOST.jpg)

You can also decode values using cyberchef with "decode <encoding_type>"  blocks. I would suggest messing around with many different encryptions and encodings like url encoding, bsae64, hex, ceasar cipher, vigenere  cipher, hashing programs, and many more.

Doing cryptography CTF challenges help build a solid foundation for many fields. You can get into blockchain, create cryptographic algorithms for banks, an encryption algorithm for network traffic and much more.

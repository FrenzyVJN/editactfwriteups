---
title: Forensics Basics
author: DrunkenCloud
pubDatetime: 2024-01-10T16:55:12.000+00:00
slug: forensics-basics
featured: false
draft: false
tags:
  - editactf2023
  - forensics
  - basics
description:
  "Uncover the digital footprints left by attackers through the fascinating world of Forensics. Explore techniques such as Metadata analysis, File Formats, Steganography, and Networking. Gain insights into blue teaming in cybersecurity, learning to analyze event logs, detect hidden data, and more. Forensics CTF challenges provide a gateway to a broader understanding of cybersecurity."
---
#  Forensics

Forensics is the art of recovering the digital footprints left by an attacker on a computer. There are plenty of methods to find data which seem to be deleted, changed, not stored or covertly stolen. Forensics is a very wide topic and I cant really cover it all here nor do I know enough to talk about it. So I shall only talk about some which we may use in this CTF here.
* Meta Data
* File Formats
* Steganography
* Networking (Wireshark)

Foresics CTFs are a wonderfull way to get into blue teaming in cyber security. While I am not talking about it, there way more types of forensics challenges. Like going through windows event logs, finding hacker traces on machine image files etc.

---

**Meta Data**

Meta data is bassically data about data. Different types of data (like image, documents, audio files etc) have different types of meta data. For an image it might be dimentions of image, capture time, dates, camera information etc, while for a audio it might artist name, recording date and title.
One tool that is extensively used to look at meta data is the **exiftool**. This is an inbuilt tool within Linux distros and even wsl. For example:

![cat](https://hackmd.io/_uploads/B1P0_cOrT.jpg)

This image of a very cool cat. Lets use exiftool to have a look at its meta data.

![exif_of_cat](https://hackmd.io/_uploads/SJjQFquB6.jpg)

The image above shows us the meta of this image. We can see the file type, access permissions, the dimentions etc. Pretty much most of what we want to would need to know about the image.
Sometimes CTFs will hide data within the meta data which you have to find the flag from.
Where does it get this data about data from? It will be discussed further in file formats.



---

**File Formates**

Have you ever wondered how your computer recognizes an image as an image and not a word document?

***File signatures!*** These are bytes within a file used to identify the format of the file. Generally they’re 2-4 bytes long, found at the beginning of a file. You can use any hex editor to have a look at it. I am using HxD but you can use an online hex editor or an inbuilt one within Linux.

![file_headers](https://hackmd.io/_uploads/rktisqOrT.jpg)

The image above is the hex editor veiw of the cool cat image from before. You can see that there is the 'y0ya JFIF' within the decoded text part and 'FF D8 FF E0 xx xx 4A 46
49 46 00' in the hex bytes veiw.

What a computer does is read the first few of these bytes and then cross checks it and then spews out the file type.

All file types like .docx, .png, etc have their own set of file headers (the file signatures). You can see all these file headers from a database online such as [Gary Kessler's](https://www.garykessler.net/library/file_sigs.html).

*It is also within these hex bytes that the meta data of a file is stored*

Maybe an image file has some broken file headers and when you try to open it, your computer will say its curropted. You can use hex editors to fix it the file headers. 
Hex editors are used to edit hex bytes, hence its funcationailty isnt limited to fixing file headers. You can manipualte the dimentions of the image which you want to show to your veiwer, like showing only a small portion of the whole picture and many other cool stuff.


---

**Steganography**

It is art of hiding data within plain site. Writing on a word document with white text can also be considered as a form of steganography but the more mainstream methods are hiding information within videos, images, audio files and network traffic. Al Qaeda once used porn videos to communicate the target locations.

So how does one hide data within images? Well images are just collection of pixels in the end. By changing the RGB value of the pixels we can hide information within it since we cant really say the difference between (0,201,39) and (0,202,40) with our naked eye but we can decode it with specific stegnographic algorithms. This is an example of LSB or Least Significat Bit Steganography. A video is just a collection of images so it works in a similar manner.

There are multiple tools to detect steganography such as stegseek and many tools to extract steganofied data such as zsteg, steghide etc which go through each byte and find the hidden data. For a more detailed explaination than I can ever manage in this small documentation, try watching this [video by computerphile](https://www.youtube.com/watch?v=TWEXCYQKyDc&t=697s)



---

**Networking**

Data being transported from one place to another is solely possible only due to the great networking foundation we have. Want to send a mail? Use SMTP protocol. Want to see a website? Use http and TLS (which form HTTPS).
There are many other protocols such as TCP and UDP for communication between 2 devices, FTP to transfer files, SSH to form a secure connection  and many many more and this is just the surface. You can always look up them on youtube. I personally like [Computerphile](https://www.youtube.com/@Computerphile) which has just soo many videos on it.

So how can one see such network traffic being sent all over the internet? Thats where the tool wireshark is used. Wireshark can analyze network packets sent and presents them in a nice readable way. The network traffic is recorded and saved within a file with format .pcap or .pcapng.

![wireshark_home](https://hackmd.io/_uploads/SklFPs_HT.jpg)

When you open a pcap file within wireshark you are greeted with such a home page. The different lines which are enumerated in the image are network packets. From the image itself we can see some data about the network packets like Time sent, source ip address and port, destination ip address and port, legnth of network packet etc. In the bellow 2 tabs we can see more data about the packet which we select such as the data sent etc.

![wireshark_info](https://hackmd.io/_uploads/ry-1ujuSp.jpg)

There is also a search bar in which we can filter and search for network packets. Such as looking for packets that use TCP, HTTP procols, or which has a certain source or destination IP address. You can right click on network packets and use the any parameter as a filtering option like shown bellow:

![wireshark_filter](https://hackmd.io/_uploads/BJBDKsuBa.jpg)

Finding all network packets than used HTTP protocol

![wireshark_http](https://hackmd.io/_uploads/HJdAvjuST.jpg)

In the Statistics tab of the home page, if you navigate to the protocols hierarchy tab, we can see all the protocols used and their usage in the recorded network traffic i.e the pcap file.

![wireshark_protocols](https://hackmd.io/_uploads/HysTDouSa.jpg)

There is obviously more to wireshark and I encourage the readers to learn more about networking. This is an absolutely beautifull field.

* If you want to dive further into Forensics, there are many many resources in the wild including youtube. Computerphile is one of my favourite channels.
* You can also ask around in our discord server for such resources. We even have a dedicated #resources channel for this.



---


---
title: Basics Linux Commands
author: DrunkenCloud
pubDatetime: 2024-01-10T16:55:12.000+00:00
slug: basic-linux-commands
featured: false
draft: false
tags:
  - editactf2023
  - Linux
  - basics
  - drunkencloud
description:
  "Master the essentials of Linux commands! From navigating directories to creating and deleting files, these commands are the backbone of CTF challenges. Discover the power of the command line and build essential skills for programming and infosec. Whether you're a beginner or an advanced user, these commands are a must-have in your toolkit."
---
# **EditaCTF**

![EditaCTF](https://hackmd.io/_uploads/ry2OKgqHp.png)

# Basic Linux Commands

Learning basic Linux is essential for programmers and computer science students as it is widely used in server environments and cloud services. Proficiency in Linux provides valuable skills for deploying applications, administering servers, and navigating command-line tools.

Command line tools are somethign which you will be using lot of in CTFs. While there are GUI tools, you will mostly be working on a terminal when you go into higher levels of programming and infosec.

Some linux commands you may use frequently are:

    ls – Displays information about files in the current directory.
    pwd – Displays the current working directory.
    mkdir – Creates a directory.
    cd – To navigate between different folders.
    rmdir – Removes empty directories from the directory lists.
    mv – Rename and Replace the files
    rm – Delete files 
    locate– Find a file in the database.
    touch – Create empty files
    cat – Display file contents on terminal
    strings- The string command prints the text stream of all human readable content within a file. (Try opening an image with notepad and then use strings on it)
    clear – Clear terminal 
    ps- Display the processes in terminal
    man – Access manual for all Linux commands. Using 'man <command_name>' you can find all the manual for the command.
    grep- Search for a specific string in an output. Especially used when you want to find the flag within say some gibirish
    echo- Display active processes on the terminal
    wget – download files from the internet
    tr- translating or deleting characters. Like say you want to base64 decode or encode your text strem (output) you cna use tr or for when you want to remove all commas from your output.
    file- Used to find the file type of a file. This is further explained in Forensics.

There is much more to linux that I havent even touched and if you want to delve deeper into the linux please feel free to do so, there are many resources dedicated to this.


---
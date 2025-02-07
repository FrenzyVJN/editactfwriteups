---
title: EditaCTF2025
author: DrunkenCloud
pubDatetime: 2025-02-7T16:55:12.000+00:00
slug: editactf2025
featured: true
draft: false
tags:
  - editactf2025
  - OSINT
  - linux
  - cryptography
  - web
  - basics
  - drunkencloud
description:
  "This write-up covers the solutions to various challenges in the EditaCTF2025 competition, including reverse engineering, cryptography, forensics, and more. Follow the steps, to unravel the mysteries and find the flags for each challenge."
---
# **EditaCTF**

![EditaCTF2025](https://hackmd.io/_uploads/rkPTml7Kkl.jpg)
## Author : DrunkenCloud

[Github](https://github.com/DrunkenCloud "Github") | [Portfolio](https://drunkencloud.vercel.app/ "Portfolio")


# EditaCTF 2.0 Writeups

## Web Exploitation

### Tri-Webathon (flag{web_ctf_basics}) - 100

Its a three part web Challenger.
1) For the first part. You just need to see the source code, the password for round 2 is there
![image](https://hackmd.io/_uploads/r1_B5IztJl.png)

2) After Logging into part 2, if you see that there is a image file in the source code of the page, it shows `/static/image.png`, this tells us there exists a `/static/`. So what we shall do now is open it on the webpage.
![image](https://hackmd.io/_uploads/HJEpqIMFyg.png)
![image](https://hackmd.io/_uploads/ryw29IzFJl.png)
When you open the password.txt, you get the password for level 3.

3) For level 3, the source code tells us the flag is in a place where even google cant find it. A bit of digging around later, you can find out what a robots.txt file is,
    > A robots.txt file contains instructions for bots that tell them which webpages they can and cannot access. Robots.txt files are most relevant for web crawlers from search engines like Google.

    When we open the robots.txt file on the webpage, we see the flag in it.
    ![image](https://hackmd.io/_uploads/ryjwo8Gtke.png)
    
This challenge was inspired by the online `wargame` [overthewire](https://overthewire.org), specifially `natas` within it, if you liked this web challenge and the ones following, please do try it out.

### Look Back (flag{r34ding_i5_g00d_f0r_y0u}) - 300

This challenge is one of the more basic directory traversal challenges. Here, the challenger is supposed to go back in the directory via the ?path variable passed in the url. This is a snippet of the code running on the server. This is also given in the `blame game` challenge.

```python=
def sanitize_path(requested_path):
    sanitized_path = requested_path.replace('../', '')

    decoded_path = urllib.parse.unquote(sanitized_path)
    real_path = os.path.abspath(os.path.join(BASE_DIR, decoded_path))

    if not real_path.startswith(LIMIT):
        return None

    return real_path
```

The issue is that simply removing "../" is ineffective because attackers can use double encoding (%252e%252e%252f → %2e%2e/ → ../) or other tricks (this is because you replace the '../' before doing a url decode).

![image](https://hackmd.io/_uploads/ByuPdDGtkl.png)

Here is an image when you open a pdf, notice the url.

![image](https://hackmd.io/_uploads/BksKdPfYyg.png)

By replacing it with `..%2F` which url decodes into `../`, which passes the check, gives us this directory. Do it again and you see a flag.txt. Open it for the flag.

![image](https://hackmd.io/_uploads/SJ4TdvMKyg.png)

![image](https://hackmd.io/_uploads/ByqRODGt1g.png)


### SQL Injection Structured Injection (flag{sql1_4_th3_w1n}) - 300

A basic SQL injection vulnerability occurs when user input is directly concatenated into an SQL query:

```query = f"SELECT * FROM users WHERE username = '{user}' AND password = '{password}'"  # Vulnerable to injection```

Here an attacker can input ``' OR '1'='1`` in the password parameter (this make the query always return the data of any user because ``'1' = '1'`` is always true), to bypass authentication. Always use parameterized queries to prevent this.

## Reverse Engineering

### Filing Input (flag{z3r0_th3_h3r0}) - 100

```c
int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Pass argv[1] a number\n");
        return 0;
    }

    int fd = atoi(argv[1]) - 0x1337;
    int len = read(fd, buf, sizeof(buf) - 1);

    if (len < 0) {
        perror("Read failed");
        printf("Learn about Linux file IO\n");
        return 0;
    }

    buf[len] = '\0';

    if (!strcmp(buf, "LETMEWIN\n")) {
        printf("Good job!\n");
        print();
        exit(0);
    }

    printf("Learn about Linux file IO\n");
    return 0;
}
```

Ok so, this is the main driving code. When compiled the resulting binary expects one CLI argument, which it subtracts from `0x1337`, then uses that as a [file descriptor](https://en.wikipedia.org/wiki/File_descriptor), and opens that file. 

The trick here is to know that the file decriptor for standard input, is 0. So if we passed in an argument equal to `0x1337`, it would open an input session, here you can enter LETMEWIN, and get the flag.

```
$ ./a.out 4919
LETMEWIN
Good job!
Here you go flag{z3r0_th3_h3r0}
```

This challenge was taken from the website `pwnable.kr`. Its a wonderfull site and people who liked this challenge following this please do look into it and try to solve problems in it.

### Over The Edge (flag{s1mp13_0v3rfl0w}) - 300

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>

int main() {
    int secret = 0xdeadbeef;
    char name[100] = {0};
    read(0, name, 0x100);

    if (secret == 0x1337) {
        puts("Wow! Here's a secret:");
        puts(flag);
    } else {
        puts("I guess you're not cool enough to see my secret");
    }
}
```

This is a buffer overflow problem. A buffer is bassically a block of memory, when you are able to arbitrarily write code over the buffer and beyond it, its called an overflow.

Here we have a buffer `name` of size 100 bytes, in the next line, we read `0X100` bytes into it. You may notice that `0x100` != `100`. This makes it so that we can write, 156 bytes (256-100) over the limit for the buffer.

The buffer `name` is stored on the stack. Above it is the buffer secret.

![image](https://hackmd.io/_uploads/B1NiZwGYkg.png)

Kind of like this. By checking how many bytes offset (i.e from the beginning of name until beginning of secret) we need to fill before we write the neccessary input, in our case `0x1337`, into the memory of secret.

```python!
import sys
import subprocess

padding = b'A' * 108
secret_value = b'\x37\x13\x00\x00'

payload = padding + secret_value

subprocess.run(['./vuln'], input=payload)
```
```
$ python3 ./solve.py
Wow! Here's a secret:
flag{s1mp13_0v3rfl0w}
```

This is a python file that loads the executable, and adds our payload, 108 'A', which is our offset, followed by the data that needs to be written into the the secret here. Notice that we write x37\x13, this is due to something known as [endianess](https://www.geeksforgeeks.org/little-and-big-endian-mystery/).


If you liked this kind of challenge where you deal with very low level mechanics such as bytes and registers, do check out [microcorruption](https://microcorruption.com/). Its a website with more challenges such as this, they provide you an emulated setting of registers, code etc.

## Cryptography

If you liked these crypto challenges, please check out [CryptoHacks](https://cryptohack.org/). Its a website where you get more such crypto challenges and also has a very big community, im sure you would love it.

### Totally Random (flag{v3ry_r4nd0m_numb3r}) - 100

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    long long random_number = rand();

    long long user_input;
    printf("Enter the number: ");
    scanf("%lld", &user_input);

    long long result = random_number ^ user_input;

    if (result == (long long)3039230920) {
        printf("Congrats! The flag is: flag{temp}");
    } else {
        printf("No\n");
    }

    return 0;
}
```

Honestly there is not much to say here, the rand() function always return the same value unless one seeds it by time (you can look up what that is). Here you just need to print out the value of rand() (in a linux system), xor it with 3039230920 and use that as your input and your done.

```
$ ./a.out
Enter the number: 3735928495
Congrats! The flag is: flag{v3ry_r4nd0m_numb3r}
```

### Seeded Chaos (flag{s1mpl3_l0ck5}) - 300

```python!
def generate_key(seed, length=16):
    random.seed(seed)
    key = ''.join(random.choice(string.ascii_letters + string.digits) for _ in range(length))
    return key

def get_keys():
    timestamp = int(time.time())
    if timestamp % 2 == 0:
        key_seed = random.randint(1, 1000)
        xor_key = 42
    else:
        key_seed = 42
        xor_key = random.randint(1, 255)
    return key_seed, xor_key
```

Here the vulnerablity arised from a week key generation algorithm, one only needs to check for key_seed value of 1 to 1000 with xor_key as 42 or key_seed value of 42 and xor_key of 1 to 255, so a ttal of 1255 possiblities, not that high, one can just write for loops for it.

These 2 keys are used to do 2 different encryptions, a XOR operation and then a Vignere Cipher, fancy name but all it does is use the key, map characters from a plaintext to a character from the key, cyclicly and then rotates the character in plaintext as many times as the position of the character in the key.  https://www.geeksforgeeks.org/vigenere-cipher/

Anyway, writing a decryption for the xor and vignere cipher is particularly easy, I think most people can handle that or GPT it. The key thing to notice is the small [key space](https://en.wikipedia.org/wiki/Key_space).

```python!
import random
import string
import base64
import time
from secret import FLAG

def generate_key(seed, length=16):
    random.seed(seed)
    key = ''.join(random.choice(string.ascii_letters + string.digits) for _ in range(length))
    return key

def xor_cipher(text, key):
    return bytes([ord(c) ^ key for c in text])

def decrypt_vigenere_cipher(encrypted_text, key):
    decrypted_text = []
    key_length = len(key)
    key_index = 0
    
    for char in encrypted_text:
        if char.isalpha():
            shift = ord(key[key_index % key_length].lower()) - ord('a')
            if char.islower():
                decrypted_char = chr(((ord(char) - ord('a') - shift) % 26) + ord('a'))
            else:
                decrypted_char = chr(((ord(char) - ord('A') - shift) % 26) + ord('A'))
            key_index += 1
        else:
            decrypted_char = char
        
        decrypted_text.append(decrypted_char)
    
    return ''.join(decrypted_text)

def brute_force_decrypt():
    with open("output.txt", "r") as f:
        encrypted_hex = f.read().strip()
        encrypted_text = bytes.fromhex(encrypted_hex).decode('utf-8')
    for key_seed in range(1, 1001):
        xor_key = 42
        key = generate_key(key_seed)
            
        xor_reversed_text = xor_cipher(encrypted_text, xor_key)
        
        decrypted_text = decrypt_vigenere_cipher(xor_reversed_text, key)
        
        if "flag" in decrypted_text:
            print(f"Found the key_seed: {key_seed}, xor_key: {xor_key}")
            print(f"Decrypted text: {decrypted_text}")
            return decrypted_text

    for xor_key in range(1, 256):
        key_seed = 42
        key = generate_key(key_seed)
            
        xor_reversed_text = xor_cipher(encrypted_text, xor_key)
        
        decrypted_text = decrypt_vigenere_cipher(xor_reversed_text, key)
        
        if "flag" in decrypted_text:
            print(f"Found the key_seed: {key_seed}, xor_key: {xor_key}")
            print(f"Decrypted text: {decrypted_text}")
            return decrypted_text

    print("Decryption failed.")
    return None

def main():
    brute_force_decrypt()

if __name__ == "__main__":
    main()
```

### Identity Collision (flag{h4sh_coll1s1on_gg}) - 300

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    ip = get_ip(request)
    user_db = get_user_db(ip)

    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        username_hash = md5(username.encode()).hexdigest()

        for db_user, (stored_hash, stored_password) in user_db.items():
            if stored_hash == username_hash and stored_password == password:
                if username == db_user:
                    return jsonify({
                        'message': f'Login successful! Welcome, {username}!'
                    })
                else:
                    return jsonify({
                        'error': 'Hash collision detected!',
                        'flag': 'flag{h4sh_coll1s1on_gg}'
                    })

        return jsonify({
            'error': 'Invalid username or password!'
        })

    return render_template_string()
```

The main algorithm for checking a login is pretty simple, it first checks if there exists a person with a certain hashed username and a password, then it checks if the username itself is the same, if it is not, it gives us the flag.

So bassically we need to find a way to have 2 users, who have the same password and same username hashes, but different usernames. How do we have 2 different usernames with same hashes? [Hash Collision](https://en.wikipedia.org/wiki/Hash_collision).

We see that the hash algorithm used here is MD5. A bit of googling gets us the strings

> TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak
> TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak

By having these 2 as the usernames, we generate the same MD5 hash, resulting in the situation we need to get the flag. Now all we need to do is register with these 2 usernames with the same passwords and login into one of them, which will give us the flag.

### Small Key, Big Trouble (flag{s3cr37s4uc3}) - 300

Using small primes p and q makes n easy to factor using online tools like factordb.com, allowing an attacker to compute ϕ(n), derive d, and decrypt the message. This breaks RSA’s security, as factoring n is the core challenge. 

Then using some regular maths used in the making of the RSA algorithm, we get the flag.

```python!
p = 857504083339712752489993810777
q = 1029224947942998075080348647219

phi = (p-1)*(q-1)
d = inverse(e, phi)

m = pow(c, d, n)
print(long_to_bytes(m))
```

Always use large, randomly generated primes (2048+ bits) to prevent this.

## Steganography

### Dialed In (flag{6c733ef09bc4f2a4313ff63087e25d67}) - 100

Using a simple DTMF decoder, where we upload the `dialtone.wav` file, like https://dtmf.netlify.app/, we get the number 

>13040004482820197714705083053746380382743933853520408575731743622366387462228661894777288573.

All we need to do now is convert it into a base 16 number system and them read every 2 hexes as a byte and decode into a string. We can use a string such as [cyberchef](https://gchq.github.io/CyberChef/) for this.

![image](https://hackmd.io/_uploads/H1W1vwfKkx.png)


### Hidden in Plain Sight (flag{stegasaurus}) - 100

Some people might have guessed that the streamer talked about in the question is [PirateSoftware](https://www.twitch.tv/piratesoftware). He uses a technique called steganography to hide text within the bits of an image. Look up techniques such as LSB steganography.

To solve the challenge, in your system you can install the tool zsteg, which looks for steganogrphies in your image.


```bash!
$ zsteg ./image.png

b1,r,lsb,xy         .. text: "flag{stegasaurus}"
b1,rgba,msb,xy      .. file: OpenPGP Public Key
b2,rgb,lsb,xy       .. file: shared library
b2,bgr,msb,xy       .. file: Applesoft BASIC program data, first line number 2
b2,rgba,lsb,xy      .. text: ["C" repeated 16 times]
b3,rgb,msb,xy       .. file: Applesoft BASIC program data, first line number 16
b4,r,lsb,xy         .. file: Novell LANalyzer capture file
b4,bgr,lsb,xy       .. file: Targa image data - Map 65536 x 1 x 16 +1 +16
```

This is what happens when you run zsteg on the image that was given.

One can also use some online tools like https://www.aperisolve.com. 

## Forensics

### Rogue Inbox (flag{y0u_s33_wh4t_I_s33}) - 100

Its really very simple, go all the way till the right, search for rows with drunkencloud@gmail.com in them. Theese have the flag's characters one by one, in a nunch of continuous rows.

### Blame Game (flag{drunkencloud}) - 300

Look up what `git` is before you attempt this challenge. Its bassically a save state manager for your code, after you make changes to a code base, you can commit it to the git repo where it stores that state, similar to save files in games. 

![image](https://hackmd.io/_uploads/SkibcDfK1g.png)

When you look at the logs in you notice the bug being pointed out by an intern `Jia Tan` with a commit message reading `This code not bussin`, after looking into who touched that piece of code before Jia Tan, we can see the user `drunkencloud`, who is our flag. We can find out its that person via git blame or just checking each commit one by one.


### Poneglyph (flag{fontyponty}) - 300

Pretty simply challenge, you just need to open the the font file `.otf`, given to you int he challeng ein a font editor, notice the smiley character, and read the name of its glyph, which is a custom name `fontyponty` in this case.

![image](https://hackmd.io/_uploads/SkueoDfF1l.png)

### Curropted Image (flag{f1x_f1l3_he4d3r5}) - 500

So, in this challenge we are given an image, but when we try to open it in an image veiwer it shows it can be opened due to unsupported file headers.

So why could this be? Well there is where the problem lies, `file headers`. Look them up but they are bassically the first few or so bytes of a file which indicate what type of file a file is, like png or jpeg etc, for a application to read through and render.

In our given image, these `magic bytes` or our file header has bassically been changed, you can look at it through a hex editor. 

![image](https://hackmd.io/_uploads/BkpqawGtye.png)

We see this, not very usefull on its own now is it. Now pull up a file headers list like: https://www.garykessler.net/library/file_sigs.html

Since its given that our file is a `.png`, lets look at the bytes for a png file

![image](https://hackmd.io/_uploads/B1gMCwfK1e.png)

So, what we have to do is,, rewrite the 3rd byte 43 to 4E, the 4th byte 24 to 47, 5th byte 91 to 0D, 6th byte 12 with 0A and 7th byte 83 with 1A. After making the changes, export it and check.

![image](https://hackmd.io/_uploads/Syaa0PzYyx.png)

![image](https://hackmd.io/_uploads/BkCRRDzKJl.png)

The flag is in the image.

## OSINT

### OSINT Bolt (flag{Sh3rl0cked_m3_in1t}) - 500

The challenge was to find out more about this `drunkencloud` person who popped up int he `blame game` challenge. You can google, use tools such as `sherlock`, any means to find this website. `https://about.me/drunkencloud`.

![image](https://hackmd.io/_uploads/H11_svMY1l.png)

We see the flag as soon as we open it.

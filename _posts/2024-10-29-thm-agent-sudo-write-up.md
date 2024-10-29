---
layout: post
title: 'THM: Agent Sudo | Write-up'
date: 2024-09-19 13:00:00 00
categories: [THM]
tags: [thm, privesc, stego, hash cracking, osint]
comments: false
---

Agent Sudo is a room on TryHackMe created by DesKel. This room includes Enumeration, Hash cracking, Steganalysis, OSINT, and Privilege Escalation.

## Task 1 Author Note 
To begin, start the machine.

## Task 2 Enumerate 

### <i>How many open ports</i>
<p>Use Nmap.</p>
![q1](/post-imgs/agent-sudo-writeup/agent-sudo-nmap.png){: width="900" }

### <i>How do you redirect yourself to a secret page?</i>
The answer to this question is in the homepage of the server. Access the home page by typing out the ip address of the machine to a web browser.
![q2](/post-imgs/agent-sudo-writeup/agent-sudo-q2.png){: width="900" }

### <i>What is the agent's name?</i>
I will be using burpsuite to intercept the request and change the user agent. The clue to this is also in the home page.
![q3](/post-imgs/agent-sudo-writeup/agent-sudo-q3.png){: width="900" }
After forwarding the request this is what we'll get.
![q3-1](/post-imgs/agent-sudo-writeup/agent-sudo-q3-1.png){: width="900" }

## Task 3 Hash cracking and brute-force

### <i>FTP Password</i>
Use Hydra.
![q4](/post-imgs/agent-sudo-writeup/agent-sudo-q4.png){: width="900" }

### <i>Zip File Password</i>
Login to the FTP server using the credentials that you've found.
![q5](/post-imgs/agent-sudo-writeup/agent-sudo-q5.png){: width="900" }
Download these files using the "mget" command
![q5-1](/post-imgs/agent-sudo-writeup/agent-sudo-q5-1.png){: width="900" }
Use binwalk on the cutie.png file
![q5-2](/post-imgs/agent-sudo-writeup/agent-sudo-q5-2.png){: width="900" }
You will notice that inside this png file, there are multiple files including a zip file. Extract the files using binwalk.
![q5-3](/post-imgs/agent-sudo-writeup/agent-sudo-q5-3.png){: width="900" }
After extracting you will see a new directory. Try opening it.
![q5-4](/post-imgs/agent-sudo-writeup/agent-sudo-q5-4.png){: width="900" }
8702 is a password protected zip file. To crack the password we will first need to extract the hash of the zip file. You can use zip2john for this.
![q5-5](/post-imgs/agent-sudo-writeup/agent-sudo-q5-5.png){: width="900" }
Now that we have the hash we will be using JohnTheRipper to bruteforce the password.
![q5-6](/post-imgs/agent-sudo-writeup/agent-sudo-q5-6.png){: width="900" }

### <i>Steg Password</i>
Use Stegseek on the cutie-alien.jpg file. This will extract the file in your directory.
![q6](/post-imgs/agent-sudo-writeup/agent-sudo-q6.png){: width="900" }

### <i>Who is the other agent?</i>
Open the text file that was extracted by stegseek.
![q7](/post-imgs/agent-sudo-writeup/agent-sudo-q7.png){: width="900" }

### <i>SSH Password?</i>
Read the text file. :)


## Task 4 Capture the user flag

### <i>What is the user flag?</i>
Using the credentials that you found in the previous question, login to the ssh.
![q8](/post-imgs/agent-sudo-writeup/agent-sudo-q8.png){: width="900" }
![q8-1](/post-imgs/agent-sudo-writeup/agent-sudo-q8-1.png){: width="900" }
Use "cat" to display whats inside the user_flag.txt.


### <i>What is the incident of the photo called?</i>
Download the jpg file in the ssh server using scp.
![q9](/post-imgs/agent-sudo-writeup/agent-sudo-q9.png){: width="900" }
After downloading use reverse image search (I used tineye)
![q9-1](/post-imgs/agent-sudo-writeup/agent-sudo-q9-1.png){: width="900" }
In the sort portion, search for foxnews.com and click the link on the first result.
![q9-2](/post-imgs/agent-sudo-writeup/agent-sudo-q9-2.png){: width="900" }

## Task 5 Privilege Escalation

### <i>CVE Number for the Escalation</i>
While logged inside the ssh, run sudo -l. This will display the allowed commands the user can do on the host.
![q10](/post-imgs/agent-sudo-writeup/agent-sudo-q10.png){: width="900" }
Search (ALL, !root) /bin/bash on google.
![q10-1](/post-imgs/agent-sudo-writeup/agent-sudo-q10-1.png){: width="900" }

### <i>What is the root flag?</i>
Scroll through the CVE documentation.
![q11](/post-imgs/agent-sudo-writeup/agent-sudo-q11.png){: width="900" }
![q11-1](/post-imgs/agent-sudo-writeup/agent-sudo-q11-1.png){: width="900" }
Navigate through the file system and find the root flag located in the root folder.
![q11-2](/post-imgs/agent-sudo-writeup/agent-sudo-q11-2.png){: width="900" }

### <i>(Bonus) Who is Agent R?</i>
Read the root.txt file. :)


This room was fun to work through, and I hope I was able to assist you in tackling it. :)

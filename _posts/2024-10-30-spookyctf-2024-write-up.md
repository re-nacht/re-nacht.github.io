---
layout: post
title: SpookyCTF 2024 | Write-up
date: '2024-10-30 07:37:13 +0000'
categories: [CTFs]
tags: [ctf, stego, cryptography, osint]
comments: false
image: /post-imgs/spookyctf-writeup/SpookyCTF_2024.png
---
## Overview
SpookyCTF is a jeopardy style Capture the Flag event hosted by the New Jersey Institute of Technology's Information and Cybersecurity Club.

<b>Site:</b> <a href="https://spookyctf.ctfd.io/">https://spookyctf.ctfd.io/</a> 
<br>
<b>Discord Server:</b>  <a href="https://discord.gg/mdx7H4DeWC">https://discord.gg/mdx7H4DeWC</a>
<br>

## <b>osint</b>

### perfect-nba-fit

![perfect-nba-fit](/post-imgs/spookyctf-writeup/perfect-nba-fit.png)

First thing I did is do a simple google search "Class A sighting in New Jersey". After looking at different article sites, I settled with the Bigfoot Field Research Organization. Based on the challenge description, the "Class A" creature was last seen 5 - 7 years ago. In this website, we can look into which counties fit the description.

![perfect-nba-fit-2](/post-imgs/spookyctf-writeup/perfect-nba-fit-2.png)

Upon investigating, I ended up with Burlington county. I then sorted all sightings to just Class A reports and found a report in 2019 which fits our description. Upon reading through the report, I found all the necessary information for the flag.

![perfect-nba-fit-3](/post-imgs/spookyctf-writeup/perfect-nba-fit-3.png)

<i>NICC{20_18:40_Route-563_Chatsworth}</i>

### they-have-returned

![they-have-returned](/post-imgs/spookyctf-writeup/they-have-returned.png)

I head over to the channel mentioned in the hint and looked at the users who arrived first. I found a user that stands out by having a role aside from the "Paranormal Investigator" role. Looking at their full profile, I found an attached link to their X profile.

![they-have-returned-2](/post-imgs/spookyctf-writeup/they-have-returned-2.png)

I visited their X profile and the flag was lying there in plain sight.

![they-have-returned-3](/post-imgs/spookyctf-writeup/they-have-returned-3.png)

<i>NICC{Gue55_wh0'5_b4ck?}</i>

## <b>steg</b>

### whispers-in-morse

![whispers-in-morse](/post-imgs/spookyctf-writeup/whispers-in-morse.png)

This challenge includes a jpg file that we can investigate.

![whispers-in-morse-2](/post-imgs/spookyctf-writeup/whispers-in-morse-2.png)

Using strings command, I found a password. Which led me to believe that I can use steghide to gain more info about this picture.

![whispers-in-morse-3](/post-imgs/spookyctf-writeup/whispers-in-morse-3.png)

And sure enough I found a "flag.txt" file inside it. With the same password, I extracted the file, and found the flag.

![whispers-in-morse-4](/post-imgs/spookyctf-writeup/whispers-in-morse-4.png)

<i>NICC{tHe_whIspeRz_iN_Th3_aiR}</i>

## <b>crypto</b>

### the-moth-flies-at-dawn

![the-moth-flies-at-dawn](/post-imgs/spookyctf-writeup/the-moth-flies-at-dawn.png)

This challenge have 2 .txt files attached to it, a hash and a wordlist. That gave me the idea of bruteforcing the hash using the given wordlist.

![the-moth-flies-at-dawn-2](/post-imgs/spookyctf-writeup/the-moth-flies-at-dawn-2.png)
![the-moth-flies-at-dawn-3](/post-imgs/spookyctf-writeup/the-moth-flies-at-dawn-3.png)

based on hashid, it may be a SHA-256 hash, I used hashcat to bruteforce and found the flag.

![the-moth-flies-at-dawn-4](/post-imgs/spookyctf-writeup/the-moth-flies-at-dawn-4.png)
![the-moth-flies-at-dawn-5](/post-imgs/spookyctf-writeup/the-moth-flies-at-dawn-5.png)

<i>NICC{blueberrypancake}</i>

## <b>misc</b>

### well-met

![well-met-5](/post-imgs/spookyctf-writeup/well-met-5.png)

The hint told us to read the lore. Upon reading it, I didn't notice anything that may point to a flag somewhere, not until I decided to look at the page source. Looking through the page source, I noticed a comment that seems to be different than the rest.

![well-met](/post-imgs/spookyctf-writeup/well-met.png)

Judging by the flag format, this looks like the last part of a flag. So i tried searching for the initial part of the flag which is "NICC", and viola I found the first part of the flag.

![well-met-2](/post-imgs/spookyctf-writeup/well-met-2.png)

Combining this with the last part of the flag still left me with a flag that does'nt make any sense. So I tried to look further. Since this is in an id called "spookyflag_part1", I tried searching for a similar case. I ended up finding the second part of the flag.

![well-met-3](/post-imgs/spookyctf-writeup/well-met-3.png)

By then, I noticed that I may be missing a third part of the flag since the last part of the flag contains "p4". I then looked for every "_" and found the third part of the flag.

![well-met-4](/post-imgs/spookyctf-writeup/well-met-4.png)

Combining all four parts, I got this.

<i>NICC{StOrIeS_DoNt_MaKe_ThE_cTf_ToO_cTfY_rIgHt?}</i>


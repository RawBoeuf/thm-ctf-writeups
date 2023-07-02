# [EasyPeasy Room](https://tryhackme.com/room/easypeasyctf)
![EasyPeasy Room Image](/resources/EasyPeasy.png)
## Introduction
The EasyPeasy Room is an "easy" rated CTF room on TryHack that aims to give beginners in Cybersecurity a way to apply the tools that they've learned i.e. nmap, Gobuster. in a more practical scenario. In this document, I will explain the steps I take as I go through this room. I have started this room on July 2nd of 2023 and I will continue to detail my steps as I go further through the room.

## Write-Up
This section details the steps I've taken to complete this CTF.

### Task 1: Enumeration through Nmap
#### Step 1: Nmap Scan
To start off, I decided to go with an nmap scan to enumerate the deployed machine. I ran this command on the AttackBox:   
`nmap -vv -T5 -A -p- MACHINE_IP`  
The results of the scan are below.  
![EasyPeasy Nmap Scan Results](/resources/EasyPeasyNmapScan.png)
#### Nmap Command Breakdown
If you're not interested in my breakdown of what the flags/switches on the nmap command do, you can skip by clicking [here](https://github.com/RawBoeuf/thm-ctf-writeups/edit/main/EasyPeasy.md#results-analysisanswering-the-task-questions).


#### Results Analysis/Answering The Task Questions
**Question 1: How many ports are open?**  

If we take a look at the nmap scan results [above](https://github.com/RawBoeuf/thm-ctf-writeups/edit/main/EasyPeasy.md#step-1-nmap-scan).   
We can find that ports 80, 6498, and 65524 are open. Thus, the answer to this question is 3.  

**Answer: 3**  

**Question 2: What is the version of nginx?**  
From the nmap scan results, we can see that .  
![EasyPeasy Nmap Scan Results Apache](resources/EasyPeasyNmapApache.png)

# [EasyPeasy Room](https://tryhackme.com/room/easypeasyctf)
![EasyPeasy Room Image](/resources/EasyPeasy.png)
## Introduction
The EasyPeasy Room is an "easy" rated CTF room on TryHack that aims to give beginners in Cybersecurity a way to apply the tools that they've learned i.e. nmap, Gobuster. in a more practical scenario. In this document, I will explain the steps I take as I go through this room. I have started this room on July 2nd of 2023 and I will continue to detail my steps as I go further through the room.

## Write-Up
This section details the steps I've taken to complete this CTF.

### Task 1: Enumeration through Nmap
#### Step 1: Nmap Scan
To start off, I decided to go with an nmap scan to enumerate the deployed machine. I ran this command on the AttackBox: `nmap -vv -T5 -A -p- MACHINE_IP`
The results of the scan are below.
![EasyPeasy Nmap Scan Results](/resources/EasyPeasyNmapScan.png)

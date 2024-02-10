Room Name : Basic Pentesting
Category : Easy
URL for the room : https://tryhackme.com/room/basicpentestingjt

Before going through the walkthrough, I would suggest you to try solving this on your own. Don't jump directly into the solution as you may miss the thrill to solve the box.  

----------------------------------------------------------------------------------------------------------------

In these set of tasks you'll learn the following:

* brute forcing 
* hash cracking 
* service enumeration
* Linux Enumeration
  
The main goal here is to learn as much as possible. Make sure you are connected to our network using your OpenVPN configuration file.

Credits to Josiah Pierce from Vulnhub.

-----------------------------------------------------------------------------------------------------------------
Deploy the machine. To connect to TryHackMe, you can either use attackbox or access the VPN provided by TryHackMe using OpenVPN

1. Deploy the machine and connect to our network
Ans. : -- No answers required --

2. Find the services exposed by the machine
Ans. : -- No answers required --

The first step would be to perform Reconnaissance on the given machine. So, using nmap to scan for services running on the machine, we get the following

**command for nmap : nmap -sV -A [MACHINE-IP]**

![1](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/505e22d1-2a09-4af1-98ca-7238699b4aee)

We can see the following services running :
1. ssh on port 22
2. http on port 80
3. Samba on port 139 etc.

Since, hhtp is open on port 80, we can look for it in the browser. Opening the browser and searching for http://[MACHINE-IP] takes us to the following page

![2](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/e86f6997-57c1-4c09-94b8-e9d53e31c014)

There is nothing much we can see from this page. Performing directory enumeration using gobuster, we can find a directory named **development**

**command for gobuster : gobuster dir -u http://[MACHINE-IP]/ -w path-for-wordlist**

![3](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3af96c1b-b820-4537-89d6-2d88ed6240ca)

3. What is the name of the hidden directory on the web server(enter name without /)?
Ans. : development



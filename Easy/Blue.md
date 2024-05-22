Room Name : Blue
Category : Easy
URL for the room : https://tryhackme.com/r/room/blue

Before going through the walkthrough, I would suggest you to try solving this on your own. Don't jump directly into the solution as you may miss the thrill to solve the box.  

--------------------------------------------------------------------------------------------------------------------

**Task 1 : Recon**

Scan and learn what exploit this machine is vulnerable to.   
Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.   
This room is not meant to be a boot2root CTF, rather, this is an educational series for complete beginners.   
Professionals will likely get very little out of this room beyond basic practice as the process here is meant to be beginner-focused. 

![1](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/6e34d753-0d8c-4c0c-9459-5ee9833b4102)

Perform NMAP scan on the IP address of the target machine

1. Scan the machine. (If you are unsure how to tackle this, I recommend checking out the Nmap room)
Ans. : -- No answer needed --

2. How many ports are open with a port number under 1000?
Ans. : 3

3. What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)
Ans. : ms17-010  
Explanation : From NMAP scan we can SMB protocol running on the target machine. Searching for the vulnerabilities on SMB, we come across EternalBlue exploit (hence the name BLUE) which is registered as MS17-010  
Source : https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010  

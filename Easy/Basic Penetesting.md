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

Now, we need to enumerate through the given machine. From nmap scan, we can find the machine is running Samba. Hence, we can use **enum4linux** to enumerate the machine (You can google to find this tool)

The enum4linux gives the usernames for the machine provided

**command for enum4linux : enum4linux [MACHINE-IP]**

![4](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/bdbd5a47-4f06-47ea-ac81-56c984437a33)

Now, sicne SSH port is open we can brute force password for any one of the user. We can find password for user **jan** is **armando**  

![5](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/d4f41f6f-363c-4d9a-a0fa-193c603340e8)

We can login to jan's shell using obtained credential to access it.  

![6](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/1819f63f-7004-40e1-970d-9012a3f8a79d)

4. User brute-forcing to find the username & password  
Ans. : -- No answers required --  

5. What is the username?  
Ans. : jan

6. What is the password?  
Ans. : armando

7. What service do you use to access the server(answer in abbreviation in all caps)?  
Ans. : SSH

After exploring a bit, I found out that there is private RSA key of user **kay** and we have the read permissions. So, we can use it to crack password of **kay**. Following are the steps to crack RSA private key of **kay**

![7](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/525cb265-7a9e-43da-a98d-6b202d5b664b)



1. Copy the key to your local computer as id_rsa (can be anything)
2. Use JohnTheRipper to crack the RSA key to find password of **kay**

**command for cracking RSA private key**  
**1. ssh2john id_rsa > hash** -> stores the private key hash into new file named hash  
**2. john --wordlist=path/for/wordlist hash** -> uses wordlist to crack the private key

From above steps, we can find the password for **kay** is **beeswax**

![8](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/24930111-d305-4c41-a384-118175276331)

Now, once we have the password for **kay**, we can login as kay as follows :
**ssh -i /home/kay/.ssh/id_rsa kay@[MACHINE-IP]**

Enter the password to login as **kay**. Once logged in we can read the pass.bak and get the final password.

![9](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/c3e62295-fa41-47f9-895f-30559b4ed539)


8. Enumerate the machine to find any vectors for privilege escalation    
Ans. : -- No answers neede --  
 
9. What is the name of the other user you found(all lower case)?  
Ans. : kay  

10. If you have found another user, what can you do with this information?  
Ans. : -- No answers needed --  

11. What is the final password you obtain?  
Ans. : heresareallystrongpasswordthatfollowsthepasswordpolicy$$  






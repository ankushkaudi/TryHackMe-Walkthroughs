Room Name : RootMe  
Category : Easy  
Tags : Security, Web, Linux, Privilege-Escalation  
URL for the room : https://tryhackme.com/room/rrootme  

Before going through the walkthrough, I would suggest you to try solving this on your own. Don't jump directly into the solution as you may miss the thrill to solve the box.

---------------------------------------------------------------------------------------------------

Task 1 : Deploy the machine  
Description : Connect to TryHackMe network and deploy the machine. If you don't know how to do this, complete the OpenVPN room first.  

Start the machine - No answers needed

----------------------------------------------------------------------------------------------------

Task 2 : Reconnaissance   

Description : First, let's get information about the target  

Connect to OpenVPN or start the attack box or Kali Linux using blue button at the top.  

The first step is to perform nmap scan to get information about the services running on the machine. The nmap command is as follows :  

sudo nmap -sV -A (ip-address-of-the-machine)  

-sV -> performs/detects the versions of services running on the machine  
-A -> performs OS and service detection  

![Pic 1](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/11435cf7-8ac6-4ad2-b133-3f4c877b20c7)


The nmap results shows the following :  
1. Port 22 is open running ssh version 7.6p1 (OpenSSH 7.6p1)  
2. Port 80 is open running http and Apache version 2.4.29 (Apache/2.4.29 (Ubuntu))  
3. The servvice info shows the machine is running Linux OS  

Since http is running on port 80 we can connect to it. Open your browser and search for the url as follows :
http://(ip-address-of-the-machine)  

![Pic 2](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/739732d1-080f-4c4b-bf03-9ee9f4a343f8)


We can use gobuster to perform directory enumeration and find out the directories present on this machine.  
The following command use common.txt wordlist to perform directory enumeration (other wordlists can also be used)  

gobuster dir -u http://(ip-address-of-the-machine)/ -w (path-for-wordlist)  

![Pic 3](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/ed16da7b-b7cc-419e-af1b-0cfb8093f749)


The gobuster directory enumeration shows the presence of a directory "/panel" which completes our Task 2  

Answers to the questions:

1. Scan the machine, how many ports are open?
Ans : 2

2. What version of Apache is running?
Ans : 2.4.29

3. What service is running on port 22?
Ans : ssh

4. Find directories on the web server using the GoBuster tool.
No answer needed

5. What is the hidden directory?
Ans : /panel

------------------------------------------------------------------------------------------------------------

Task 3 : Getting a shell

Description : Find a form to upload and get a reverse shell, and find the flag

From the gobuster directory enumeration, we have a hidden directory "/panel". Navigating to the url gives us a 
file upload form
http://(ip-address-of-the-machine)/panel

![Pic 4](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/cfcd361a-c9bd-478a-9228-dc487d04e19c)


We can obtain the reverse shell using reverse shell payload which can be found on the below url 

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Make sure to change the IP address and the port  
-> IP address is the address of your attack box or VPN IP address  
-> Port can be any arbitrary port number  

![Pic 6](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/af3856bb-15b5-4952-8be4-c284bd1a378b)


Start a netcat listen on the mentioned port with the command as follows :  
nc -lvnp 9001

![Pic 8](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/edc72b9b-a6bf-4edd-b59c-288082f23bf2)


Uplooad the php payload after changing IP address and port. After uploading it an error as   
PHP não é permitido! which means PHP is not allowed.   

![Pic 5](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/0dd2a213-26bf-4549-a39a-546cacfe2a8c)


Searching for php file extensions, we can find few like php4, php5, etc.  

Save the payload as payload.php5 and upload which gives a message "The file has been uploaded" but still we do not find any shell on netcat  

![Pic 7](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/7d28de8e-de35-45d7-b4e9-8d0cf62f416d)


Navigating to the uploads directory (ref : gobuster directory enumeration) we can find our payload here clicking on it will give a shell on our netcat.   

![Pic 9](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/1e4b597b-cac4-4c27-891b-f5e919cb1e03)


We can find the user flag as follows :

![Pic 10](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/61dcb0e4-424f-44a0-80c5-752e0ce6d340)


We can search for user.txt with the following command :
find / -type f -name user.txt 2> /dev/null

find / -> used to find in / directory
-type -> denotes the file type
-name -> name of the file
2> /dev/null -> skips the error/not found messages and just gives the positive results

We can find the user.txt is located at /var/www/user.txt read the file and obtain the flag

cat /var/www/user.txt

![Pic 11](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/424c43ee-4713-4a60-9bb3-c16d29869057)


Answer to the question:

1. user.txt  
Ans : THM{y0u_g0t_a_sh3ll}

-----------------------------------------------------------------------------------------------------------------

Task 4 : Privilege Escalation

Description : Now that we have a shell, let's escalate our privileges to root

Now we should find a way to escalate our privileges to root. We need to find files with SUID permissions which
can be found by the below command :
find / -user root -perm 4000

The command says to find all the files in / directory owned by root and with SUID bit set. One such file is /usr/bin/python  

We can find a way to gain privilege escalation on GTFOBins

https://gtfobins.github.io/gtfobins/python/

![Pic 12](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/7996ff9b-7861-444e-8360-aafc39b9ca9f)


Executing the above command in the shell obtained we can gain root access

Once we have the root access, we can read the root.txt

![Pic 13](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3e6db471-99dd-4acc-9ff2-92cacd8d8fe8)


Answers to the questions :

1. Search for files with SUID permission, which file is weird?  
Ans : /usr/bin/python  

2. Find a form to escalate your privileges  
No answer needed  

3. root.txt  
Ans : THM{pr1v1l3g3_3sc4l4t10n}  


developed room for beginners. 

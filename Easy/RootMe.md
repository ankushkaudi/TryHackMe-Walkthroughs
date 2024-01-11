Room Name : RootMe  
Category : Easy  
Tags : Security, Web, Linux, Privilege-Escalation  
URL for the room : https://tryhackme.com/room/rrootme  

---------------------------------------------------------------------------------------------------

Task 1 : Deploy the machine
Description : Connect to TryHackMe network and deploy the machine. If you don't know how to do this, complete the 
OpenVPN room first.

Start the machine - No answers needed

----------------------------------------------------------------------------------------------------

Task 2 : Reconnaissance

Description : First, let's get information about the target

Connect to OpenVPN or start the attack box or Kali Linux using blue button at the top.

The first step is to perform nmap scan to get information about the services running on the machine. The nmap
command is as follows :

sudo nmap -sV -A ip-address-of-the-machine

-sV -> performs/detects the versions of services running on the machine  
-A -> performs OS and service detection  

![Pic 1](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3e21704f-6506-4962-bcf3-6447b0e59600)


The nmap results shows the following :  
1. Port 22 is open running ssh version 7.6p1 (OpenSSH 7.6p1)  
2. Port 80 is open running http and Apache version 2.4.29 (Apache/2.4.29 (Ubuntu))  
3. The servvice info shows the machine is running Linux OS  

Since http is running on port 80 we can connect to it. Open your browser and search for the url as follows :
http://<ip-address-of-the-machine>

![Pic 2](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/a0b6c970-b785-4c48-a726-ac2a189c02b6)


We can use gobuster to perform directory enumeration and find out the directories present on this machine.
The following command use common.txt wordlist to perform directory enumeration (other wordlists can also be used)

gobuster dir -u http://<ip-address-of-the-machine>/ -w <path-for-wordlist>

![Pic 3](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/a9f0bc4a-38e3-4dd9-a6e2-7e4a9be3e3ed)


The gobuster directory enumeration shows the presence of a directory "/panel" which completes our task 2

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
http://<ip-address-of-the-machine>/panel

![Pic 4](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3e3a603c-d701-4105-bba8-15965fa3b214)


We can obtain the reverse shell using reverse shell payload which can be found on the below url 

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Make sure to change the IP address and the port
IP address is the address of your attack box or VPN IP address
Port can be any arbitrary port number

![Pic 6](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/6faf0706-7df3-4100-9572-6e2789a41e95)


Start a netcat listen on the mentioned port with the command as follows :
nc -lvnp 9001

![Pic 8](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/982bbd46-0894-4380-a69c-676a3c4e0a74)


Uplooad the php payload after changing IP address and port. After uploading it an error as 
PHP não é permitido! which means PHP is not allowed. 

![Pic 5](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/ef7511b5-8ba5-409b-901a-c90ee0ed71d4)


Searching for php file extensions, we can find few like
php4, php5, etc.

Save the payload as payload.php5 and upload which gives a message "The file has been uploaded" but still we
do not find any shell on netcat

![Pic 7](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/1b31929d-974d-465b-a25c-0d5dd89b7bf1)


Navigating to the uploads directory (ref : gobuster directory enumeration) we can find our payload here clicking 
on it will gove a shell on our netcat 

![Pic 9](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/da5be8b2-6b19-427b-b7c1-9b78260b6303)


We can find the user flag as follows :

![Pic 10](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/8ddef220-0ade-43a2-9665-67e66198ef63)


We can search for user.txt with the following command :
find / -type f -name user.txt 2> /dev/null

find / -> used to find in / directory
-type -> denotes the file type
-name -> name of the file
2> /dev/null -> skips the error/not found messages and just gives the positive results

We can find the user.txt is located at /var/www/user.txt read the file and obtain the flag

cat /var/www/user.txt

![Pic 11](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/7fe137c9-6385-43fa-8830-e396e490b9da)


Answer to the question:

1. user.txt
Ans : THM{y0u_g0t_a_sh3ll}

-----------------------------------------------------------------------------------------------------------------

Task 4 : Privilege Escalation

Description : Now that we have a shell, let's escalate our privileges to root

Now we should find a way to escalate our privileges to root. We need to find files with SUID permissions which
can be found by the below command :
find / -user root -perm 4000

The command says to find all the files in / directory owned by root and with SUID bit set. One such file is
/usr/bin/python

We can find a way to gain privilege escalation on GTFOBins

https://gtfobins.github.io/gtfobins/python/

![Pic 12](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/d571b59e-6fa8-4e19-9d84-930e107b7681)


Executing the above command in the shell obtained we can gain root access

Once we have the root access, we can read the root.txt

![Pic 13](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/695be804-49a1-4b8e-b977-9f8bbd6e1cd1)


Answers to the questions :

1. Search for files with SUID permission, which file is weird?
Ans : /usr/bin/python

2. Find a form to escalate your privileges
No answer needed

3. root.txt
Ans : THM{pr1v1l3g3_3sc4l4t10n}

Summary : 

It was a very interesting room designed for beginners. It gives a hands on experience to work with nmap to find 
services running along with their versions and OS. Using gobuster to find the hidden directories. Using 
php-reverse-shell payload to obtain a shell and finding ways to escalate the privileges. Overall, a perfectly 
developed room for beginners. 

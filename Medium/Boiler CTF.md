Room Name : Boiler CTF  
Category : Medium  
URL for the room : https://tryhackme.com/r/room/boilerctf2  

Before going through the walkthrough, I would suggest you to try solving this on your own. Don't jump directly into the solution as you may miss the thrill to solve the box.

-------------------------------------------------------------------------------------------

Task 1 : Intermediate level CTF. Just enumerate, you'll get there.

Perform nmap scan for the given IP address of the machine


![1](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/c22241be-745d-4659-8943-f878f20268cd)

Results of the nmap scan are as follows :
1. ftp open on port 21 with anonymous login allowed
2. http open on port 80
3. ssh open on port 55007

Logging in to ftp as anonymous and exploring gives the following :

![2](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/623da5ce-9555-4df6-9968-f7651f30b195)

Presence of info.txt file completes the 1st question of task 1

Using gobuster to find the directories reveals the following : 

![3](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/c2335a6d-4375-45ed-aba4-a7c2490338ea)

We can see "joomla", which is CMS. Navigating to /joomla gives the CMS interface. Performing directory scan again on http://<ip>/joomla gives aa lot of directories and files as follows : 

![4](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/c776d510-1b89-4e76-bfb0-cdd8d3303d5c)

There are tons of directories but "_test" is an interesting one. Navigating to /_test gives the following page which says it's using sar2html.

About sar2html : 
Sar2html is a tool used for generating graphical reports from system performance data collected by the Unix/Linux "sar" command. The "sar" command, which stands for System Activity Reporter, is used to collect, report, and save system activity information such as CPU utilization, memory usage, disk activity, and network activity over time.

Sar2html takes the raw data produced by the "sar" command and converts it into HTML format, allowing users to view the system performance data in a more user-friendly and graphical manner. This can be helpful for system administrators and performance analysts to monitor and analyze system performance trends over time.

The generated HTML reports typically include various graphs and tables presenting metrics such as CPU usage, memory usage, disk activity, network activity, and more, making it easier to identify performance bottlenecks, trends, and abnormalities in system behavior.

![5](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/ded7440f-a3db-4ad5-8f87-2b452ccbc066)

We can see there is link provided which downloads sar2ascii.tar. This compressed file contains a text file which is as follows :

![6](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/655dc646-0c9d-48c5-be1a-d53fd5483887)

From the text file, we can see the version of sar2html used, which is v3.2.1.

A google search for the given version says it is vulnerable to Remote Code Execution (RCE). The exploit usage is as follows : 

![7](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3a315cbc-4daa-4eaf-bd0f-5bc54738d087)

Following the above usage, we can try "ls" to find the existing files in Present Working Directory (pwd) of the server.

![8](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/99888dcd-a76b-4d5c-9d41-7460d3705b65)

We can see a log.txt which seems a bit interesting. If we do "cat log.txt", we can find credentials for ssh login which is running on port 55007.

![9](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/7f49ea19-e7a9-4186-b015-eda398514334)

This completes the Task 1

Questons : 

1. File extension after anon login  
Ans : txt

2. What is on the highest port?  
Ans : ssh (on port 55007)

3. What's running on port 10000?  
Ans : Webmin (can be found in nmap scan)

4. Can you exploit the service running on that port? (yay/nay answer)  
Ans : nay (Webmin cannot be exploited as it is in the latest version)

5. What's CMS can you access?  
Ans : joomla

6. Keep enumerating, you'll know when you find it.  
Ans : --No answer needed--

7. The interesting file name in the folder?  
Ans : log.txt

-----------------------------------------------------------------------------------------------------------------------------








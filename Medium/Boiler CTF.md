Room Name : Boiler CTF  
Category : Medium  
URL for the room : https://tryhackme.com/r/room/boilerctf2  

Before going through the walkthrough, I would suggest you to try solving this on your own. Don't jump directly into the solution as you may miss the thrill to solve the box.

-------------------------------------------------------------------------------------------

**Task 1 : Intermediate level CTF. Just enumerate, you'll get there.**

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

**Task 2 : You can complete this with manual enumeration, but do it as you wish**

We have the ssh credentials for user named basterd, so we can login and find the file named backup.sh which contains the credentials for another user named stoner
Username : stoner
Password : superduperp@$$no1knows

![10](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/2b8cd446-5bf3-4fa7-b87c-8978f173cd55)

Now logging in to ssh as stoner and exploring, we find a file named secret which has the answer for user.txt

![11](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/5afe8a85-b965-48e0-96b0-0f7ec7eb5f2d)

Now our last task is to ecalate the privileges, so we can find the files whose SUID bit is set

About SUID : 
SUID stands for Set User ID. It's a special permission in Unix-like operating systems, including Linux. When SUID is set on an executable file, it allows that file to run with the privileges of the file owner rather than the user who is executing it. In other words, when a user runs an executable file with the SUID bit set, the program will run with the same permissions as the owner of the file, regardless of who executed it.

SUID is represented by the 's' bit in the permissions of a file. If the 's' bit is set in the user permissions (the owner's permissions) of the file, it indicates SUID. Likewise, the 's' bit in group permissions or others' permissions can represent SGID (Set Group ID) and sticky bit, respectively.

The SUID mechanism is often used for programs that need elevated privileges to perform certain operations but need to be executed by regular users. Examples include programs related to system administration tasks, such as changing passwords or managing system resources, which require elevated privileges but should be accessible to non-administrative users. However, it's crucial to handle SUID-enabled programs carefully to avoid security vulnerabilities, as they can potentially be exploited to gain unauthorized access or execute malicious actions if not properly secured.

We can find the files with SUID bit set using the following command : 

**find / -perm /4000 -type f -exec ls -ld {} \; 2>/dev/null**

Explanation of above command : 
-> find: This is the command used to search for files and directories within a specified directory hierarchy.

-> /: This specifies the root directory from which the search should begin.
-> -perm /4000: This is the primary condition used by find to search for files with specific permissions. In this case, it's looking for files with the SUID or SGID bit set. The 4000 value represents the SUID (4) or SGID (2) bit being set. The / before 4000 means that either the SUID or SGID bit is set.

-> -type f: This additional condition tells find to only search for regular files, excluding directories and other types of files.

-> -exec ls -ld {} \;: This part of the command executes the ls -ld command on each file found by find. ls -ld is used to list files in long format (-l) and provide details about the files, including permissions, owner, group, size, and modification time. {} is a placeholder for each file found by find, and \; indicates the end of the -exec command.

-> 2>/dev/null: This part of the command redirects standard error output (file descriptor 2) to /dev/null, effectively suppressing any error messages that may occur during the execution of the command.

This gives the following results with all the files with SUID bit set.

![12](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/f288a424-8c06-44a2-9fda-66abb2e8b4bf)

We can find /usr/bin/find whose SUID bit is set and can be used to escalate the privileges using the following command and we can read the root.txt

**find . -exec chmod 777 /root \;**

Explanation of above command : 
This command is attempting to change the permissions of the /root directory and potentially all files and directories within it to allow full read, write, and execute permissions for all users. 

-> find .: This command starts the search from the current directory (.), searching recursively through all files and directories within it.

-> -exec: This option allows you to execute a command on each file or directory found by find.

-> chmod 777 /root: This is the command being executed for each file or directory found. chmod is a command used to change file permissions, and 777 represents full permissions for owner, group, and others (read, write, and execute permissions). However, the target directory specified here is /root, which is the root user's home directory. Changing permissions on this directory could potentially have severe security implications, as it could allow any user on the system to modify critical system files, which is highly discouraged and often prevented by the system.

-> \;: This signifies the end of the -exec command.

![13](https://github.com/ankushkaudi/TryHackMe-Walkthroughs/assets/111695465/3cc861ce-569a-4ea9-a55f-85df9584cdf0)

This completes the Task 2

Questions : 

1. Where was the other users pass stored(no extension, just the name)?
Ans : backup (in backup.sh)

2. user.txt
Ans : You made it till here, well done. (from secret)

3. What did you exploit to get the privileged user?
Ans : find

4. root.txt
Ans : It wasn't that hard, was it?  








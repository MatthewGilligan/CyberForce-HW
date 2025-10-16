# Overview
Week 1's Homework was to get experience with either windows or linux, whichever you were LEAST comfortable with. Since I am a windows user, I needed to research linux. 
I have used linux (mainly WSL) in the past for various CTFs or I use the powershell for my computer science classes, but I am still a linux beginner. 

## Questions to answer
- enumerate users, groups, permissions
- identify common places for suspicious files
	- as well as how to sort by recency
- View active network connections
- How to lock user accounts / re-roll credentials
- View active processes w/ detail (not just task manager)

## Enumeration
### Users
**Resource used:** https://phoenixnap.com/kb/how-to-list-users-linux 
If we were given a file, such as "/etc/passwd" that contains the users on a system we could use "cat" to list the contents of the file (I was already familiar with this)
One new thing that I learned is that you can combine "cat" with the wordcount command "wc" and "-l" add on to count the amount of lines. 
Because each line has a new user, listing the amount of lines would list the amount of users. However, this doesn't always work, and doesn't give us all the names. 

Example input would look like: "cat /etc/passwd | wc -l"


Another method that could be used to list the users could be the "less" or "more" commands. Generally less is a better command than more, as more is older 
and less was literally made to be an improvement on more. 
These commands allow for keyboard navigation of a file. They don't directly enumerate all the users, but make it easier for you to manually enumerate. 
I could see this being useful if there is a smaller file, but I doubt that it would be super useful for Cyberforce by itself (I could be wrong though)

Exampue input would look like: "less /etc/passwd"


What I think is the **BEST** way of listing all the users is the "awk" command. This command lists all of the usernames out for us to read. You can also combine this with the previously mentioned "less" command. You can also use grep with this, (another one of the few linux commands I know) for better sorting. 
The syntax is a little tricky, but I think with practice awk is going to be very useful for Cyberforce. 

Example input would look like: awk -F':' '{ print $1}' /etc/passwd  


The final method that I researched about listing users seems to be super useful for cyberforce. This is the "getent" command, that when combined with a filter for a specific username lists information on that user. Additionally the syntax is very easy to remember. Just type getent, location to search, and the username to search for. 

Example input would look like: getent passwd [username] (replacing username with the desired name)


### Groups
**Resource used:** https://linuxize.com/post/how-to-list-groups-in-linux/  
There are 2 different types of groups a user can be a part of: A Primary group (also called login group) and a secondary group (also called supplementary group)
The primary group is the group of the files created by the user. Each user MUST belong to EXACTLY one primary group. 
Secondary groups are groups that can be used to grant various priveliges to users. Users can belong to ANY NUMBER of secondary groups. 

Besides just "cat" "grep" and "less" like we talked about before, there is a specific command that was built specifically to give information about groups. 
This is the "groups" command, and will print a list of all the groups the user is currently logged into. (I bet we could run this at the start of a competition, just to see what we're working with). We can also pass in a username to get the groups that a specific user belongs to. 

Example input would look like: groups


Another way to get the groups is by using the "id" command. This command lists the user id as well as the primary group and all secondary groups in number form. 
Like groups, this command can work with a username, or if entered without a username it will display information about the user. Appending -n will show the names instead of number, -g will print only the primary group and -G will print all groups

Example input would look like: id username (-nG)


To **get all members of a group** we go back to "getent" but we add "group" and the name of the group. No output means the group doesn't exist.

Example input would look like: getent group groupname

In order to **list all groups** you can either open the /etc/group file with something like less or use getent group. Other commands can be used in tandem with this, such as awk which we went over in the last section. 

Example input would look like: getent group

### Permissions
**Resource used:** https://www.howtouselinux.com/post/check-file-permissions-in-linux 

The most common and easiest way to see the permissions of a file is to run ls appending "-l" . This will show information about all the files in the working directory, including their permissions, ownership, size, and modification timestamps. Additionally, you can add in the filename to file size, last modification date, owner, group, and name of the file.

Example input would look like: ls -l (or ls-l filename)

Another way would be to use the "stat" command. Combine this with the filename of the file you want to check out and it will give you file permissions in both numeric and symbolic formats. 

Example input would look like: stat filename

**What does the information I'm getting from these commands mean?** 
Each file or directory has 3 levels of ownership: user owner (u), group owner (g), and others (o)
Each of these can be assigned with read (r), write (w) and execute (x) permissions. So the output "rw-r--r--" means that the owner has read and write perms while
other group members and all others have read perms only. (This is symbolic mode, and is used in the chmod command to change perms. Like  “chmod u+w filename” would give the owner write permissions. Using a "-" instead of a "+" removes perms instead of granting them)


## Identifying common places for suspicious files
**Resource used:** https://thesecmaster.com/blog/essential-files-and-directories-in-linux-for-security-monitoring
There are 10 main types of files and directories that can help find out where an attack was launched. One useful trick to **sort by most recently modified** is to use "ls -t" or for reverse, you can use "ls -tr". (This can be used if you think you are in the spot where a suspicious file was recently modified or placed and you want to check) 

I am not sure about which ones are the most common for cyberforce, so I am listing all of the examples given in the article. 
#### 1. System configuration files
These files are essential to making sure that the system is working properly, and would definitely be a place for a red teamer to attack. 
Common locations in the system configuration files include:
- /etc/passwd : A file that contains info about the user account (like usernames and user IDs)
- /etc/shadow : A file that stores hashed passwords (a very important file that could cause a lot of account theft)
- /etc/group  : Defines the groups where users belong. If this was modified, there was probably some privelege escalation happening
- /etc/sudoers: Controls the sudo privileges (root perms). If this was modified, there was definitely a big breach
- /etc/hosts  : Configures IP addresses and domain names. If modified, could be some sort of redirection of network traffic
 	
#### 2. Critical System directories
These directories are also critical to the proper functioning of the linux system. They are super important files to monitor, as well as big targets to attack.
Common locations include:
- /root/ : The big guns. This contains super sensitive data and tons of configuration files that run our system.
- /boot/ : Also big guns. This contains the kernel and bootloader files that allow the system to start up. Tampering with the boot process can brick the device
- binaries and system binaries. More on these later
- Directories containing shared libraries such as /lib/, /usr/lib/, /lib64/ or /usr/lib64/. Common spots for malware

#### 3. Log files
Monitoring these files is super useful for detecting attacks and various system malfunctions
Important files to monitor include:
- /var/log/auth.log (or /var/log/secure on Red Hat-based systems): This file contains authentication logs, recording successful and failed login attempts. Monitoring this file helps detect unauthorized access attempts and privilege escalations.
- /var/log/syslog: A general system log file. Useful for detecting system-wide issues or signs of compromise.
- /var/log/messages: Another general system log, especially on Red Hat-based systems. Gives a comprehensive view of system activities.
- /var/log/dmesg: Contains kernel ring buffer messages. The kernel interacts with the hardware, so hardware errors (or attacks) appear here
- /var/log/lastlog: Records the last login of each user. Defnitiely monitor this one for suspicious users!
- /var/log/wtmp, /var/log/btmp: These files record login history and failed login attempts. Can detect brute force attacks

#### 4. User-Specific Files and Directories
These files contain user information like SSH keys and other personal stuff, making them a target for attackers. 
Make sure to monitor these locations to make sure that data doesn't get leaked:
- ~/.bashrc, ~/.profile, ~/.bash_profile, ~/.bash_logout: These are shell configuration files for individual users. Unauthorized changes to these files could indicate attempts to modify user environment settings, potentially for malicious purposes.
-~/.ssh/authorized_keys: This file stores SSH-authorized keys. Unauthorized additions to this file could indicate that an attacker has added a backdoor for remote access.
-~/.ssh/known_hosts: Contains SSH known hosts. Changes here might indicate unauthorized access attempts or man-in-the-middle attacks.
-~/.bash_history: Stores command history for users. While attackers might clear or modify this file, monitoring it can provide insights into suspicious user activity.

#### 5. Cron Jobs
(I hadn't even heard of this, but they seem super related to cyberforce)
"Cron jobs are scheduled tasks that can be used by attackers to gain persistence on a system." This seems like exactly what the red teamers use (pre-planned attacks that need to be defended against. Perhaps I misinterpreted what these are, but they seem important)
Monitor these files to check the cron jobs:
- /etc/crontab, /etc/cron.d/ : System wide cron jobs
- /var/spool/cron/crontabs/  : User specific cron jobs

#### 6. Package Management
These are responsible for updating, installing and removing software. These also seem super related, as a common attack is *installing* malware onto a device
The most common places to look would be:
- /etc/apt/sources.list, /etc/yum.repos.d/ : These files configure package repos, so trying to change them could point to someone trying to install malware.
- /var/lib/dpkg, /var/lib/rpm : These store the package management databases, and trying to change them might indicate an attempt to install or remove packages.

#### 7. System Binaries
These are various executables that preform all sorts of system functions. Monitor these to make sure that no malware has been entered into the system
Most common places:
- /usr/local/bin/ : This is often used for custom or manually installed binaries. Changes could indicate malware installs
-/usr/local/sbin/:. This is used for system binaries. Changes could affect system operations

#### 8. Network Configuration Files
These files control how the system connects to networks. Pay attention to these, especially for network based attacks.
Make sure to monitor:
-/etc/hosts.allow, /etc/hosts.deny: Changes here could affect the network access controls, and are important for keeping our blue points (I think...)
-/etc/iptables/rules.v4, /etc/iptables/rules.v6: These files store firewall rules, so they're super essential to monitor to make sure that the firewall works.

#### 9. Kernel Modules
Pieces of code that get loaded into the kernel (hardware interaction)
Make sure to monitor "/etc/modules, /etc/modprobe.d/" as these are the kernal configuration files.

#### 10. Temporary Directories
Temporary directories are used by normal users and attackers to store/execute files
Make sure to monitor "/tmp/, /var/tmp/:" as these are the directories used for temporary storage. Pay attention to unusal activity here. 








 

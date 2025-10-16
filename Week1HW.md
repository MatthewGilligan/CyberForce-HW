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


 

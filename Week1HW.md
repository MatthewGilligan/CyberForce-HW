# Overview
Week 1's Homework was to get experience with either windows or linux, whichever you were LEAST comfortable with. Since I am a windows user, I needed to research linux. 
I have used linux (mainly WSL) in the past for various CTFs or I use the powershell for my computer science classes, but I am still a linux beginner. 

## Questions to answer
- enumerate users
- groups/permissions
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

### Permissions

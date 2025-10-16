# Overview
Week 1's Homework was to get experience with either windows or linux, whichever you were LEAST comfortable with. Since I am a windows user, I needed to research linux. 
I have used linux (mainly WSL) in the past for various CTFs or I use the powershell for my computer science classes, but I am still a linux beginner. 

## Questions to answer
- enumerate users, groups/permissions
- identify common places for suspicious files
	- as well as how to sort by recency
- View active network connections
- How to lock user accounts / re-roll credentials
- View active processes w/ detail (not just task manager)

## Enumerating users
** Resource used: ** https://phoenixnap.com/kb/how-to-list-users-linux 
If we were given a file, such as "/etc/passwd" that contains the users on a system we could use "cat" to list the contents of the file (I was already familiar with this)
One new thing that I learned is that you can combine "cat" with the wordcount command "wc" and "-l" add on to count the amount of lines. 
Because each line has a new user, listing the amount of lines would list the amount of users. However, this doesn't always work, and doesn't give us all the names. 
Example input would look like: "cat /etc/passwd | wc -l"


Another method 

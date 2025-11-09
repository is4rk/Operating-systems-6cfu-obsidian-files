# Docker
Open-source technology that permits the execution of applications quickly and efficiently by mean of Containers.
Containers share the kernel of the OS, the embedded depenedencies and configurations of the application.
![[Pasted image 20250928222236.png|500]]
## Commands
list containers
`sudo docker container ls -a`
run create container:
`sudo docker start <dockername>`
stop running docker:
`sudo docker stop <dockername>`
remove a container:
`sudo docker rm <dockername>`

# SHH
Remote connection
`ssh -X <username@hostname>`
-X is to redirect grafical content

# Help commands
`apropos <command>`
`whatis <command>`
`whereis <command>`

# Command parsing
You can use '\\' to continue a long command on the next line.
Two commands can be written on the same line using ';'

# ls
`ls [-options] [file ...]`
-a shows hidden files
-l long list format
-t sort by date
-r reverse order 
-R recursive, includes files in subdir
![[Pasted image 20250928223913.png|600]]
![[Pasted image 20250928223923.png|600]]

-|rwx|rwx|rwx
0 1      2       3
0:  - normal file, d directory,  s socket file, l symbolic link 
1: user permissions
2: group permissions
3: other's permissions ($other=totalUsers-user-group$)
# Files  move, copy, remove
`mv [options] file1 file2 ... dest`

`cp [options] src1 src2 ... dest`

`rm [options] file1 file2 …`

Options:
-f force, doenst ask confirmation
-i ask confirmation for each file
-r -R recursively to all sub dir

# Directory move, cpy, remove
`mkdir dir`
`rmdir dir` //same as `rm -fr dir`

# Permission for directories
There exist 3 permissions:
r, w, x
read, write, execute
`cp file1 file2` fails if file1 has not read permissions or if file2 has not write permissions
`cd dir` fails if dir has not execution permissions

# Permission management
It is possible to change file permissions if you have the rights, i.e., if you are the owner of the file.
## User
There are commands to change personal generalities (i.e., the user) of files on a UNIX system:
To become a different user:
`su username`
	The password of the new user is requested
To run commands as a superuser (or other user):
`sudo command`
	The password of the user is required
To know which user you are:
`whoami`

## Files
It is possible to change file permissions
`chmod [options] permissions file`
Examples:
`chmod g+r filename`
`chmod +x filename`
`chmod +xw filename`
`chmod uo+rx filename`  user other add read execute. Adds to user and others read and execute permissions to that file.
The owner can always change the permissions of the file. 

Can also use this system:

|     | r   | w   | x   |
| --- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 1   | 0   | 0   | 1   |
| 2   | 0   | 1   | 0   |
| 3   | 0   | 1   | 1   |
| 4   | 1   | 0   | 0   |
| 5   | 1   | 0   | 1   |
| 6   | 1   | 1   | 0   |
| 7   | 1   | 1   | 1   |
So if i want
rwx|r-x|---
750
chmod 750 file0.txt

rw-|r-w|-w-
652
chmod 652 file1.txt

`chmod go-w` //removes write permission from group and other
![[Pasted image 20250929105639.png]]

## Directory
Changing the owner of a directory entry
	`chown [options] user entry`
Changing the group of a directory entry
	`chgrp [options] group entry`
	`chown [options] user[:group] entry` it means that it gives the permission to user and to group, so you write `chown -options gabriele:teachers file.txt`
	`chown [options] uid[:gid] entry`

Options:
-R recursive

if i have
![[Pasted image 20250929111008.png]]
dir 1 rwx
dir 2 --x
dir 3 ---
![[Pasted image 20250929111245.png]]

# Output the content of a file
Output and concatenate files:
	`cat filename1 filename2`]
Output the first n lines of a file
	`head [options] filename ...`  //default is 10, can also remove lines from print by doing -3, doenst print last 3
Output the last num lines of a file
	`tail [options] filename ...`

Options:
-n num, number of lines
-f, outputs appended data as the file grows (i.e., the file
is continuously re-read)

browse page-wise through text file:
	`pg [options] filename ...`
to view a text file:
	`more [options] filename ...`
Like the previous command but allows the use of arrows to move in the text (advanced version of more):
	`less [options] filename ...`

more and less can be nagivated with the following inputs:
- space: Next page
- return: Next line
- b: Previous page
- /str: Find next occurrence of string str
- ?str: Find previous occurrence of string str
- q: Quit
# Counts
`wc filename`
prints:
number_lines, number_word, number_char

options:
-l prints only n lines
-w prints only n words
-c  prints only n byts
-m prints only number of characters

It always outputs the name of the file as the first line

# File comparison
`diff [options] file1 file2`
lists the line number of the lines added, deleted, changed

`diff [options] dir1 dir2`

OPTIONS:
-q reports only when files differ
-b Ignores spaces at the end of the line, merges the others
-i case insensitive
-w ignores completely al white spaces
-B ingnores all blank lines

# Hard and soft links
Symbolic or soft link:
- Particular type of file that simply contains a path (i.e., the name) of another object (file or directory)
- Allows references between different file-systems (partitions)
- If you remove the file the link remains pending 

Physical or hard link
- Association between an object name and its content (pointer from directory-entry to i-node)
- It is not possible to create hard links between different file-systems, or hard links to a directory
- The file is removed only when it is removed the last of its hard links

## Link creation
`ln [options] souce [destination]`
Default:
Creates a hard link. If the destination is not present, creates a link with the same filename on the working director.

Options:
-s creates a symbolic link
-f forces creation, removes file if already exist
-f, -F allow the superuser to attempt to create a hard link to a directory

# Archive management
Data storage and compression can be managed using the `tar` command
**Archiving**:
`tar czvf <file>.tgz <dir>`
Options:
-c
-z,j,J compression gzip, bzip2, 7z
-v vebose
-f specify the name of the archive

**Extracting**
`tar xzvf <file>.tgz <dir>`
-x Extracts the files from the archive
-z, j, J Compression (gzip, bzip2, 7z)
-v Verbose (print some messages and statistics)
f Specify the name of the archive (always present)

# Disk space occupation
To control disk occupancy, it is possible to use the df command
`df [options] [disk ...]`
OPTIONS: 
-B SIZE, scale sizes by SIZE before printing them. Size can be 1K, 10K, 1M, 1G, 1T etc.
	--block-size=SIZE
-k correpsonds to --blocksize=1k


To control direcotry occupation
`du [options] directory`
Options:
-a Occupation of each file
--summarize, -s Prints only the total
--block-size=1K, -k Occupation in kB

# pushd
pushd is like cd, but after setting a path it remebers it, so if i run pushd, i jump to that directory.

# Running time
to check how long a programm takes to run write
`time program`
if i have a compiled c program:
`time compiled_c_program`
it will print:
`./compiled_c_program  0.01s user 0.06s system 2% cpu 2.626 total`


MANCANO SECTION 3 e 4
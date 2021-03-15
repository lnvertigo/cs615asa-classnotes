What is the difference between a BIOS partition and an operating system partition?


How does a server boot up? After powering on a server, what happens next?

	- POST
	- ** some layer here**
	- 1st stage boot loader
		- executable code loaded into master boot record (MBR)
	- 2nd stage boot loader or loads kernel
		- 2nd stage gives user option
		- kernel loads and starts executing
	- kernel and starts init (non-kernel process)
	- init starts the system processes and daemons

Why are the primary and secondary boot loaders?


When creating a file system, what (if any) aspects or properties are fixed?

	- inode count (# of files that can be made)
	- block count?
	- file system block size


Is there a way to grow or shrink a file system? Think about how that would be implemented.

	- 

# Segment 1: The Boot Process and the MBR
## 00:00​ Introduction
## 00:36​ Booting up a system
	Booting up a web server
- Power on hardware (beeps)
- POST (power on self test) and hardware init
- first stage boot loader (off of the mbr/master boot record)
- Hands control to the init process which controls the bootstrapping process, mounting the fs, bringing up networking, launching daemons
- then login prompt
## 05:55​ The Master Boot Record
## 08:14​ A Partition Table Entry
## 10:35​ A Practical Example
## 25:47​ Summary

# 2: Filesystems
## 00:00​ Introduction
## 00:48​ A naive filesystem
## 04:20​ Allocating storage blocks
## 08:25​ Adding file attributes
## 12:17​ tar(2) as a filesystem format
## 14:38​ Recap

# 3: UNIX Filesystem
## 00:00​ Introduction
## 01:23​ The UNIX Filesystem
## 03:44​ news(8) and dumpfs(8) by example
## 08:55​ Summary

# 4: Files go hier(7)
## 00:00​ Introduction
## 00:35​ File types
## 06:34​ FreeBSD mounts
## 07:03​ OmniOS mounts
## 08:36​ Fedora mounts
## 11:04​ NetBSD mounts
## 11:46​ Filesystem hierarchy
## 15:34​ Exercises
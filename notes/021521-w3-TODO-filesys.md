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
512 bytes large (sector 0)
- last two bytes (0x55, 0xAA) - boot signature
- 64 bytes (446-509) - bios partition table
	- 16 bytes (4 partition entries)
- 446 bytes for stage 1 boot loader

## 08:14​ A Partition Table Entry
- 16 bytes (4 partition entries)
	- 0x00 - active
	- 0x01 - 3 bytes for CHS of 1st sector
		- 1 byte Head
		- 2 bits (high bit of cylinder, 6 bits sector)
		- 8 bits of cylinder
	- 0x04 - 1 byte partition type
	- 0x05 - 3 bytes for CHS of last sector
		- 1 byte Head
		- 2 bits (high bit of cylinder, 6 bits sector)
		- 8 bits of cylinder
	- 0x08 - 4 bytes LBA of 1st sector
	- 0x12 - 4 bytes LBA of last sector
	- 
## 10:35​ A Practical Example TODO
## 25:47​ Summary

# 2: Filesystems
## 00:48​ A naive filesystem
save 1st cat
	dmesg | grep xbd1
		check disk
	printf '(cat1)' | dd of=/dev/xbd1
		save cat to disk
	dd if=/dev/xbd1 count=1 2>/dev/null | hexdump -C
		hexdump the disk to read it, cat should be in there
	printf "$(dd if=/dev/xbd1 bs=1 count=4 2>/dev/null)\n"
		print the bytes from disk

save 2nd cat
	printf '(cat2)' | dd of=/dev/xbd1 bs=1 seek=4
		saved 2nd cat
	printf "$(dd if=/dev/xbd1 bs=1 count=8 2>/dev/null)\n"
		read both cats

need to know where a file starts and ends to read from disk properly

deleted from the disk
	dd if=/dev/zero of=/dev/xbd1
		fill /dev/xbd1 with 0s

## 04:20​ Allocating storage blocks
the files are separated into their own 512 sections 
	dd of=/dev/xbd1 bs=1 seek=512
		the seek flag is the storage block size?
		bs flag is the actual block size

	printf "$(dd if=/dev/xbd1 count=1 2>/dev/null)\n"
		would print the first cat
	printf "$(dd if=/dev/xbd1 count=1 skip=2 2>/dev/null)\n"
		would print the 3rd cat (Jan put in a third one, didn't type here)

adding metadata
	dd if=/dev/zero of=/dev/xbd1
	printf '\x1\x4(cat1)' | dd of=/dev/xbd1
		saved the file number and file size to the file itself
	printf '\x2\x4(cat2)' | dd of=/dev/xbd1 bs=1 seek=512
		saved 2nd cat
	printf '\x3\x0D(cat3)' | dd of=/dev/xbd1 bs=1 seek=512
		saved 3rd cat that's 14 bytes large

## 08:25​ Adding file attributes
16 bytes of metadata
- 2 bytes - identifier
- 4 bytes - perms
- 1 byte - owner
- 1 byte - group
- 4 bytes - size
- 4 bytes - offset
	- the offset is to tie the metadata to the file itself, which is saved SOMEWHERE ELSE

Example:
	printf '\x0\x1\x0\x7\x4\x4\x0\x0\x0\x0\x0\x4\x1\x0\x0\x0' | dd of=/dev/xbd1
	printf '(cat1)' | dd of=/dev/xbd1 bs=1 seek=4096
		metadata for cat1 and cat1 itself

	printf '\x0\x2\x0\x7\x4\x4\x0\x0\x0\x0\x0\x4\x1\x0\x0\x4' | dd of=/dev/xbd1 bs=1 seek=16
	printf '(cat2)' | dd of=/dev/xbd1 bs=1 seek=4100
		" for cat2

	dd if=/dev/xbd1 count=2 bs=16 2>/dev/null | hexdump -C
		read the metadata in disk

	printf "$(dd if=/dev/xbd1 count=4 bs=1 skip=4096 x>/dev/null)\n"
	printf "$(dd if=/dev/xbd1 count=4 bs=1 skip=4100 x>/dev/null)\n"

## 12:17​ tar(2) as a filesystem format
	cd /tmp
	echo (cat1) > cat1
	echo (cat2) > cat2
	echo (cat3) > cat3

	tar cf /dev/xbd1 /tmp
	dd if=dev/xbd1 count=10 | hexdump -C | more

	dd if=/dev/xbd1 count=10 2>/dev/null | tar tvf -

# 3: UNIX Filesystem TODO i can't wrap my head around this...
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
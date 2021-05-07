# Pre-class Questionnaire
> Diff between NAS and SAN?
> 
network attached storage - a device that handles files that's attached to a network

storage area network - a network of storage devices

> What storage device do the ec2 instances use?
> 
~~> /dev/rxbd0~~

ebs - elastic block storage

> What disk interfaces do you have exp with?

SATA for HDD interfaces,  
not sure for local data storage devices

> Name 3 types of file systems. Give 1 example for each.
	
- Disk file system
	- FAT, UFS, ext2  
- Tape file system
	- Linear tape file system  
- Network file system (NFS)
	- AFS, SMB
- journaling file system:
	- when performing transactions, all the metadata and transaction data is stored separately from the file data.

> What's an inode? What's NOT in an inode?

An inode is a piece of metadata correlating for every file and directory in a system that stores information such as size, permissions, owners, file path, and date.

The contents of a file itself are not contained in an inode.

# 1 | Storage Models and Disks
## Intro
> Parkinson's Law
	Disks are always full. It is futile to try to get more disk space.
	Data expands to fill any void.
## Core Concepts
- basic disk concepts
	- storage models
	- disk interfaces (storage device connections + protocols)
	- physical disk structure
	- partitions
- basic file system concepts
	- RAID
	- logical volume management
	- device formatting
- file systems
## DAS (Direct Attached Storage)
app software <-> file system <-> storage
- storage device is physical part of server and managed by OS
- file system created on storage device

Advantages:
- no network = no network failure
- no network latency

Disadvantages:
- direct attachment = isolation from network
## NAS (Network Attached Storage)
- data on host A is the same on host B
- network file systems connects host OSes to a file server
## SANs (Storage Area Network)
- NAS, but for scale
- storage (SAN) devices connected to a network that hosts connect to as a DAS or through a file server as NAS
## Cloud Storage
- models:
	- "file level" (Dropbox, GDrive, iCloud)
	- "object level" (AWS S3)
	- "block level" (AWS EBS)
		- create file systems and partitions as desired

## S3 vs EBS
	aws s3 mb s3://cs615asa [make bucket]
	aws s3 cp --recursive --exclude ".git/*" html s3://cs615asa [copy files into bucket]

	aws ec2 describe-instances --instance-id _____ | jq -r '.Reservations[].Instances[].BlockDeviceMappings'
		[get storage device and id]
	aws ec2 describe-volumes --volume-id _______
		[properties, size, etc of disk]
	aws ec2 create-volume --size 4 --availability-zone us-east-1a
		[create storage volume in ebs]

# 2 | Device Interfaces
## SCSI - small computer system interface
>SAS - serial attached scsi (listed below)
## ATA - advanced tech attachment
>SATA - serial ata
## IDE - integrated device electrones interface
NSA could insert malware into fireware
## SSD - solid state drives
the good shit
## fibre channel
used like a switch and uses fiber/copper cables
## storage protocols
- ATA over Ethernet (AoE)
- fiber channel over Ethernet (FCoE)
- oE (over Ethernet)
- iSCSI
- serial attached SCSI (SAS)
## capacity
shit's cheap, ssd sizes are the new expensive/premium

JBOD: just a bunch of disks (horizontal scaling)

flash arrays

# 3 | Storage Virtualization
## RAID
redundant array of independent/inexpensive disks
RAID combines all disks into single virtual disk
improves I/O efficiency
provides data redundency/fault tolerance

RAID 0 - blk lvl striping
- basically using it like 2 full disks
- no redundancy
- very fast

RAID 1 - mirroring
- basically a backup between each disk

RAID 5 - block lvl striping w/ distributing parity
- each write going to separate disks but separated by parity bits
- needs at least 3 disks

can combine RAID levels

## Logical Volume Management
- mngmnt of physical volumes
- pool physical volume groups into logical volumes (LV)
- combine/create LVs to make
	- JBOD
	- hot swapping
	- dynamically resizing file systems
	- add RAID
	- implicit backup via snapshots

## LVM/Device Mapper Example
	mount | grep ^/
		print mounted disks
	dmesg | grep sda
		get disk info
	lslbk
		more info (like partitions)
	swapon--show
		confirms swap space is available

## ZFS Example TODO
fs from Solaris

# 4 | Physical Disk Structure
## Physical components of a hard drive
- magnetic platter (7-15k rpm)
	- tracks
		- sector (512 byte blocks max)
	- cylinder
- read/write head
- actuator arm

each platter side can be used to store data
## Zone Bit Recording
- more sectors are placed on the outer zones
- more capacity on outer rings
- more data transfer speeds on outer sectors

all of this is because the 512 block is the same regardless of how far it is from the center read/write head despite the area growing larger as well, meaning outer sectors have more space to utilize

## Hard Drive Performance
performance is limited
- transfer bit rate
- seek time (actuator time to move between tracks)
- rotational latency (disk needs to spin back to spot you need)
- others
	- external data rate
	- command overhead
	- access time
	- etc

## Capacity
how many blocks are on the disk?

Cylinder-Head-Sector (CHS) method
	ATA specification
		65536 cylinders
		16 heads
		255 sectors/track
		512 bytes/sector
		= ~137 GB
	BIOS limit
		1024 cy
		256 heads
		63 sec/track
		512
		= ~8.5 GB
	combined (min of each)
		16 heads
		63 s/t
		512
		~528 MB

Logical Block Addressing (LBA)

Master Boot Record uses 32-bit sector numbers
	limit = 2^32 * 512 = ~2.1TB

# 5 | Partitions
## Physical Arrangement of Partitions
partitions are stored as rings around the disk instead of pie wedges as visualized by software
- boot usually at the outer edge
- then swap
- then root ("slash")
- then usr
## NetBSD disklabel example
	start-netbsd
	new-volume
	aws ec2 attach-volume --volume-id volid --instance-id i-id --device /dev/sdf
	ec2wait i-id

	disklabel xbd1
		disk information
	disklabel -i xbd1 (manually set partitions with disklabel)
		partition>a
		63s
		100M
		swap
		a
		128M
		e
		4.2BSD
		b
		256M
		f
		4.2BSD
		e
		$
		p
		W
		y
		Q
	disklabel -C xbd1

partitions d is used to describe the whole disk in NetBSD
part c is reserved for the OS

## OmniOS format example TODO
## Fedora Linux fdisk example TODO

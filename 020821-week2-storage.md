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
## Logical Volumn Management
## LVM/Device Mapper Example
## ZFS Example

# 4 | Physical Disk Structure
## Physical components of a hard drive
## Zone Bit Recording
## Hard Drive Performance
## Capacity
## Summary

# 5 | Partitions
## Physical Arrangement of Partitions
## NetBSD disklabel example
## OmniOS format example
## Fedora Linux fdisk example

# 6 | 
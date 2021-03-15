Give three examples of where you might find firmware:


Try to provide a concise description of what comprises an "Operating System".
Where do you draw the boundary between the "core OS" and (optional) "add-on software"?


Have you ever installed an OS from scratch? What kind? What did the process entail?


Which package manager(s) do you have experience using?
Have you created packages of software for use with a package manager before?


Which package manager(s) do you have experience using?
Have you created packages of software for use with a package manager before?

# Multi-User Basics
## Implications of a Multi-User System
Users can either coordinate together or get in each others' ways.
Shared resources can negatively affect other users.

## Consider Scalability
## Granting Privileges Requires TRUST
- diff envs have diff trust models
- small groups human interactions = stronger trust
- larger groups are split into smaller closenit groups
- more groups = weaker trust bonds

**TRUST DOES NOT SCALE.**

Implement ZERO TRUST principles
- computers and humans = Least Privilege
	- LEAST PRIVILEGE = time-based access expiration, automated renewal with strong audit capabilities

humans will find ways around your control if you get in their way

## Implications of a Multi-User System
- users might want to keep files private
- users might want to share files

MISSED

## Users and User-IDs
MISSED
- not injective (multiple users have access to an account [root])

## Auth
- proof of ID, *not authorization*
	- something you **know**
	- something you **have**
	- something you **are**
- multi-factor auth combines these to protect against diff threats
- mutual auth may be a requirement

common examples:
- passwds, pins
- ssh keys
- security tokens, OTPs, RFIDs
- physical biometrics
- behavioral biometrics

mix and match the yield multi-factor:
- password + PIN via SMS
- ssh key + TOTP from mobile device
- fingerprint + security token
- etc

## UNIX Fundies: User Accnts and File Perms
check with *ls -l [file]*

## Raising Privs
- binding a port < 1024
- operating on raw sockets (ping(1), traceroute(8))
- changing local passwds
- accessing files/dirs w/o explicit perms
- just about anything involving fs
- etc
  
### Methods
- log in as root
- su (superuser)
	- su user2-not-you -c `some command`
- sudo (superuser do)
	- more granular control over what user can do with sudo
		- sudo bash
			- not allowed
		- sudo ls dir
			- not allowed
		- sudo -u otheruser ls dir
			- allowed

## UNIX groups
- enables arbitrary collections of users with shared resources
- info stored in /etc/group
	- format:
		- name: *:GID:user1,user2,...
- most UNIX systems impose a limit of 16 or 32 members
- default user groups
- ...

## Group Access
- a central user db
- users divd into diff access groups
- access to systems is granted primarily by such group membership
- privs on a system are also granted by such group membership

RBAC (role based access control)

## Multiuser Truths
- All users are equal.
- Some users are more equal than others.
- The principle of least priv applies to all.
- Trust does not scale. ("Zero trust")
- You will always face trade-offs.

## Adding and Removing Accounts
### Exercises
https://stevens.netmeister.org/615/useradd-exercise.html
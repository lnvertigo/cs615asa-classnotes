# Segment 1 - E-Mail, Part I
## 00:00​ Introduction
## 03:35​ The Mail System

SMTP
- Mail User Agent (MUA)
	- mail apps like Outlook or commands like mutt
- Mail Transfer Agent (MTA)
	- works like mail servers
	- postfix, sendmail, qmail
- Mail Delivery Agent (MDA)
	- procmail
- Access Agent
	- POP, IMAP


## 07:15​ Using mail(1) to send an email
	tcpdump -w file port 53 or port 25 >/dev/nulldsjijf
	mail -s "SMTP Test" ajin1@stevens.edu
		MESSAGE  HERE
	fg crtl+C
	sudo tail /var/log/maillog
	tcpdump -r file

rejected traffic with invalid reverse DNS records

- looked up DNS MX record to find mail servers
- make tcp conn to prt 25 to server
- send data to try to send mail and get rejected through ipv6
- try again for ipv4 bc of that reverse record

## 12:23​ Receiving mail QUESTION
from the receiving mail server...
	sudo egrep "[mail-localid]|???what's this string" /var/log/maillog
		find it in the logs

	grep -A 2 "[datetime of email] ~/Mail/proclog
		find it in the logs

	mutt -f /tmp/smtp-test
		open it in the agent

## 15:11​ Sending mail using telnet(1)
	host -t mx netmeister.org
		find which mail server
	host panix.netmeister.org
		find ip address
	telnet [ip]
	EHLO cs615asa.netmeister.org
	MAIL FROM: <ajin1@stevens.edu>
	RCPT TO: <ajin1@stevens.edu>
	DATA
	From: Full Name <ajin1@stevens.edu>
	To: ajin1@stevens.edu
	Subject: subjectiosjdf
	
	message goes here
	.
	quit

# Segment 2 - E-Mail, Part II TODO
## 00:00​ Introduction
## 00:41​ Receiving our mail
## 04:37​ STARTTLS
## 09:21​ MTA-STS
## 11:40​ DANE
## 14:40​ Summary

# Segment 3 - E-Mail, Part III
## 00:00​ Introduction
## 00:40​ SMTP headers
## 07:08​ Mail Relay
## 11:13​ Spoofing "From"
## 13:46​ Sender Policy Framework
## 18:50​ Forwarded mail and the "Received:" header
## 22:12​ DomainKeys Identified Mail
## 26:41​ DMARC
## 30:17​ Summary
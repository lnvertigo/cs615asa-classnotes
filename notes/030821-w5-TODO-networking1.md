# Networking 1
## 1 | Layers
### 00:00​ Introduction
### 00:42​ The OSI Model
all people seem to need dominos pizza

- Political (org structure)
- Financial (the funding)
- Application (Layer 7)
- Presentation
- Session
- Transport (most of TCP)
- Network (inter-network packet transfer)
- Data Link (node-to-node communication [PPP,MAC,etc])
- Physical (medium and bit rate)

### 02:50​ Packet capture using tcpdump(8)

	sudo tcpdump -w /tmp/out port  80 2>/dev/null &
	curl -s -I http://www.cs.stevens.edu >/dev/null
	fg
	sudo chmod a+r /tmp/out
	tcpdump -r /tmp/out -XX -n - 1

### 03:57​ The TCP/IP Layer Model
### 05:13​ Link Layer Packets
### 07:02​ The IP Packet
### 10:07​ Source and Destination IP Addresses
### 12:02​ Comparing to Wireshark
### 13:20​ Summary

## 2 | IPv4 and CIDR Basics

### 01:03​ IPv4 address in binary
### 04:50​ Classless Inter-Domain Routing (CIDR)
### 06:47​ ipcalc example
### 09:58​ CIDR cheat sheet
### 11:12​ Summary

## 3 | IPv6 Basics
### 03:32​ The IPv6 Header
### 05:23​ IPv6 Address Representation
### 07:23​ Special Cases
### 08:46​ Address Scopes
### 10:47​ sipcalc(1) example
### 12:00​ Conclusion

## 4 | IP Allocation and Exhaustion
### 01:36​ The birth of IANA
### 03:01​ IP Allocation
### 07:22​ IPv4 Exhaustion
### 10:23​ IPv6 Address Space Size
### 13:21​ Summary and Exercises

## 5 | Physical Internet
### 00:54​ Layer 1
### 02:36​ Submarine Communications Cables I
### 04:16​ Shark Attack!
### 04:47​ Submarine Communications Cables II
### 07:00​ Internet Censorship
### 09:27​ Room 641A

## 6 | Network of Networks
### 02:13​ Autonomous Systems
### 03:51​ whois(1)
### 06:39​ traceroute(1)
### 09:34​ PeeringDB
### 12:16​ IXP Stats
### 14:24​ Summary and Questions
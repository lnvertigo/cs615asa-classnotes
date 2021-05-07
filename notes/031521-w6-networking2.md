# Segment 1 - Networking II, A Simple Request
## 00:00​ Introduction
## 00:52​ A Simple Request
simple HTTP request
```
	> telnet www.yahoo.com 80
	> HEAD / HTTP/1.0
```

- localhost connects to remotehost**** paying only attention to this for now
- localhost sends data
- remotehost replies with data

## 02:00​ ktrace(1)

```
	> tcpdump -w /tmp/simple.pcap port not 22 >/dev/null 2>&1 &
	> arp -d -a
	> ktrace -i telnet -K www.yahoo.com 80
	> HEAD / HTTP/1.0
```

## 03:08​ Analyzing kdump(1) output
	ktrace is a tool to trace other commands
	kdump reads output from ktrace

## 04:27​ /etc/nsswitch.conf
	man nsswitch.conf - explanation of conf file and what it has

## 05:38​ /etc/hosts
	man hosts.conf
takes precedence over dns
only contained localhost by default
asks dns if hostname is not found, in resolve.conf

## 06:33​ /etc/resolv.conf
	grep ^hosts /etc/nsswitch.conf
- if no entries in hosts, look up using dns
- resolve.conf contains nameserver

## 07:25​ Looking at socket(2) calls
kdump trace output should have some values from:
- resolv.conf
- nsswitch.conf

vi
	/socket - next instance of socket in the file

# Segment 2 - A Simple Request, Part II
## 01:11​ We're being scanned!
	tcpdump -t -n -r /tmp/simple.pcap | more
	ifconfig -a
	tcpdump -t -n -r /tmp/simple.pcap | head
		shows first two packets
	host 162.142.125.150 (from the first packet)
## 02:51​ censys.io
attack service detection/analysis service
RFC1918 - private address
NAT/public address has more info than private IP address
## 04:27​ ARP packets
	tcpdump -tnr /tmp/simple.pcap arp

ARP pkts are updates for the MAC addresses of other devices (like routers)
the "who-is" in the visualization

## 06:23​ DNS packets
	tcpdump -tnr /tmp/simple.pcap udp port 53

DNS uses pt 53, so here we go
asking DNS server for quadA records

## 07:47​ TCP packets
	tcpdump -tnr /tmp/simple.pcap tcp port 80 | head -3

tcp handshake packets
flags S, S., and . are the SYN/ACK pkts

	tcpdump -tnr /tmp/simple.pcap tcp port 80 | head -8 | tail -5

HEAD / HTTP/1.0 - 18 bytes (seq 1:18)

## 09:57​ Visualizing the packets
app layer
	- HTTP request 
	- DNS protocol 
transport layer 
	- tcp/udp
network layer
	- ip/ip6
link+phys layer
	- arp

# Segment 3 - Networking II: ARP and NDP
## 00:00​ Introduction
common protocols, ARP and NDP (ARP for ipv6)
## 00:37​ ARP
	arp -a
		display arp cache
need to be able to flush cache and capture packets

	sudo tcpdump -w(rite) /tmp/arp arp >/dev/null 2>&1 &
		run tcpdump that captures all arp traffic
	sudo arp -da
		flush cache
	ping -c 3 www.yahoo.com
		create traffic to cpature
	fg
		bring process to foreground
	sudo tcpdump -r(ead) /tmp/arp

who-has requests
	requests make by other systems to broadcast to all devices in the broadcast domain

is-at request
	our system's answers tothat contain where we are

gratuitous ARP

## 03:23​ ARP Diagram
requests from other systems asking who-has
the switch broadcasts the who-has to everyone in the domain
the is-at is unicast, but can be broadcast as a gratuitous is-at

## 04:50​ NDP
uses ICMP
	sudo tcpdump -w(rite) /tmp/icmp6 icmp6 >/dev/null 2>&1 &
	sudo ndp -c
		flush ncp table
	ping
	ndp -na
	fg
	sudo tcpdump -ntr /tmp/icmp6 | grep neighbor

	ifconfig
		for the linklocal address

neighbor advertisement/solicitation

ff02::1 - multicast address

## 08:14​ NDP Diagram
all systems have a linklocal address and a global address
to find a mac address, send a neighbor solicitation to "solicited node multicast address"
request is multicast to only the addresses that fit the snma group
then gets delivered

# Segment 4 - Networking II: ICMP
## 00:00​ Introduction
internet control msg protocol
## 00:30​ ping(1)
	sudo tcpdump /tmp/ping icmp > /dev/null 2>&1
	ping -c 3 www.yahoo.com
	fg
	sudo tcpdump -tnr /tmp/ping

## 02:00​ Data exfiltration using ICMP echo requests
echo requests and replies
- type
	- 0 - reply, 8 - request
- code field
- chksum
- identifier
- seq num
- data

putting in data:
	echo -n "let's exfil data" | od -A n -t x1 | tr -d ' '
		encode data into hex

	sudo tcpdump /tmp/ping icmp > /dev/null 2>&1
	ping -c 1 -p [echo result] www.yahoo.com
	fg
	sudo tcpdump -XX -tnr /tmp/ping
		-XX is full packet

## 03:36​ ping6(1)
pretty much the same but with icmp6 packets

## 04:36​ traceroute(1)
2 instances:
	tcpdump -ntv "ip and (icmp or udp)"
		run this first

	traceroute -n -q 1 www.yahoo.com

## 08:17​ traceroute6(1)
2 instances:
	tcpdump -ntv "ip6 and (udp or (icmp6 && (ip6[40] == 3) || ip6[40] == 1))"

	traceroute -n -q 1 www.yahoo.com

## 09:49​ traceroute illustrated
trick the routers in the path to the dest by incrementing the HLIM to expire for every router in the way

traceroute.txt - Only contains the basic traceroute to www.stevens.edu
	- traceroute works properly for both OmniOS and Fedora
	- snoop has not been finding any packets
	- had to install traceroute and tcpdump on Fedora, surprising

		snoop -o stevens-snoop host www.stevens.edu icmp,udp

		sudo tcpdump -w stevens-tcpdump -ntv "ip and (udp or icmp)"

		traceroute -n -q 1 www.stevens.edu

		Made a PCAP file with ICMP and UDP packets leading to the 104.16.12*.* network.

http.txt - Couldn't get the IPv6 to work.
	- pinging 6.ifconfig.pro didn't work on Omni, but worked fine on Fedora
	- installed telnet to Fedora, surprised I didn't have to install ping too >:/
	- honestly not sure of how to download files from AWS, so I just installed Wireshark to Fedora too

		root@ip-10-10-0-52:~# telnet 6.ifconfig.pro
		Trying 2604:180:f1::92...
		telnet: Unable to connect to remote host: Network is unreachable

		[fedora@ip-10-10-0-32 ~]$ ping 6.ifconfig.pro
		PING 6.ifconfig.pro(ifconfig.pro (2604:180:f1::92)) 56 data bytes
		64 bytes from ifconfig.pro (2604:180:f1::92): icmp_seq=1 ttl=50 time=44.4 ms
		64 bytes from ifconfig.pro (2604:180:f1::92): icmp_seq=2 ttl=50 time=17.7 ms
		64 bytes from ifconfig.pro (2604:180:f1::92): icmp_seq=3 ttl=50 time=19.3 ms
		64 bytes from ifconfig.pro (2604:180:f1::92): icmp_seq=4 ttl=50 time=17.6 ms
		^C
		--- 6.ifconfig.pro ping statistics ---
		4 packets transmitted, 4 received, 0% packet loss, time 3005ms
		rtt min/avg/max/mdev = 17.608/24.757/44.422/11.372 ms
		[fedora@ip-10-10-0-32 ~]$ telnet 6.ifconfig.pro
		Trying 2604:180:f1::92...
		telnet: connect to address 2604:180:f1::92: Connection refused

	- followed the blog post as well
	- using ipadm create-addr -T addrconf xnf0/v6 like Charles suggested on Slack didn't work

		root@ip-10-10-0-52:~# ipadm create-addr -T addrconf xnf0/v6
		root@ip-10-10-0-52:~# ping 6.ifconfig.pro
		ping: sendto No route to host
		root@ip-10-10-0-52:~# ping ifconfig.pro
		ifconfig.pro is alive

	- tried tcpdumping anyway, got some packets
		Reading through Wireshark gave me ICMPv6, a TCP/SYN packet, and a TCP/RST,ACK packet from when I was pinging the host in a separate shell.

visual-traceroute.txt

	I included the visual-traceroute-browser results in the tar archive. Used the results of traceroute [ip] in OmniOS to compare with the browser's.

I honestly made this assignment more complicated than it should have been by:
	- trying to do everything on my own instead of following the videos for the week's lectures
	- using a VM to connect to linux-lab instead of just from my computer alone
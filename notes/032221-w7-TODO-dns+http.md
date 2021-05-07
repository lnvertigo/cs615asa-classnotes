# Segment 1 - The Domain Name System, Part I

## 00:00​ Introduction
## 02:11​ ARPANET, the early days
from last week:
	/etc/nsswitch.conf
	/etc/hosts.conf

ARPANET used to have a central hosts.conf file
## 04:10​ RFC597 - RFC810
earliest iteration of the hosts database
	curl -s https://tools.ietf.org/rfc/rfc597.txt | more
		Dec 1973
		octal and decimal, not IP
RFC810
	1982
	Elizabeth Feinler + others
	defined the DoD host table

## 07:38​ Address allocation at SRI-NIC
- addresses were assigned manually by calling NIC (network info center)
- feinler developed the concept of "domains"
- paul mockapetris proposed a DNS in RFC882 and RFC883 in 1983
- first UNIX name server implementation: BIND (Berkeley Internet Name Domain) in 1984

## 08:59​ The domain name space
tree-like hierarchical structure

. (root)
zones (TLD - top level domains)
- com
- edu
- org
2nd lvl domains
- stevens
- yahoo
3rd lvl domains
- www
- cs

Zone authority can be delegated.
Every node must have a label and some Resource Records (RRs)
	DNS records, like A, CNAME, and MX

eg: www.cs.stevens.edu.

The trailing dot is usually left out, but it's still needed in networking.
CAN GET THROUGH PAYWALLS BY USING IT ON THE LIKES OF nytimes.com.
HOLY SHIT

## 12:31​ TLDs
RFC920 added:
- com
- edu
- gov
- mil
- org
- arpa  (temp use by ARPA, but stayed)

Newer TLDs:
- country-code TLDs (ccTLD)
	- de
	- fr
	- ar
- internationalized generic TLDs (IDN gTLD)
- sponsored TLDs (sTLD)
	- cat
	- jobs
	- xxx
- new generic TLDs
	- auto
	- blue
	- literally anything really

Can get the root file from:
	curl -s ftp://rs.internic.net/domain/root.zone > /tmp/root
	more /tmp/root

	grep "IN	NS	" /tmp/root
		find all unique TLDs

	grep "IN	NS	" /tmp/root | awk `{print $1}` | sort -u | wc -l
		get the count of all unique TLDs

## 15:40​ Layer 9
managing the domains

IANA manages root zone
domain name registries handles other zones
	registries control allocation policies
domain name registrars handles domain name registries

You cannot just use any domain that exists because the registry can take it down if they want to. boo.ru for example, which is why they were forced to use booru.org instead.

# Segment 2 - The Domain Name System, Part II

## 00:26​ A simple lookup
	sudo tcpdump -w /tmp/dns.pcap port 53 >/dev/null 2>&1 &
		should know what this does by now
	nslookup www.yahoo.com
	fg + ctrlC

compare results to resolve.conf
	tcpdump -r pcap

open the pcap in wireshark
- left panel of a packet should have "Domain Name System (query) and have a query or RRs 
- flags contain authority flag
  
- authoritative ns provide auth answers
- resolver relays info by asking other ns first

- one request can have many queries and have multiple RR types, like CNAME, A, and AAAA
	like MAGIC :)

## 05:06​ Getting an authoritative response
	sudo tcpdump -w /tmp/dns.pcap port 53 >/dev/null 2>&1 &
	nslookup -query=ns www.yahoo.com
		gives auth location + origin
	nslookup [auth location] [auth location origin]
	fg + ctrlC

	read tcpdump

## 07:49​ Resolving a name top-down
(Assumes that we're managing a resolver NS)
- ask the root for the www.yahoo.com A record
	- gives NS responsible for .com
- ask .com server for .yahoo
- ask .yahoo server for www

DNS Query Name Minimization
- labels stripped to be private

resolve NSs cache results

## 11:28​ tcpdump on a caching resolver (on NetBSD)
	echo "named=YES" | sudo tee -a /etc/rc.conf > /dev/null
	sudo vi /etc/resolve.conf
		add nameserver ::1 (localhost)
	sudo tcpdump -w dns.pcap port 53 >
	sudo /etc/rc.d/named start
	sudo rndc flush
	nslookup www.yahoo.com
	fg ctrlC
	tcpdump -r dns.pcap

# Segment 3 - The Domain Name System, Part III

## 00:00​ Introduction
## 01:52​ Finding the root nameservers
	dig -t ns . | more
		find the root zone responsible for the DNS
	
/etc/namedb/root.cache
	new resolvers need this info

	ftp -o - ftp://rs.internic.net/domain/named.cache | more

## 05:48​ root-servers.org
there are more than just 13 NSs, they're authorities

root-servers.org shows a map of the root NSs

root servers use anycast to appear as a single IP despite having hundreds of locations globally

## 08:54​ Different RR types
	dig -t ns . | more
- NS records
	- use internet/IN class of RRs
	- has TTL (root have about 5 days of TTL)
- SOA records
	- start of auth
	- defines info of zone
- CAA
	- info about cert authorities

## 13:24​ Reverse lookups
	tcpdump -w
	host -t ptr 155.246.56.11
	fg ctrl+C
wireshark
- start w/ root
- find zone
- "who's responsible for 155.in-addr.arpa?"
- blocks? TODO

delegation of zone auths along netblock allocation lines

## 16:26​ DNSSEC
	tcpdump -w
	dig www.iana.org
	fg ctrl+C

how to check if the tcpdump is authentic?
	ad flag - authenticated

	tcpdump -w
	sudo rnds flush
	dig www.iana.org
wireshark
- has NS records
- has an RRSIG record ***
- DS records - delegation signers

DNS table has location of public key for delegation servers, and uses it to verify
- DNSKEY type

## 20:20​ Implications and Considerations TODO
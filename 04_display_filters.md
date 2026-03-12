# Display Filters

## What are Display Filters?
- Display filters are filters applied after traffic is captured
- They show only matching packets from already captured data
- All packets are still saved in capture file
- Only matching packets are visible on screen
- Can be changed anytime during analysis
- Uses Wireshark specific filter syntax
- Much more powerful than capture filters
- Most commonly used feature in Wireshark

---

## How to Apply Display Filters

### Method 1 - Filter Toolbar
```
Type filter in filter bar at top
Press Enter to apply
Press X button to clear filter
```

### Method 2 - Right Click
```
Right click any field in packet details
Apply as Filter → Selected
Apply as Filter → Not Selected
Prepare as Filter → Selected
```

### Method 3 - Analyze Menu
```
Analyze → Display Filters
Select saved filter
Click OK
```

---

## Display Filter Syntax

### Basic Syntax
```bash
# filter by protocol
http
dns
tcp
udp
icmp
ftp
ssh
arp
tls

# filter by field value
ip.addr == 192.168.1.1
tcp.port == 80
http.request.method == "GET"
```

### Comparison Operators
```bash
== equal to
!= not equal to
> greater than
< less than
>= greater than or equal
<= less than or equal
contains contains string
matches regular expression
```

---

## Filter by IP Address
```bash
# traffic to or from IP
ip.addr == 192.168.1.1

# traffic from specific IP
ip.src == 192.168.1.1

# traffic to specific IP
ip.dst == 192.168.1.1

# exclude specific IP
ip.addr != 192.168.1.1

# filter entire subnet
ip.addr == 192.168.1.0/24

# filter between two IPs
ip.src == 192.168.1.1 and ip.dst == 192.168.1.2
```

---

## Filter by Port
```bash
# filter by TCP port
tcp.port == 80
tcp.port == 443
tcp.port == 22
tcp.port == 21

# filter by source port
tcp.srcport == 80

# filter by destination port
tcp.dstport == 80

# filter by UDP port
udp.port == 53
udp.port == 67

# filter port range
tcp.port >= 1024 and tcp.port <= 65535
```

---

## Filter by Protocol
```bash
# show only HTTP traffic
http

# show only DNS traffic
dns

# show only TCP traffic
tcp

# show only UDP traffic
udp

# show only ICMP traffic
icmp

# show only ARP traffic
arp

# show only FTP traffic
ftp

# show only SSH traffic
ssh

# show only TLS traffic
tls

# show only DHCP traffic
dhcp

# show only SMB traffic
smb
```

---

## HTTP Display Filters
```bash
# show all HTTP traffic
http

# show HTTP GET requests only
http.request.method == "GET"

# show HTTP POST requests only
http.request.method == "POST"

# show HTTP responses only
http.response

# show specific HTTP response code
http.response.code == 200
http.response.code == 404
http.response.code == 500

# show HTTP traffic to specific host
http.host == "example.com"

# show HTTP requests containing specific URI
http.request.uri contains "login"
http.request.uri contains "password"

# show HTTP traffic with specific user agent
http.user_agent contains "Mozilla"
```

---

## DNS Display Filters
```bash
# show all DNS traffic
dns

# show DNS queries only
dns.flags.response == 0

# show DNS responses only
dns.flags.response == 1

# show DNS queries for specific domain
dns.qry.name == "google.com"
dns.qry.name contains "google"

# show DNS errors
dns.flags.rcode != 0

# show specific DNS record type
dns.qry.type == 1
```

---

## TCP Display Filters
```bash
# show all TCP traffic
tcp

# show TCP SYN packets
tcp.flags.syn == 1

# show TCP SYN ACK packets
tcp.flags.syn == 1 and tcp.flags.ack == 1

# show TCP FIN packets
tcp.flags.fin == 1

# show TCP RST packets
tcp.flags.reset == 1

# show TCP ACK packets
tcp.flags.ack == 1

# show TCP retransmissions
tcp.analysis.retransmission

# show TCP errors
tcp.analysis.flags

# show TCP zero window
tcp.analysis.zero_window

# show TCP duplicate ACK
tcp.analysis.duplicate_ack
```

---

## ICMP Display Filters
```bash
# show all ICMP traffic
icmp

# show ICMP ping requests
icmp.type == 8

# show ICMP ping replies
icmp.type == 0

# show ICMP destination unreachable
icmp.type == 3

# show ICMP time exceeded
icmp.type == 11
```

---

## Filter by Packet Size
```bash
# packets larger than 1000 bytes
frame.len > 1000

# packets smaller than 100 bytes
frame.len < 100

# packets of exact size
frame.len == 64

# TCP payload size
tcp.len > 0
```

---

## Filter by Time
```bash
# packets after specific time
frame.time >= "2024-01-01 00:00:00"

# packets before specific time
frame.time <= "2024-12-31 23:59:59"

# packets in time range
frame.time >= "2024-01-01 00:00:00" and frame.time <= "2024-01-01 01:00:00"
```

---

## Combining Display Filters

### AND operator
```bash
# HTTP traffic from specific IP
http and ip.src == 192.168.1.1

# DNS traffic to specific server
dns and ip.dst == 8.8.8.8

# TCP traffic on specific port from IP
tcp.port == 80 and ip.addr == 192.168.1.1
```

### OR operator
```bash
# HTTP or HTTPS traffic
http or tls

# DNS or DHCP traffic
dns or dhcp

# traffic from two IPs
ip.src == 192.168.1.1 or ip.src == 192.168.1.2
```

### NOT operator
```bash
# exclude ICMP traffic
not icmp

# exclude specific IP
not ip.addr == 192.168.1.1

# exclude broadcast traffic
not eth.dst == ff:ff:ff:ff:ff:ff
```

### Complex Combinations
```bash
# HTTP POST requests from specific IP
http.request.method == "POST" and ip.src == 192.168.1.1

# DNS queries excluding local DNS
dns.flags.response == 0 and not ip.dst == 192.168.1.1

# TCP SYN packets excluding established connections
tcp.flags.syn == 1 and tcp.flags.ack == 0
```

---

## Security Analysis Filters
```bash
# detect port scan SYN packets
tcp.flags.syn == 1 and tcp.flags.ack == 0

# detect large number of RST packets
tcp.flags.reset == 1

# find cleartext credentials in HTTP
http.request.method == "POST" and http.request.uri contains "login"

# find suspicious DNS queries
dns.qry.name contains ".tk" or dns.qry.name contains ".xyz"

# find large DNS responses possible DNS tunneling
dns and frame.len > 512

# find FTP credentials
ftp.request.command == "USER" or ftp.request.command == "PASS"

# find Telnet traffic cleartext
telnet

# find unencrypted HTTP login
http contains "password"
http contains "passwd"
http contains "username"
```

---

## Useful Built in Filter Buttons
```
HTTP → shows HTTP traffic
TCP Errors → shows TCP problems
No ARP → hides ARP traffic
IPv4 only → shows only IPv4
IPv6 only → shows only IPv6
```

---

## Saving Display Filters
```
Click bookmark icon in filter bar
Save current filter
Give it a name
Access saved filters anytime
```
---


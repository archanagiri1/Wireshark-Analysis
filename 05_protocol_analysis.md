# Protocol Analysis

## What is Protocol Analysis?
- Protocol analysis is the process of examining network protocols
  in captured traffic
- It helps understand how network communication works
- Identifies normal and abnormal network behavior
- Used for troubleshooting, security analysis, and forensics
- Wireshark automatically dissects and displays protocol details
- Each protocol has its own fields and structure in Wireshark

---

## OSI Model and Protocols

### Layer 1 - Physical Layer
- Deals with physical transmission of data
- Cables, signals, bits
- Wireshark shows frame details at this layer

### Layer 2 - Data Link Layer
- Handles MAC addresses and frames
- Protocols: Ethernet, ARP, WiFi
- Wireshark shows Ethernet frame details

### Layer 3 - Network Layer
- Handles IP addresses and routing
- Protocols: IP, ICMP, ARP
- Wireshark shows IP header details

### Layer 4 - Transport Layer
- Handles end to end communication
- Protocols: TCP, UDP
- Wireshark shows TCP and UDP details

### Layer 5 6 7 - Application Layer
- Handles application communication
- Protocols: HTTP, DNS, FTP, SSH, SMTP
- Wireshark shows full application data

---

## Protocol Hierarchy Statistics
- Shows breakdown of all protocols in capture
- Displays percentage of each protocol
- Helps understand traffic composition

### How to Access
```
Statistics → Protocol Hierarchy
```

---

## ARP Protocol Analysis

### What is ARP?
- ARP stands for Address Resolution Protocol
- Resolves IP addresses to MAC addresses
- Works at Layer 2 and Layer 3
- Essential for local network communication

### ARP Packet Types
- ARP Request → who has this IP tell me your MAC
- ARP Reply → I have that IP here is my MAC

### ARP Display Filters
```bash
# show all ARP traffic
arp

# show ARP requests only
arp.opcode == 1

# show ARP replies only
arp.opcode == 2

# show ARP for specific IP
arp.dst.proto_ipv4 == 192.168.1.1
```

### ARP Security Issues
- ARP Spoofing → attacker sends fake ARP replies
- ARP Poisoning → poisoning ARP cache of victims
- Man in the Middle attack via ARP spoofing
- Detection: multiple ARP replies for same IP from different MACs

---

## ICMP Protocol Analysis

### What is ICMP?
- ICMP stands for Internet Control Message Protocol
- Used for network diagnostics and error reporting
- Used by ping and traceroute commands
- Works at Layer 3

### ICMP Message Types
- Type 0 → Echo Reply (ping reply)
- Type 3 → Destination Unreachable
- Type 5 → Redirect
- Type 8 → Echo Request (ping request)
- Type 11 → Time Exceeded (traceroute)
- Type 13 → Timestamp Request
- Type 14 → Timestamp Reply

### ICMP Display Filters
```bash
# show all ICMP traffic
icmp

# show ping requests
icmp.type == 8

# show ping replies
icmp.type == 0

# show destination unreachable
icmp.type == 3

# show time exceeded
icmp.type == 11
```

### ICMP Security Issues
- ICMP flood attack (ping flood)
- ICMP tunneling for data exfiltration
- Large ICMP packets may indicate tunneling
- Ping of death (oversized ICMP packets)

---

## TCP Protocol Analysis

### What is TCP?
- TCP stands for Transmission Control Protocol
- Connection oriented reliable protocol
- Guarantees delivery and order of packets
- Uses three way handshake
- Works at Layer 4

### TCP Three Way Handshake
- Step 1 → Client sends SYN
- Step 2 → Server sends SYN ACK
- Step 3 → Client sends ACK
- Connection established

### TCP Four Way Termination
- Step 1 → Client sends FIN
- Step 2 → Server sends ACK
- Step 3 → Server sends FIN
- Step 4 → Client sends ACK
- Connection terminated

### TCP Flags
- SYN → synchronize sequence numbers
- ACK → acknowledge received data
- FIN → finish connection
- RST → reset connection
- PSH → push data immediately
- URG → urgent data
- ECE → ECN echo
- CWR → congestion window reduced

### TCP Display Filters
```bash
# show all TCP
tcp

# show SYN packets
tcp.flags.syn == 1

# show SYN ACK
tcp.flags.syn == 1 and tcp.flags.ack == 1

# show FIN packets
tcp.flags.fin == 1

# show RST packets
tcp.flags.reset == 1

# show retransmissions
tcp.analysis.retransmission

# show duplicate ACKs
tcp.analysis.duplicate_ack

# show zero window
tcp.analysis.zero_window
```

### TCP Security Issues
- SYN flood attack → too many SYN packets
- Port scanning → SYN packets to many ports
- TCP session hijacking
- TCP RST attack

---

## UDP Protocol Analysis

### What is UDP?
- UDP stands for User Datagram Protocol
- Connectionless unreliable protocol
- No guarantee of delivery or order
- Faster than TCP
- Used for real time communication
- Works at Layer 4

### UDP Use Cases
- DNS queries
- DHCP
- VoIP calls
- Video streaming
- Online gaming
- TFTP

### UDP Display Filters
```bash
# show all UDP
udp

# show UDP on specific port
udp.port == 53
udp.port == 67
udp.port == 68

# show UDP by source port
udp.srcport == 53

# show UDP by destination port
udp.dstport == 53

# show large UDP packets
udp.length > 512
```

### UDP Security Issues
- UDP flood attack
- DNS amplification attack
- UDP port scanning
- VoIP eavesdropping

---

## DNS Protocol Analysis

### What is DNS?
- DNS stands for Domain Name System
- Resolves domain names to IP addresses
- Uses UDP port 53 for queries
- Uses TCP port 53 for zone transfers
- Works at Application Layer

### DNS Query Types
- A → IPv4 address lookup
- AAAA → IPv6 address lookup
- MX → mail server lookup
- NS → name server lookup
- CNAME → alias lookup
- PTR → reverse lookup
- TXT → text record lookup

### DNS Display Filters
```bash
# show all DNS
dns

# show DNS queries
dns.flags.response == 0

# show DNS responses
dns.flags.response == 1

# show DNS for specific domain
dns.qry.name == "google.com"
dns.qry.name contains "google"

# show DNS errors
dns.flags.rcode != 0

# show large DNS packets
dns and frame.len > 512
```

### DNS Security Issues
- DNS spoofing → fake DNS responses
- DNS tunneling → hiding data in DNS queries
- DNS amplification → DDoS attack
- DNS cache poisoning

---

## HTTP Protocol Analysis

### What is HTTP?
- HTTP stands for Hypertext Transfer Protocol
- Used for web communication
- Plain text protocol not encrypted
- Uses TCP port 80
- Works at Application Layer

### HTTP Methods
- GET → request data from server
- POST → send data to server
- PUT → update data on server
- DELETE → delete data on server
- HEAD → request headers only
- OPTIONS → check allowed methods

### HTTP Response Codes
- 200 → OK success
- 301 → Moved Permanently
- 302 → Found redirect
- 400 → Bad Request
- 401 → Unauthorized
- 403 → Forbidden
- 404 → Not Found
- 500 → Internal Server Error

### HTTP Display Filters
```bash
# show all HTTP
http

# show GET requests
http.request.method == "GET"

# show POST requests
http.request.method == "POST"

# show specific response code
http.response.code == 200
http.response.code == 404

# show specific host
http.host == "example.com"

# show login pages
http.request.uri contains "login"

# find credentials in POST
http.request.method == "POST" and http.request.uri contains "login"
```

### HTTP Security Issues
- Credentials sent in plain text
- Session cookies visible in traffic
- Sensitive data exposure
- HTTP to HTTPS downgrade attack

---

## FTP Protocol Analysis

### What is FTP?
- FTP stands for File Transfer Protocol
- Used for transferring files between systems
- Plain text protocol not encrypted
- Uses TCP port 21 for control
- Uses TCP port 20 for data transfer
- Works at Application Layer

### FTP Commands
- USER → send username
- PASS → send password
- LIST → list directory contents
- RETR → retrieve file
- STOR → store file
- QUIT → disconnect

### FTP Display Filters
```bash
# show all FTP traffic
ftp

# show FTP commands
ftp.request.command == "USER"
ftp.request.command == "PASS"
ftp.request.command == "RETR"
ftp.request.command == "STOR"

# show FTP data transfer
ftp-data
```

### FTP Security Issues
- Username and password sent in plain text
- Files transferred without encryption
- Anonymous FTP access misconfiguration
- FTP bounce attack

---

## SMTP Protocol Analysis

### What is SMTP?
- SMTP stands for Simple Mail Transfer Protocol
- Used for sending emails
- Uses TCP port 25
- Plain text protocol
- Works at Application Layer

### SMTP Commands
- HELO → identify sender
- MAIL FROM → sender email address
- RCPT TO → recipient email address
- DATA → start email body
- QUIT → end session

### SMTP Display Filters
```bash
# show all SMTP traffic
smtp

# show SMTP commands
smtp.req.command == "MAIL"
smtp.req.command == "RCPT"
smtp.req.command == "DATA"
```

### SMTP Security Issues
- Email content visible in plain text
- Sender spoofing
- Spam relay misconfiguration

---

## TLS and HTTPS Analysis

### What is TLS?
- TLS stands for Transport Layer Security
- Encrypts network communication
- HTTPS uses TLS over HTTP
- Uses TCP port 443
- Cannot read encrypted content in Wireshark

### TLS Display Filters
```bash
# show all TLS traffic
tls

# show TLS handshake
tls.handshake

# show TLS client hello
tls.handshake.type == 1

# show TLS server hello
tls.handshake.type == 2

# show TLS certificate
tls.handshake.type == 11

# show SNI server name  tls.handshake.extensions_server_name
```

### What Can Be Seen in TLS
- TLS version used
- Cipher suites negotiated
- Certificate details
- Server name via SNI
- Cannot see actual data content

---

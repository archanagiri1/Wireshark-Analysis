# Suspicious Traffic Analysis

## What is Suspicious Traffic Analysis?
- Suspicious traffic analysis is the process of identifying
  abnormal or malicious network activity in captures
- It combines knowledge of all protocols to find threats
- It is the most important skill in network security analysis
- Used in incident response, threat hunting, and forensics
- Requires understanding of normal traffic patterns
- Abnormal patterns indicate attacks, malware, or breaches
- Also called Network Forensics or Network Threat Hunting

---

## Why Suspicious Traffic Analysis is Important?
- Detects active attacks on network
- Finds malware communicating with attackers
- Identifies data exfiltration attempts
- Reveals unauthorized access and lateral movement
- Finds policy violations on network
- Helps reconstruct attack timeline
- Provides evidence for incident response
- Identifies compromised hosts on network

---

## Understanding Normal Traffic
- Before finding suspicious traffic know what is normal
- Normal traffic patterns:
  - DNS queries to known DNS servers
  - HTTP and HTTPS to known websites
  - Regular business application traffic
  - Email traffic to mail servers
  - Internal network communication
  - Regular backup and update traffic

---

## General Suspicious Indicators

### Volume Anomalies
- Sudden spike in traffic volume
- Unusually high DNS query rate
- Large data transfers at unusual times
- High number of failed connections

### Timing Anomalies
- Traffic at unusual hours
- Regular interval beaconing traffic
- Sudden burst of connections

### Destination Anomalies
- Connections to unknown external IPs
- Traffic to unusual countries
- Connections to known malicious IPs
- Traffic to Tor exit nodes

### Protocol Anomalies
- Unusual protocols on well known ports
- HTTP on port 443
- DNS on non standard ports
- Encrypted traffic on plain text ports

---

## Port Scan Detection

### Types of Port Scans
- SYN Scan → stealth scan most common
- Connect Scan → full TCP handshake
- FIN Scan → FIN flag only
- NULL Scan → no flags
- XMAS Scan → FIN URG PSH flags
- UDP Scan → UDP packets to all ports

### Port Scan Indicators
- Many connections to sequential ports
- Many RST responses from target
- SYN packets to many ports from single source
- Short time between connection attempts
- Many ICMP port unreachable messages

### Port Scan Detection Filters
```bash
# detect SYN scan
tcp.flags.syn == 1 and tcp.flags.ack == 0

# detect many RST responses indicating closed ports
tcp.flags.reset == 1

# detect FIN scan
tcp.flags.fin == 1 and tcp.flags.ack == 0 and tcp.flags.syn == 0

# detect NULL scan no flags
tcp.flags == 0

# detect XMAS scan
tcp.flags.fin == 1 and tcp.flags.urg == 1 and tcp.flags.push == 1

# detect UDP scan
udp and icmp.type == 3 and icmp.code == 3
```

---

## Network Reconnaissance Detection

### ARP Scanning
- Attacker sends ARP requests to all hosts
- Finds alive hosts on local network
- Fast and reliable on local network

### ARP Scan Detection Filters
```bash
# show all ARP requests
arp.opcode == 1

# show ARP requests from single source
arp.opcode == 1 and arp.src.proto_ipv4 == attacker_ip

# detect ARP sweep many requests quickly
arp
# check if many different target IPs in short time
```

### ICMP Sweep Detection
```bash
# show ping sweep
icmp.type == 8

# show many pings from single source
icmp.type == 8 and ip.src == attacker_ip
```

---

## Brute Force Attack Detection

### SSH Brute Force
- Attacker tries many username password combinations
- Many failed authentication attempts
- High volume of SSH connections from single IP

### SSH Brute Force Detection Filters
```bash
# show SSH traffic
tcp.port == 22

# show many connections to SSH port
tcp.flags.syn == 1 and tcp.dstport == 22

# show SSH traffic from specific source
tcp.port == 22 and ip.src == attacker_ip
```

### HTTP Brute Force
- Many POST requests to login page
- Same URI with different credentials
- High volume of 401 or 403 responses

### HTTP Brute Force Detection Filters
```bash
# show many POST to login
http.request.method == "POST" and http.request.uri contains "login"

# show many failed authentication responses
http.response.code == 401
http.response.code == 403

# show POST requests from single source
http.request.method == "POST" and ip.src == attacker_ip
```

### FTP Brute Force Detection
```bash
# show FTP login attempts
ftp.request.command == "USER" or ftp.request.command == "PASS"

# show FTP failures
ftp.response.code == 530
```

---

## Malware Communication Detection

### Command and Control Beaconing
- Malware regularly contacts attacker server
- Traffic at regular intervals
- Same destination IP or domain
- Small consistent packet sizes
- Often uses HTTP HTTPS or DNS

### Beaconing Detection Filters
```bash
# show regular HTTP connections to same destination
http and ip.dst == suspicious_ip

# show regular DNS queries to same domain
dns.qry.name contains "suspicious_domain"

# show regular connections at fixed intervals
# use Statistics → IO Graph to visualize timing
```

### Malware HTTP Communication
- Malware uses HTTP for command and control
- Suspicious user agents
- Connections to IP addresses not domains
- Unusual URI patterns
- Encoded or encrypted POST data

### Malware HTTP Detection Filters
```bash
# show HTTP connections to IP directly not domain
http and not http.host matches "[a-zA-Z]"

# show suspicious user agents
http.user_agent contains "python"
http.user_agent contains "curl"
http.user_agent == ""

# show encoded POST data
http.request.method == "POST" and http.request.uri contains "."

# show HTTP on unusual ports
http and not tcp.port == 80
```

---

## Data Exfiltration Detection

### What is Data Exfiltration?
- Attacker stealing data from network
- Sending stolen data to external server
- Can use any protocol to hide exfiltration
- Common methods: HTTP POST DNS FTP ICMP

### HTTP Exfiltration Detection
```bash
# show large HTTP POST requests possible data upload
http.request.method == "POST" and http.content_length > 10000

# show uploads to external servers
http.request.method == "POST" and not ip.dst == internal_network

# show base64 encoded data in POST
http.request.method == "POST" and http contains "base64"
```

### DNS Exfiltration Detection
```bash
# show large DNS queries
dns and frame.len > 150

# show many unique subdomains same domain
dns.qry.name contains "attacker_domain"

# show high volume DNS from single host
dns and ip.src == suspicious_host
```

### FTP Exfiltration Detection
```bash
# show FTP file uploads
ftp.request.command == "STOR"

# show FTP data transfer
ftp-data

# show large FTP transfers
ftp-data and frame.len > 1000
```

### ICMP Exfiltration Detection
```bash
# show large ICMP packets
icmp and frame.len > 100

# show ICMP with large payload
icmp.type == 8 and data.len > 64
```

---

## Lateral Movement Detection

### What is Lateral Movement?
- Attacker moving from one compromised host to others
- Using legitimate protocols and credentials
- Expanding access across network
- Common techniques: SMB RDP WMI PsExec

### SMB Lateral Movement Detection
```bash
# show SMB traffic
smb or smb2

# show SMB authentication
smb2.cmd == 1

# show SMB file access
smb2.cmd == 5

# show SMB from unexpected hosts
smb and ip.src == suspicious_host
```

### RDP Lateral Movement Detection
```bash
# show RDP traffic
tcp.port == 3389

# show RDP connections from unexpected sources
tcp.dstport == 3389 and ip.src == suspicious_host

# show many RDP connection attempts
tcp.flags.syn == 1 and tcp.dstport == 3389
```

---

## Man in the Middle Attack Detection

### ARP Spoofing Detection
- Attacker sends fake ARP replies
- Multiple MAC addresses for same IP
- MAC address changes for gateway IP

### ARP Spoofing Detection Filters
```bash
# show all ARP replies
arp.opcode == 2

# show duplicate IP to MAC mappings
arp
# look for same IP with different MAC addresses

# show ARP replies from unexpected sources
arp.opcode == 2 and arp.src.proto_ipv4 == gateway_ip
# check if MAC matches real gateway MAC
```

### SSL Stripping Detection
- Attacker downgrades HTTPS to HTTP
- Victim connects over plain HTTP
- Attacker connects to server over HTTPS
- All data visible to attacker

### SSL Stripping Detection Filters
```bash
# show HTTP traffic that should be HTTPS
http and http.host contains "bank"
http and http.host contains "mail"
http and http.host contains "login"

# show mixed HTTP and HTTPS to same host
http.host == "example.com"
```

---

## Ransomware Traffic Detection

### Ransomware Network Indicators
- DNS queries to random looking domains
- Large encrypted connections to external servers
- SMB scanning for file shares
- Rapid file access across network shares
- Communication with known ransomware IPs

### Ransomware Detection Filters
```bash
# show DNS to random domains DGA
dns.flags.rcode == 3

# show SMB scanning
smb and tcp.flags.syn == 1

# show large outbound transfers encryption key exchange
tcp and ip.dst == external_ip and frame.len > 1000

# show connections to many internal hosts
tcp.flags.syn == 1 and ip.src == infected_host
```

---

## Credential Theft Detection

### Cleartext Credential Detection
```bash
# find HTTP credentials
http contains "password"
http contains "passwd"
http.request.method == "POST" and http.request.uri contains "login"

# find FTP credentials
ftp.request.command == "USER"
ftp.request.command == "PASS"

# find Telnet credentials
telnet

# find SMTP credentials
smtp.req.command == "AUTH"
```

### Kerberoasting Detection
- Attacker requests service tickets for cracking
- Many TGS requests in short time
- Requests for service accounts

### NTLM Hash Capture Detection
```bash
# show NTLM authentication
ntlmssp

# show NTLM challenge response
ntlmssp.identifier == "NTLMSSP"
```

---

## Suspicious Traffic Analysis Workflow

### Step 1 - Get Overview
```
Statistics → Protocol Hierarchy
Statistics → Conversations
Statistics → Endpoints
Statistics → IO Graph
```

### Step 2 - Identify Hosts
```bash
# find all communicating hosts
# check Statistics → Endpoints → IPv4
# identify internal vs external hosts
# note any unexpected external IPs
```

### Step 3 - Check DNS Traffic
```bash
dns.flags.rcode == 3
dns and frame.len > 150
dns.qry.name contains suspicious_keywords
```

### Step 4 - Check for Scanning
```bash
tcp.flags.syn == 1 and tcp.flags.ack == 0
arp.opcode == 1
icmp.type == 8
```

### Step 5 - Check HTTP Traffic
```bash
http.request.method == "POST"
http contains "password"
http.user_agent contains "curl"
http.response.code == 401
```

### Step 6 - Check for Exfiltration
```bash
http.request.method == "POST" and http.content_length > 10000
dns and frame.len > 150
icmp and frame.len > 100
ftp.request.command == "STOR"
```

### Step 7 - Check for Lateral Movement
```bash
smb or smb2
tcp.dstport == 3389
tcp.dstport == 22
```

### Step 8 - Document Findings
- Note all suspicious IPs and domains
- Record timestamps of suspicious activity
- Capture screenshots as evidence
- Create timeline of events
- Write detailed analysis report

---
## Important Points
- Always establish baseline of normal traffic first
- Single suspicious packet may not indicate attack
- Look for patterns and correlations across protocols
- Timestamps are critical for building attack timeline
- Document everything with screenshots and notes
- Combine multiple filters for precise detection
- Statistics menu gives best overview of traffic
- Follow streams to understand complete conversations
- Cross reference suspicious IPs with threat intelligence
- Always perform analysis only on authorized captures
```

---

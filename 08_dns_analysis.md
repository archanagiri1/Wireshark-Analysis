# DNS Analysis

## What is DNS Analysis?
- DNS Analysis is the process of examining DNS traffic in captures
- DNS is one of the most important protocols to analyze
- Every internet connection starts with a DNS query
- DNS traffic reveals websites visited even on HTTPS
- Used for troubleshooting, security analysis, and forensics
- DNS is often abused for data exfiltration and tunneling
- Works on UDP port 53 for queries
- Works on TCP port 53 for zone transfers and large responses

---

## Why DNS Analysis is Important?
- Reveals all domains visited by a host
- Detects malware communicating with command and control servers
- Finds DNS tunneling used for data exfiltration
- Detects DNS spoofing and poisoning attacks
- Identifies fast flux domains used by botnets
- Finds suspicious or malicious domain lookups
- Helps reconstruct browsing history
- Reveals internal network structure

---

## DNS Packet Structure

### DNS Header
- Transaction ID → unique identifier for query and response pair
- Flags → QR opcode AA TC RD RA Z RCODE
- Questions → number of questions in packet
- Answer RRs → number of answers
- Authority RRs → number of authority records
- Additional RRs → number of additional records

### DNS Flags
- QR → 0 for query 1 for response
- Opcode → type of query 0 is standard
- AA → authoritative answer
- TC → truncated message
- RD → recursion desired
- RA → recursion available
- RCODE → response code 0 is no error

### DNS Response Codes
- 0 → No Error success
- 1 → Format Error bad query
- 2 → Server Failure server problem
- 3 → NXDOMAIN domain does not exist
- 4 → Not Implemented query type not supported
- 5 → Refused query refused by server

---

## DNS Record Types

### A Record
- Maps hostname to IPv4 address
- Most common DNS record type
- Example: google.com → 142.250.190.46

### AAAA Record
- Maps hostname to IPv6 address
- Example: google.com → 2607:f8b0:4004:c09::64

### CNAME Record
- Canonical name alias record
- Points one hostname to another hostname
- Example: www.example.com → example.com

### MX Record
- Mail Exchange record
- Points to mail server for domain
- Has priority value lower is higher priority

### NS Record
- Name Server record
- Points to authoritative DNS server for domain
- Example: example.com → ns1.example.com

### PTR Record
- Pointer record for reverse DNS lookup
- Maps IP address to hostname
- Used in reverse DNS zones

### TXT Record
- Text record
- Used for SPF DKIM DMARC verification
- Can contain arbitrary text data

### SOA Record
- Start of Authority record
- Contains zone administrative information
- Primary name server and admin contact

### SRV Record
- Service record
- Defines location of specific services
- Used by VoIP and messaging applications

---

## DNS Display Filters in Wireshark

### Basic DNS Filters
```bash
# show all DNS traffic
dns

# show DNS queries only
dns.flags.response == 0

# show DNS responses only
dns.flags.response == 1

# show DNS over UDP
dns and udp

# show DNS over TCP
dns and tcp
```

### Filter by Domain Name
```bash
# show queries for specific domain
dns.qry.name == "google.com"

# show queries containing keyword
dns.qry.name contains "google"
dns.qry.name contains "microsoft"
dns.qry.name contains "facebook"

# show queries for specific TLD
dns.qry.name contains ".tk"
dns.qry.name contains ".xyz"
dns.qry.name contains ".onion"
```

### Filter by Record Type
```bash
# show A record queries
dns.qry.type == 1

# show AAAA record queries
dns.qry.type == 28

# show MX record queries
dns.qry.type == 15

# show NS record queries
dns.qry.type == 2

# show TXT record queries
dns.qry.type == 16

# show PTR record queries
dns.qry.type == 12

# show ANY queries often suspicious
dns.qry.type == 255
```

### Filter by Response Code
```bash
# show successful responses
dns.flags.rcode == 0

# show NXDOMAIN responses domain not found
dns.flags.rcode == 3

# show all error responses
dns.flags.rcode != 0

# show server failure responses
dns.flags.rcode == 2
```

### Filter by IP in DNS Response
```bash
# show DNS responses containing specific IP
dns.a == 192.168.1.1

# show DNS responses for IP range
dns.a == 8.8.8.8
```

---

## DNS Query Analysis

### Normal DNS Query Pattern
- Client sends query to DNS server
- DNS server responds with answer
- Query and response have same Transaction ID
- Response time usually under 100ms
- Single query generates single response

### Analyzing DNS Queries
```bash
# step 1 show all DNS queries
dns.flags.response == 0

# step 2 check query names
# look for suspicious domains
# look for long random looking domains
# look for unusual TLDs

# step 3 check query frequency
# malware often queries same domain repeatedly
# use Statistics → DNS for overview
```

### DNS Statistics in Wireshark
```
Statistics → DNS
Shows all DNS queries and responses
Shows response times
Shows query type distribution
Shows server response codes
```

---

## DNS Tunneling Detection

### What is DNS Tunneling?
- Hiding data inside DNS queries and responses
- Used to bypass firewalls that allow DNS
- Used for command and control communication
- Used for data exfiltration
- Encodes data in subdomain labels of DNS queries

### How DNS Tunneling Works
- Attacker encodes data in DNS query name
- Example: data.exfil.attacker.com
- DNS server controlled by attacker decodes data
- Response contains encoded data back to victim
- Bypasses most firewalls since DNS is allowed

### DNS Tunneling Indicators
- Very long domain names
- High entropy random looking subdomains
- Unusually large number of DNS queries
- Large DNS response packets
- DNS queries with unusual record types
- Repeated queries to same domain
- Base64 or hex encoded subdomains

### DNS Tunneling Detection Filters
```bash
# show large DNS packets possible tunneling
dns and frame.len > 512

# show DNS over TCP large transfers
dns and tcp

# show unusually long domain names
dns.qry.name and frame.len > 100

# show high volume DNS from single host
dns and ip.src == suspicious_ip

# show TXT record queries often used for tunneling
dns.qry.type == 16

# show NULL record queries used for tunneling
dns.qry.type == 10
```

---

## DNS Spoofing Detection

### What is DNS Spoofing?
- Attacker sends fake DNS response to victim
- Victim resolves domain to wrong IP address
- Victim connects to attacker controlled server
- Used for phishing and man in the middle attacks

### DNS Spoofing Indicators
- Multiple DNS responses for same Transaction ID
- DNS response from unexpected source IP
- DNS response with different IP than expected
- Very fast DNS responses before real server responds

### DNS Spoofing Detection Filters
```bash
# show multiple responses for same transaction
dns.flags.response == 1

# compare source IPs of DNS responses
# legitimate response should come from configured DNS server
dns.flags.response == 1 and not ip.src == your_dns_server
```

---

## DNS Cache Poisoning Detection

### What is DNS Cache Poisoning?
- Attacker injects fake DNS records into DNS cache
- Affects all users using that DNS server
- Victims redirected to malicious servers
- Harder to detect than spoofing

### Detection Approach
- Monitor DNS server for unexpected record changes
- Check DNSSEC validation failures
- Look for NXDOMAIN suddenly resolving to IP
- Compare DNS responses over time

---

## Fast Flux Detection

### What is Fast Flux?
- Botnet technique to hide malicious infrastructure
- Domain resolves to many different IPs rapidly
- IPs change every few minutes
- Makes takedown very difficult
- Used by malware and phishing operations

### Fast Flux Indicators
- Same domain resolves to different IPs over time
- Very low TTL values in DNS responses
- Many A records in single DNS response
- IPs from many different countries

### Fast Flux Detection Filters
```bash
# show DNS responses with many answers
dns.count.answers > 5

# show DNS responses with very low TTL
# check TTL field in DNS answer section
dns.flags.response == 1
```

---

## DNS Exfiltration Detection

### What is DNS Exfiltration?
- Sending stolen data out through DNS queries
- Data encoded in subdomain names
- Example: c3RvbGVuZGF0YQ.attacker.com
- Difficult to detect without DNS analysis

### DNS Exfiltration Indicators
- Unusually long subdomain names
- Base64 or hex patterns in subdomains
- High volume of DNS queries
- DNS queries to unknown external domains
- Large number of unique subdomains for same domain

### DNS Exfiltration Detection Filters
```bash
# show long domain name queries
dns and frame.len > 150

# show queries to suspicious TLDs
dns.qry.name contains ".tk"
dns.qry.name contains ".pw"
dns.qry.name contains ".cc"

# show high volume DNS traffic
dns and ip.src == internal_host_ip
```

---

## DNS Malware Communication Detection

### Malware DNS Patterns
- Repeated queries to same domain
- DGA domains random looking names
- Queries to newly registered domains
- DNS queries at regular intervals beaconing
- Queries to IP addresses instead of domains

### DGA Domain Detection
- DGA stands for Domain Generation Algorithm
- Malware generates random domain names
- Attacker registers one of them for communication
- Domains look like random strings

### DGA Detection Filters
```bash
# show DNS failures NXDOMAIN many failures indicate DGA
dns.flags.rcode == 3

# show high volume of NXDOMAIN from single host
dns.flags.rcode == 3 and ip.src == suspicious_host
```

---

## Reverse DNS Analysis
```bash
# show PTR record queries reverse lookups
dns.qry.type == 12

# reverse lookup filter
dns.qry.name contains ".in-addr.arpa"
dns.qry.name contains ".ip6.arpa"
```

---

## DNS Analysis Workflow

### Step 1 - Get Overview
```
Statistics → DNS
Check query types and response codes
Note any unusual patterns
```

### Step 2 - Find All Queried Domains
```bash
dns.flags.response == 0
# scroll through and note all domain names
# look for suspicious or unknown domains
```

### Step 3 - Check for Failures
```bash
dns.flags.rcode == 3
# many NXDOMAIN may indicate DGA malware
```

### Step 4 - Check for Large Packets
```bash
dns and frame.len > 512
# large packets may indicate tunneling
```

### Step 5 - Check for Unusual Record Types
```bash
dns.qry.type == 16
dns.qry.type == 255
dns.qry.type == 10
# unusual types may indicate tunneling
```

### Step 6 - Check Query Frequency
```
Statistics → Conversations → UDP
# look for high volume DNS traffic to single server
```

---



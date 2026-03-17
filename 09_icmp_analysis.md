# ICMP Analysis

## What is ICMP?
- ICMP stands for Internet Control Message Protocol
- It is a network layer protocol used for diagnostics
- Used by network devices to send error messages
- Used by ping and traceroute commands
- Works at Layer 3 of OSI model
- Does not use ports like TCP and UDP
- Every network administrator and hacker uses ICMP
- Defined in RFC 792

---

## Why ICMP Analysis is Important?
- Detects ping sweeps and network scanning
- Finds ICMP flood attacks
- Detects ICMP tunneling for data exfiltration
- Identifies network unreachable errors
- Helps troubleshoot network connectivity issues
- Reveals traceroute activity
- Detects covert communication channels
- Finds ping of death attacks

---

## ICMP Message Format

### ICMP Header Fields
- Type → 8 bits → identifies message type
- Code → 8 bits → subtype of message
- Checksum → 16 bits → error detection
- Rest of Header → 32 bits → depends on type and code
- Data → variable → payload of ICMP message

---

## ICMP Message Types

### Type 0 - Echo Reply
- Response to ping request
- Sent by destination host
- Contains same data as echo request

### Type 3 - Destination Unreachable
- Sent when destination cannot be reached
- Has multiple codes explaining reason
- Code 0 → Network Unreachable
- Code 1 → Host Unreachable
- Code 2 → Protocol Unreachable
- Code 3 → Port Unreachable
- Code 4 → Fragmentation Needed
- Code 9 → Network Administratively Prohibited
- Code 10 → Host Administratively Prohibited
- Code 13 → Communication Administratively Prohibited

### Type 4 - Source Quench
- Tells sender to slow down
- Deprecated in modern networks
- Was used for flow control

### Type 5 - Redirect
- Tells host to use different router
- Can be abused for man in the middle attacks
- Code 0 → Redirect for Network
- Code 1 → Redirect for Host

### Type 8 - Echo Request
- Ping request sent to destination
- Contains sequence number and identifier
- Contains data payload
- Destination should reply with Type 0

### Type 9 - Router Advertisement
- Router announces its presence
- Used in router discovery protocol

### Type 10 - Router Solicitation
- Host requests router advertisement
- Used when host needs router information

### Type 11 - Time Exceeded
- Sent when TTL reaches zero
- Used by traceroute to map network path
- Code 0 → TTL Exceeded in Transit
- Code 1 → Fragment Reassembly Time Exceeded

### Type 12 - Parameter Problem
- Sent when IP header has problem
- Indicates malformed packet

### Type 13 - Timestamp Request
- Requests time synchronization
- Can be used for network reconnaissance

### Type 14 - Timestamp Reply
- Response to timestamp request

### Type 17 - Address Mask Request
- Requests subnet mask information
- Deprecated in modern networks

### Type 18 - Address Mask Reply
- Response to address mask request

---

## ICMP Display Filters in Wireshark

### Basic ICMP Filters
```bash
# show all ICMP traffic
icmp

# show all ICMPv6 traffic
icmpv6

# show ICMP from specific host
icmp and ip.src == 192.168.1.1

# show ICMP to specific host
icmp and ip.dst == 192.168.1.1
```

### Filter by ICMP Type
```bash
# show echo requests ping
icmp.type == 8

# show echo replies
icmp.type == 0

# show destination unreachable
icmp.type == 3

# show redirect messages
icmp.type == 5

# show time exceeded traceroute
icmp.type == 11

# show timestamp requests
icmp.type == 13

# show source quench
icmp.type == 4
```

### Filter by ICMP Code
```bash
# show network unreachable
icmp.type == 3 and icmp.code == 0

# show host unreachable
icmp.type == 3 and icmp.code == 1

# show port unreachable
icmp.type == 3 and icmp.code == 3

# show fragmentation needed
icmp.type == 3 and icmp.code == 4

# show TTL exceeded
icmp.type == 11 and icmp.code == 0
```

---

## Ping Analysis

### What is Ping?
- Ping sends ICMP Echo Request to destination
- Destination replies with ICMP Echo Reply
- Measures round trip time
- Checks if host is reachable
- Uses Type 8 for request and Type 0 for reply

### Normal Ping Pattern
- One Echo Request followed by one Echo Reply
- Response time should be consistent
- Sequence numbers increment by one
- Data payload same in request and reply

### Ping Filters
```bash
# show ping requests
icmp.type == 8

# show ping replies
icmp.type == 0

# show complete ping exchange
icmp.type == 8 or icmp.type == 0

# show ping between two hosts
icmp and ip.addr == 192.168.1.1 and ip.addr == 192.168.1.2
```

### Analyzing Ping in Wireshark
```
1. Apply filter: icmp.type == 8 or icmp.type == 0
2. Check sequence numbers increment properly
3. Check response times in Info column
4. Check if every request has a reply
5. Check data payload for hidden data
```

---

## Ping Sweep Detection

### What is Ping Sweep?
- Attacker pings many hosts to find alive ones
- Sends ICMP Echo Request to range of IPs
- Hosts that reply are considered alive
- Used in reconnaissance phase
- Also called ICMP scan

### Ping Sweep Indicators
- Many ICMP Echo Requests from single source
- Requests to sequential IP addresses
- Short time between requests
- Many different destination IPs

### Ping Sweep Detection Filters
```bash
# show all ICMP requests from single source
icmp.type == 8 and ip.src == attacker_ip

# show ICMP requests to multiple destinations
icmp.type == 8

# check Statistics → Endpoints for ICMP sweep pattern
```

---

## ICMP Flood Attack Detection

### What is ICMP Flood?
- Attacker sends massive ICMP Echo Requests
- Overwhelms target with traffic
- Consumes bandwidth and processing power
- Type of Denial of Service attack
- Also called Ping Flood or Smurf Attack

### ICMP Flood Indicators
- Extremely high volume of ICMP packets
- Many requests from same source
- Target becomes unresponsive
- Network bandwidth saturated

### ICMP Flood Detection Filters
```bash
# show high volume ICMP from single source
icmp.type == 8 and ip.src == attacker_ip

# check packet rate in Statistics
Statistics → IO Graph
# spike in ICMP traffic visible

# show ICMP traffic statistics
Statistics → Protocol Hierarchy
# ICMP percentage very high during flood
```

---

## Traceroute Analysis

### What is Traceroute?
- Maps network path between source and destination
- Sends packets with incrementing TTL values
- Each router decrements TTL by one
- When TTL reaches zero router sends Time Exceeded
- Source records each router in path

### How Traceroute Works
- Send packet with TTL 1
- First router sends Time Exceeded
- Send packet with TTL 2
- Second router sends Time Exceeded
- Repeat until destination reached

### Traceroute Filters
```bash
# show time exceeded messages traceroute
icmp.type == 11

# show traceroute activity
icmp.type == 11 and icmp.code == 0

# show complete traceroute
icmp.type == 8 or icmp.type == 11 or icmp.type == 0
```

### Analyzing Traceroute in Wireshark
```
1. Apply filter: icmp.type == 11
2. Note source IPs of Time Exceeded messages
3. Each source IP is a router in path
4. Order by time to see path sequence
5. Map IP addresses to locations using ipinfo.io
```

---

## ICMP Tunneling Detection

### What is ICMP Tunneling?
- Hiding data inside ICMP packets
- Uses data field of Echo Request and Reply
- Bypasses firewalls that allow ICMP
- Used for covert communication channel
- Used for data exfiltration

### How ICMP Tunneling Works
- Normal ping data field contains random or fixed data
- ICMP tunnel encodes real data in payload
- Both sides run tunnel software
- Data passes through firewall hidden in ICMP

### ICMP Tunneling Indicators
- Unusually large ICMP payload size
- Non standard data in ICMP payload
- High volume of ICMP traffic
- ICMP traffic at regular intervals
- Encrypted or compressed looking payload data
- ICMP Echo Replies without matching requests

### ICMP Tunneling Detection Filters
```bash
# show large ICMP packets
icmp and frame.len > 100

# show very large ICMP packets
icmp and frame.len > 500

# show ICMP with unusual payload
icmp.type == 8 and data.len > 64

# show high volume ICMP from single host
icmp and ip.src == suspicious_host
```

### Analyzing ICMP Payload
```
1. Click on suspicious ICMP packet
2. Expand ICMP section in packet details
3. Look at Data field
4. Normal ping has fixed pattern like abcdefgh
5. Tunnel traffic has random or encrypted looking data
6. Right click data field → Follow ICMP Stream
```

---

## ICMP Redirect Attack Detection

### What is ICMP Redirect Attack?
- Attacker sends fake ICMP Redirect message
- Tells victim to use attacker as router
- All traffic routes through attacker
- Enables man in the middle attack

### ICMP Redirect Indicators
- ICMP Type 5 messages from unexpected sources
- Redirect pointing to unusual gateway
- Traffic suddenly routing through new host

### ICMP Redirect Detection Filters
```bash
# show all ICMP redirect messages
icmp.type == 5

# show redirect from specific source
icmp.type == 5 and ip.src == suspicious_ip
```

---

## Ping of Death Detection

### What is Ping of Death?
- Sending oversized ICMP packets to crash target
- ICMP packet larger than 65535 bytes
- Causes buffer overflow on target
- Older vulnerability mostly patched now

### Detection Filters
```bash
# show large ICMP packets
icmp and frame.len > 1500

# show fragmented ICMP
ip.frag_offset > 0 and icmp
```

---

## ICMPv6 Analysis

### What is ICMPv6?
- ICMP version for IPv6 networks
- More features than ICMPv4
- Used for neighbor discovery
- Used for router discovery
- Used for path MTU discovery

### Important ICMPv6 Types
- Type 1 → Destination Unreachable
- Type 2 → Packet Too Big
- Type 3 → Time Exceeded
- Type 4 → Parameter Problem
- Type 128 → Echo Request
- Type 129 → Echo Reply
- Type 133 → Router Solicitation
- Type 134 → Router Advertisement
- Type 135 → Neighbor Solicitation
- Type 136 → Neighbor Advertisement

### ICMPv6 Filters
```bash
# show all ICMPv6
icmpv6

# show ICMPv6 echo requests
icmpv6.type == 128

# show ICMPv6 echo replies
icmpv6.type == 129

# show neighbor solicitation
icmpv6.type == 135

# show neighbor advertisement
icmpv6.type == 136

# show router advertisement
icmpv6.type == 134
```

---

## ICMP Analysis Workflow

### Step 1 - Get Overview
```bash
# show all ICMP traffic
icmp
# check volume and sources
```

### Step 2 - Check for Ping Sweep
```bash
# show all ping requests
icmp.type == 8
# check if many different destination IPs from single source
```

### Step 3 - Check for Flood
```
Statistics → IO Graph
# look for ICMP traffic spikes
```

### Step 4 - Check for Tunneling
```bash
# show large ICMP packets
icmp and frame.len > 100
# check payload content
```

### Step 5 - Check for Traceroute
```bash
# show time exceeded
icmp.type == 11
# map routing path
```

### Step 6 - Check for Redirects
```bash
# show redirect messages
icmp.type == 5
# verify source is legitimate router
```

---




# Capture Filters

## What are Capture Filters?
- Capture filters are filters applied before capturing traffic
- They tell Wireshark which packets to capture and which to ignore
- Only matching packets are saved to capture file
- Reduces capture file size significantly
- Useful when you know exactly what traffic you want to capture
- Uses BPF syntax (Berkeley Packet Filter)
- Different from display filters which filter already captured packets

---

## Capture Filters vs Display Filters

### Capture Filters
- Applied before capture starts
- Only captures matching packets
- Uses BPF syntax
- Cannot be changed during capture
- Reduces file size
- Set in Capture Options

### Display Filters
- Applied after capture
- All packets are captured but only matching shown
- Uses Wireshark display filter syntax
- Can be changed anytime during analysis
- Does not reduce file size
- Set in filter toolbar

---

## How to Set Capture Filters

### Method 1 - Capture Options
```
Capture → Options
Select interface
Enter filter in Capture Filter box
Click Start
```

### Method 2 - Welcome Screen
```
Double click interface name
Enter filter before starting
```

### Method 3 - Toolbar
```
Click Capture Filter button in toolbar
Select or type filter
```

---

## Basic Capture Filter Syntax

### Filter by Host
```bash
# capture traffic to and from specific IP
host 192.168.1.1

# capture traffic from specific IP only
src host 192.168.1.1

# capture traffic to specific IP only
dst host 192.168.1.1
```

### Filter by Network
```bash
# capture entire subnet
net 192.168.1.0/24

# capture from specific network
src net 192.168.1.0/24

# capture to specific network
dst net 192.168.1.0/24
```

### Filter by Port
```bash
# capture specific port
port 80
port 443
port 53

# capture from specific port
src port 80

# capture to specific port
dst port 80

# capture port range
portrange 80-100
```

### Filter by Protocol
```bash
# capture only TCP traffic
tcp

# capture only UDP traffic
udp

# capture only ICMP traffic
icmp

# capture only ARP traffic
arp

# capture only DNS traffic
udp port 53

# capture only HTTP traffic
tcp port 80

# capture only HTTPS traffic
tcp port 443

# capture only FTP traffic
tcp port 21

# capture only SSH traffic
tcp port 22
```

---

## Combining Capture Filters

### AND operator
```bash
# capture TCP traffic from specific host
tcp and host 192.168.1.1

# capture HTTP traffic from specific host
tcp port 80 and host 192.168.1.1
```

### OR operator
```bash
# capture HTTP or HTTPS traffic
port 80 or port 443

# capture traffic from two hosts
host 192.168.1.1 or host 192.168.1.2
```

### NOT operator
```bash
# capture everything except ICMP
not icmp

# capture everything except specific host
not host 192.168.1.1

# capture everything except broadcast
not broadcast
```

### Complex Combinations
```bash
# capture TCP traffic excluding specific host
tcp and not host 192.168.1.1

# capture HTTP or HTTPS excluding local network
(port 80 or port 443) and not net 192.168.1.0/24

# capture traffic between two specific hosts
host 192.168.1.1 and host 192.168.1.2
```

---

## Common Capture Filter Examples

### Network Troubleshooting
```bash
# capture all traffic on subnet
net 192.168.1.0/24

# capture traffic to gateway
host 192.168.1.1

# capture DNS traffic
udp port 53

# capture DHCP traffic
udp port 67 or udp port 68
```

### Security Monitoring
```bash
# capture suspicious port traffic
tcp port 4444
tcp port 1337
tcp port 8080

# capture all TCP SYN packets
tcp[tcpflags] & tcp-syn != 0

# capture ICMP only
icmp

# capture non standard ports
not port 80 and not port 443 and tcp
```

### Web Traffic Analysis
```bash
# capture HTTP only
tcp port 80

# capture HTTPS only
tcp port 443

# capture both HTTP and HTTPS
tcp port 80 or tcp port 443

# capture web traffic from specific host
host 192.168.1.5 and (port 80 or port 443)
```

### Email Traffic
```bash
# capture SMTP traffic
tcp port 25

# capture POP3 traffic
tcp port 110

# capture IMAP traffic
tcp port 143

# capture all email protocols
tcp port 25 or tcp port 110 or tcp port 143
```

---

## Capture Filter by Packet Size
```bash
# capture packets larger than 1000 bytes
greater 1000

# capture packets smaller than 100 bytes
less 100

# capture packets of exact size
len == 64
```

---

## Capture Filter by MAC Address
```bash
# capture traffic from specific MAC
ether host 00:11:22:33:44:55

# capture traffic to specific MAC
ether dst 00:11:22:33:44:55

# capture traffic from specific MAC
ether src 00:11:22:33:44:55
```

---

## Capture Filter for Wireless
```bash
# capture beacon frames
type mgt subtype beacon

# capture probe requests
type mgt subtype probe-req

# capture data frames only
type data
```

---

## Saving Capture Filters
```
Capture → Options
Enter filter
Click Save button next to filter box
Give filter a name
Filter saved for future use
```

---

## Common Mistakes with Capture Filters
- Using display filter syntax in capture filter box
- Capture filters use different syntax than display filters
- Example: http is not valid capture filter
- Use tcp port 80 instead of http
- Example: ip.addr is not valid capture filter
- Use host instead of ip.addr

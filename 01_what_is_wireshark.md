# What is Wireshark

## Definition
- Wireshark is a free and open source network packet analyzer
- It is used to capture and analyze network traffic in real time
- It is the most widely used network analysis tool in the world
- Originally called Ethereal renamed to Wireshark in 2006
- Developed by Gerald Combs in 1998
- Available on Windows, Linux, and macOS

---

## What is Packet Analysis?
- Every data sent over network is broken into small pieces called packets
- Each packet contains source, destination, and data information
- Packet analysis means capturing and reading these packets
- Wireshark captures these packets and displays them in readable format
- Also called packet sniffing or network sniffing

---

## Why Wireshark is Important?
- Used by network engineers to troubleshoot network problems
- Used by cybersecurity professionals to detect attacks
- Used by ethical hackers for network reconnaissance
- Used by students to learn how network protocols work
- Used in incident response to investigate security breaches
- Used to find unencrypted credentials on network

---

## What Wireshark Can Do?
- Capture live network traffic
- Open and analyze saved pcap files
- Filter packets by protocol, IP, port
- Follow TCP and HTTP streams
- Decode encrypted and unencrypted protocols
- Export captured data for further analysis
- Colorize packets by protocol type
- Generate network statistics and graphs
- Detect suspicious network activity

---

## What Wireshark Cannot Do?
- Cannot decrypt traffic without encryption keys
- Cannot capture traffic on switched networks without port mirroring
- Cannot send packets (only captures)
- Cannot bypass network encryption like HTTPS automatically
- Cannot capture traffic on other networks without permission

---
## Wireshark File Formats
- PCAP → most common packet capture format
- PCAPNG → newer version of pcap with more features
- Both can be opened and analyzed in Wireshark

---

## How Wireshark Works
- Wireshark puts network interface into promiscuous mode
- Promiscuous mode allows capturing all packets on network
- Not just packets meant for your device
- Packets are captured and stored in memory
- Displayed in real time in Wireshark interface
- Can be saved as pcap file for later analysis

---

## Wireshark vs Other Tools
- Wireshark → GUI based full packet analysis
- tshark → command line version of Wireshark
- tcpdump → lightweight command line packet capture
- NetworkMiner → passive network forensic analyzer
- Zeek → network security monitoring framework

---

## Important Points
- Always use Wireshark on networks you own or have permission to monitor
- Capturing traffic on unauthorized networks is illegal
- Wireshark is preinstalled on Kali Linux
- Run Wireshark with sudo for full capture capabilities
- pcap files can be shared and analyzed offline
```

# TCP Analysis

## What is TCP?
- TCP stands for Transmission Control Protocol
- It is a connection oriented protocol
- Provides reliable ordered and error checked delivery of data
- Used by most application layer protocols
- Works at Layer 4 of OSI model
- Every TCP connection has a beginning and an end
- Most common protocol seen in network captures

---

## TCP Header Structure
- Source Port → 16 bits → port number of sender
- Destination Port → 16 bits → port number of receiver
- Sequence Number → 32 bits → tracks order of data
- Acknowledgment Number → 32 bits → confirms received data
- Data Offset → 4 bits → size of TCP header
- Flags → 9 bits → control bits for connection management
- Window Size → 16 bits → flow control buffer size
- Checksum → 16 bits → error detection
- Urgent Pointer → 16 bits → points to urgent data
- Options → variable → optional settings

---

## TCP Flags Explained

### SYN (Synchronize)
- Used to initiate a connection
- First packet in three way handshake
- Contains initial sequence number
- Only set in first two packets of handshake

### ACK (Acknowledge)
- Confirms receipt of data
- Set in almost all packets after connection established
- Acknowledgment number shows next expected byte

### FIN (Finish)
- Used to terminate a connection gracefully
- Sender has no more data to send
- Connection closes after FIN ACK exchange

### RST (Reset)
- Abruptly terminates a connection
- Used when connection is invalid or error occurs
- Common in port scanning responses
- Indicates connection refused

### PSH (Push)
- Tells receiver to pass data to application immediately
- Does not wait for buffer to fill
- Used for interactive applications

### URG (Urgent)
- Marks data as urgent
- Receiver should process immediately
- Rarely used in modern networks

### ECE (ECN Echo)
- Used for congestion notification
- Part of Explicit Congestion Notification

### CWR (Congestion Window Reduced)
- Confirms congestion notification received
- Part of Explicit Congestion Notification

---

## TCP Three Way Handshake

### Step by Step
```
Client                    Server
  |                         |
  |-------- SYN ----------->|  Step 1: Client sends SYN
  |                         |          seq=1000
  |<------ SYN ACK ---------|  Step 2: Server sends SYN ACK
  |                         |          seq=2000 ack=1001
  |-------- ACK ----------->|  Step 3: Client sends ACK
  |                         |          ack=2001
  |                         |
  | Connection Established  |
```

### Wireshark Filter for Handshake
```bash
# show SYN packets
tcp.flags.syn == 1 and tcp.flags.ack == 0

# show SYN ACK packets
tcp.flags.syn == 1 and tcp.flags.ack == 1

# show complete handshake
tcp.flags.syn == 1
```

---

## TCP Four Way Termination

### Step by Step
```
Client                    Server
  |                         |
  |-------- FIN ----------->|  Step 1: Client sends FIN
  |<-------- ACK -----------|  Step 2: Server sends ACK
  |<-------- FIN -----------|  Step 3: Server sends FIN
  |--------- ACK ---------->|  Step 4: Client sends ACK
  |                         |
  |  Connection Terminated  |
```

### Wireshark Filter for Termination
```bash
# show FIN packets
tcp.flags.fin == 1

# show RST packets
tcp.flags.reset == 1

# show FIN or RST
tcp.flags.fin == 1 or tcp.flags.reset == 1
```

---

## TCP Sequence and Acknowledgment Numbers

### Sequence Number
- Tracks position of data in stream
- Every byte of data has a sequence number
- Helps receiver reassemble data in correct order
- Initial sequence number is random

### Acknowledgment Number
- Tells sender which byte is expected next
- Confirms all previous bytes received
- Example: ACK 1001 means received up to byte 1000

### Relative Sequence Numbers
- Wireshark shows relative sequence numbers by default
- Starts from 0 instead of actual random number
- Makes analysis easier
- Can be changed in preferences

---

## TCP Window Size and Flow Control

### What is Window Size?
- Controls how much data sender can send before waiting for ACK
- Prevents receiver from being overwhelmed
- Larger window = faster transfer
- Smaller window = slower transfer

### Window Scaling
- TCP window size field is 16 bits maximum 65535 bytes
- Window scaling option multiplies window size
- Allows much larger windows for high speed networks

### Wireshark Filters for Window
```bash
# show zero window packets
tcp.analysis.zero_window

# show window update packets
tcp.analysis.window_update

# show window full packets
tcp.analysis.window_full
```

---

## TCP Retransmissions and Errors

### Types of TCP Issues

#### Retransmission
- Packet was lost and sender retransmits it
- Causes performance degradation
- Indicates network problems

#### Duplicate ACK
- Receiver sends same ACK multiple times
- Indicates packet loss or reordering
- Triggers fast retransmit

#### Out of Order
- Packets arrive in wrong order
- Normal on some networks
- TCP reorders before passing to application

#### Zero Window
- Receiver buffer is full
- Tells sender to stop sending
- Indicates slow receiver or application

#### Fast Retransmit
- Triggered after three duplicate ACKs
- Retransmits without waiting for timeout
- Faster recovery from packet loss

### Wireshark Filters for TCP Issues
```bash
# show retransmissions
tcp.analysis.retransmission

# show fast retransmissions
tcp.analysis.fast_retransmission

# show duplicate ACKs
tcp.analysis.duplicate_ack

# show out of order packets
tcp.analysis.out_of_order

# show zero window
tcp.analysis.zero_window

# show all TCP problems
tcp.analysis.flags
```

---

## TCP Stream Analysis

### What is TCP Stream?
- Complete conversation between two endpoints
- Contains all packets in both directions
- Wireshark can reconstruct entire stream
- Shows actual data exchanged

### How to Follow TCP Stream
```
Right click any TCP packet
Follow → TCP Stream
Stream window opens
Shows complete conversation
```

### TCP Stream Colors
- Red text → client to server data
- Blue text → server to client data
- Can change display format:
  - ASCII → readable text
  - Hex dump → raw bytes
  - C arrays → code format
  - Raw → binary data

---

## TCP Port Analysis

### Well Known Ports
- 20 21 → FTP
- 22 → SSH
- 23 → Telnet
- 25 → SMTP
- 53 → DNS
- 80 → HTTP
- 110 → POP3
- 143 → IMAP
- 443 → HTTPS
- 445 → SMB
- 3306 → MySQL
- 3389 → RDP
- 8080 → HTTP Alternate

### Wireshark Filters for Ports
```bash
# filter specific port
tcp.port == 80
tcp.port == 443
tcp.port == 22

# filter source port
tcp.srcport == 80

# filter destination port
tcp.dstport == 80

# filter high ports
tcp.port > 1024

# filter well known ports
tcp.port < 1024
```

---

## TCP Security Analysis

### Port Scan Detection
- Attacker sends SYN to many ports
- Closed ports respond with RST
- Open ports respond with SYN ACK

#### Types of Port Scans
- SYN Scan → SYN then RST after SYN ACK
- Connect Scan → full three way handshake
- FIN Scan → FIN to closed ports
- NULL Scan → no flags set
- XMAS Scan → FIN URG PSH flags set

#### Port Scan Filters
```bash
# detect SYN scan
tcp.flags.syn == 1 and tcp.flags.ack == 0

# detect many RST packets
tcp.flags.reset == 1

# detect FIN scan
tcp.flags.fin == 1 and tcp.flags.ack == 0

# detect XMAS scan
tcp.flags.fin == 1 and tcp.flags.urg == 1 and tcp.flags.push == 1
```

### SYN Flood Attack Detection
- Attacker sends massive SYN packets
- Server allocates resources for each
- Server runs out of resources
- Legitimate connections denied

#### SYN Flood Filters
```bash
# show all SYN packets
tcp.flags.syn == 1 and tcp.flags.ack == 0

# check if many SYNs from same IP
tcp.flags.syn == 1 and ip.src == attacker_ip
```

### TCP Session Hijacking
- Attacker takes over established TCP session
- Predicts sequence numbers
- Injects malicious data into stream
- Detection: unexpected RST or sequence number jumps

---

## TCP Conversations Analysis

### How to View TCP Conversations
```
Statistics → Conversations → TCP tab
Shows all TCP connections
Displays bytes transferred each direction
Shows duration of connection
```

### What to Look For
- Long duration connections may indicate persistent access
- Large data transfers may indicate exfiltration
- Many short connections may indicate scanning
- Connections to unusual ports may indicate malware

---

## TCP Timestamps Analysis
- TCP timestamps option tracks round trip time
- Helps detect retransmissions accurately
- Wireshark shows timestamps in packet details
- Useful for performance analysis

---



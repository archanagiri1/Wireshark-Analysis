# Wireshark Interface

## What is Wireshark Interface?
- Wireshark interface is the graphical user interface of Wireshark
- It is divided into multiple panels and sections
- Understanding the interface is first step to using Wireshark
- Each section provides different information about captured packets
- Interface is same on Windows and Linux with minor differences

---

## Main Sections of Wireshark Interface

### Title Bar
- Shows name of currently open capture file
- Shows interface name during live capture
- Shows Wireshark version

### Menu Bar
- File → open, save, export capture files
- Edit → find packets, mark packets, preferences
- View → zoom, colorize, show hide panels
- Go → go to specific packet number
- Capture → start, stop, options for capturing
- Analyze → display filters, follow streams
- Statistics → protocol hierarchy, conversations, endpoints
- Telephony → VoIP and phone call analysis
- Wireless → wireless network analysis
- Tools → additional tools
- Help → documentation and about

### Main Toolbar
- Quick access buttons for common actions
- Start capture button
- Stop capture button
- Restart capture button
- Open file button
- Save file button
- Find packet button
- Go to packet button
- Zoom in and zoom out buttons
- Colorize button

### Filter Toolbar
- Located below main toolbar
- Where you type display filters
- Green color means filter is valid
- Red color means filter is invalid
- Yellow color means filter has warning
- Bookmark icon saves favorite filters
- Arrow icon applies filter

---
## Three Main Panels

### Packet List Panel (Top Panel)
- Shows all captured packets in a list
- Each row is one packet
- Columns:
  - No → packet number in sequence
  - Time → time packet was captured
  - Source → source IP address
  - Destination → destination IP address
  - Protocol → protocol used
  - Length → size of packet in bytes
  - Info → summary of packet content
- Color coded by protocol type
- Click any packet to select it

### Packet Details Panel (Middle Panel)
- Shows detailed breakdown of selected packet
- Displays all protocol layers
- Expandable tree structure
- Layers shown from bottom to top:
  - Frame → physical layer details
  - Ethernet → data link layer
  - IP → network layer
  - TCP or UDP → transport layer
  - HTTP or DNS etc → application layer
- Click arrow to expand each layer
- Right click field to apply as filter

### Packet Bytes Panel (Bottom Panel)
- Shows raw bytes of selected packet
- Left side shows hexadecimal values
- Right side shows ASCII representation
- Highlighted bytes correspond to selected field
- Useful for deep packet inspection

---
## Status Bar
- Located at very bottom of Wireshark
- Left side shows current profile
- Middle shows expert information alerts
- Right side shows packet count
- Shows capture file information

---

## Welcome Screen
- Shown when no capture file is open
- Shows list of available network interfaces
- Shows recent capture files
- Shows interface traffic activity graph
- Double click interface to start capturing

---
## Color Coding in Wireshark

### Default Colors
- Light purple → TCP traffic
- Light blue → UDP traffic
- Black → TCP packets with errors
- Light green → HTTP traffic
- Light yellow → Windows specific traffic
- Dark yellow → Routing protocols
- Dark gray → TCP SYN and FIN packets
- Red → Errors and problems

### How to View Color Rules
```
View → Coloring Rules
```

### How to Change Colors
```
View → Coloring Rules → Edit or Add rules
```

---

## Wireshark Panels Customization

### Show or Hide Panels
```
View → Packet List → show or hide
View → Packet Details → show or hide
View → Packet Bytes → show or hide
```

### Resize Panels
- Drag border between panels to resize
- Double click border to auto resize

### Add or Remove Columns
```
Right click column header
Add or Remove columns
```

## Follow Stream Feature
- Reconstructs entire conversation between two hosts
- Very useful for reading HTTP, FTP, Telnet data
- Shows data in human readable format

### How to Use
```
Right click any packet
Follow → TCP Stream
Follow → UDP Stream
Follow → HTTP Stream
```

---

## Export Features
- Export specific packets to new file
- Export packet dissections as text or CSV
- Export objects like files transferred over HTTP

### Export Objects
```
File → Export Objects → HTTP
File → Export Objects → FTP
```

---

## Wireshark Preferences
- Customize Wireshark behavior
- Change name resolution settings
- Change protocol settings
- Change appearance settings

### How to Open
```
Edit → Preferences
```
## Important Points
- Packet list panel is where you start analysis
- Packet details panel gives full protocol breakdown
- Packet bytes panel shows raw data
- Color coding helps quickly identify traffic types
- Follow stream is very powerful for reading conversations
- Always save capture file before closing Wireshark
```

---

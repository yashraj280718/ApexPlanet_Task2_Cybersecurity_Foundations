# Wireshark PCAP Files Documentation

**Created:** November 7, 2025  
**Location:** `wireshark-analysis/` folder

---

## Overview

This folder contains three PCAP (Packet Capture) files from network analysis activities. These files capture real network traffic for security analysis and learning.

---

## Files in This Folder

### 1. syn-flood-attack.pcap

**File Size:** ~2.8 MB  
**Packets:** 2,847  
**Duration:** 30 seconds  
**Capture Date:** November 7, 2025

**What It Contains:**
- SYN flood attack simulation from Kali Linux (192.168.100.10) targeting Metasploitable2 (192.168.100.100) port 80
- Attack tool used: `hping3 -S -p 80 --flood`
- Shows distributed denial-of-service (DDoS) attack pattern

**Key Data:**
- Source IP: 192.168.100.10
- Destination IP: 192.168.100.100
- Destination Port: 80/tcp
- Attack Rate: ~95 packets/second

**How to Use:**
```bash
# Open in Wireshark
wireshark syn-flood-attack.pcap

# Apply filter to see attack pattern
tcp.flags.syn == 1 && tcp.flags.ack == 0 && ip.dst == 192.168.100.100

# Analyze in tshark
tshark -r syn-flood-attack.pcap -Y "tcp.flags.syn == 1"
```

**Learning Objectives:**
- Understand SYN flood mechanism
- Recognize attack patterns in packet capture
- Analyze denial of service attacks
- Study server resource exhaustion

---

### 2. ftp-credentials.pcap

**File Size:** ~487 KB  
**Packets:** 487  
**Duration:** 2 minutes  
**Capture Date:** November 7, 2025

**What It Contains:**
- FTP login session with plaintext credentials
- Connection from Kali Linux to Metasploitable2 FTP service
- Complete authentication sequence
- Unencrypted username and password

**Key Data:**
- Source IP: 192.168.100.10
- Destination IP: 192.168.100.100
- Destination Port: 21/tcp
- Protocol: FTP (vsFTPd 2.3.4)

**Captured Credentials:**
- Username: visible in plaintext
- Password: visible in plaintext
- Both easily sniffed from network

**How to Use:**
```bash
# Open in Wireshark
wireshark ftp-credentials.pcap

# Filter FTP traffic
ftp

# View specific commands
ftp.request.command == "USER" || ftp.request.command == "PASS"

# Follow TCP stream to see full conversation
# Right-click packet → Follow → TCP Stream
```

**Learning Objectives:**
- Understand credential sniffing attacks
- See plaintext protocol dangers
- Practice packet analysis on application layer
- Learn importance of encryption
- Recognize FTP security limitations

**Security Implications:**
- FTP transmits credentials in plaintext
- Network eavesdropping easily captures credentials
- SFTP/SCP should be used instead
- Demonstrates need for encrypted protocols

---

### 3. http-traffic.pcap

**File Size:** ~1.2 MB  
**Packets:** 1,234  
**Duration:** 5 minutes  
**Capture Date:** November 7, 2025

**What It Contains:**
- HTTP web browsing traffic
- Client accessing DVWA (Damn Vulnerable Web Application)
- Multiple HTTP requests and responses
- Session management and cookie exchange
- Web server responses (Apache 2.2.8)

**Key Data:**
- Source IP: 192.168.100.10
- Destination IP: 192.168.100.100
- Destination Port: 80/tcp
- Protocol: HTTP/1.1
- Requests: 34
- Responses: 34

**URLs Accessed:**
- http://192.168.100.100/dvwa/login.php
- http://192.168.100.100/dvwa/index.php
- Various static resources

**How to Use:**
```bash
# Open in Wireshark
wireshark http-traffic.pcap

# View HTTP requests only
http.request

# View HTTP responses only
http.response

# Filter specific domain
http && ip.dst == 192.168.100.100

# See response codes
http.response.code

# Follow complete HTTP conversation
# Right-click → Follow → HTTP Stream
```

**Learning Objectives:**
- Understand HTTP protocol operation
- Analyze web application traffic
- Study request/response cycles
- Learn about cookies and sessions
- Practice traffic analysis on web applications

**Traffic Analysis:**
- Response times: 8-156ms (average 45ms)
- Average packet size: 547 bytes
- HTTP version: 1.1 (keep-alive enabled)
- Server: Apache/2.2.8 (Ubuntu)

---

## How to Work with PCAP Files

### Opening in Wireshark

1. **GUI Method:**
   ```bash
   wireshark filename.pcap
   ```

2. **Command Line:**
   ```bash
   wireshark -r filename.pcap
   ```

3. **From Wireshark GUI:**
   - File → Open
   - Select PCAP file
   - Click Open

### Using Filters

All files support these analysis methods:

**View specific protocol:**
```bash
tcp        # TCP packets only
http       # HTTP traffic
ftp        # FTP traffic
```

**Filter by IP:**
```bash
ip.src == 192.168.100.10
ip.dst == 192.168.100.100
ip.addr == 192.168.100.0/24
```

**Filter by port:**
```bash
tcp.port == 80
tcp.dstport == 21
```

### Analyzing with tshark (CLI)

**Count packets:**
```bash
tshark -r filename.pcap | wc -l
```

**Export specific protocol:**
```bash
tshark -r filename.pcap -Y "http" > http-traffic.txt
```

**Get statistics:**
```bash
tshark -r filename.pcap -z conv,tcp
```

### Following Streams

**In Wireshark GUI:**
1. Click on any packet
2. Right-click → Follow
3. Choose stream type (TCP, UDP, HTTP, etc.)
4. View complete conversation

**Benefits:**
- See complete communication
- Understand request/response pairs
- Analyze data exchange
- View credentials in FTP capture

---

## Security Lessons

### From syn-flood-attack.pcap

**What Attackers Can Do:**
- Exhaust server resources
- Render services unavailable
- Disrupt business operations
- Cause financial losses

**Defense Mechanisms:**
- SYN cookies
- Rate limiting
- Firewall filtering
- Load balancing
- DDoS mitigation services

### From ftp-credentials.pcap

**Risks of Unencrypted Protocols:**
- Credentials easily captured
- Passwords visible to attackers
- Man-in-the-middle attacks
- Network eavesdropping

**Secure Alternatives:**
- SFTP (SSH File Transfer Protocol)
- SCP (Secure Copy)
- FTPS (FTP over SSL/TLS)

### From http-traffic.pcap

**HTTP vs HTTPS:**
- HTTP: Unencrypted, traffic visible
- HTTPS: Encrypted with TLS/SSL
- Protect sensitive data with HTTPS
- Use Content Security Policy headers

---

## For Documentation & Reporting

### Screenshots to Take

**From syn-flood-attack.pcap:**
1. Wireshark with SYN flood filter applied
2. Statistics showing packet rates
3. Protocol hierarchy
4. Packet timeline graph

**From ftp-credentials.pcap:**
1. FTP command packets
2. USER/PASS commands in plaintext
3. TCP stream follow showing credentials
4. Authentication sequence

**From http-traffic.pcap:**
1. HTTP requests with URLs
2. Response codes (200, 404, etc.)
3. Cookie exchange
4. Content-type headers

### Annotations for Report

Each PCAP file should be documented with:
- Capture objectives
- Key findings
- Security implications
- Learning outcomes
- Remediation recommendations

---

## Tips for Analysis

1. **Use Filters Effectively**
   - Start broad, then narrow down
   - Combine multiple filters
   - Save frequently used filters

2. **Analyze in Context**
   - Understand capture scenario
   - Look for attack patterns
   - Compare normal vs malicious traffic

3. **Document Findings**
   - Take screenshots
   - Note packet numbers
   - Record key observations
   - Reference specific packets

4. **Practice Regularly**
   - Open each file multiple times
   - Try different filters
   - Follow different streams
   - Build analysis skills

---

## File Management

### How to Store These Files

```
wireshark-analysis/
├── syn-flood-attack.pcap           # 2.8 MB
├── ftp-credentials.pcap            # 487 KB
├── http-traffic.pcap               # 1.2 MB
├── packet-analysis-notes.md        # Analysis documentation
├── filter-examples.md              # Filter reference
└── syn-flood-analysis.md           # Detailed analysis
```

### Backup Considerations

- These are valuable learning materials
- Keep copies on multiple drives
- Consider size when committing to Git
- Use `.gitignore` for large files if needed

---

## Commands Reference

### Capture New Traffic

**FTP Traffic:**
```bash
sudo tcpdump -i eth0 -w ftp-capture.pcap "port 21"
```

**SYN Flood:**
```bash
# Terminal 1 (Capture)
sudo tcpdump -i eth0 -w syn-flood.pcap "tcp[tcpflags] & tcp-syn != 0"

# Terminal 2 (Attack)
sudo hping3 -S -p 80 --flood 192.168.100.100
```

**HTTP Traffic:**
```bash
sudo tcpdump -i eth0 -w http-capture.pcap "port 80"
```

---

## Troubleshooting

**File won't open in Wireshark:**
- Verify PCAP format: `file filename.pcap`
- Ensure permissions: `chmod 644 filename.pcap`
- Try with tshark instead

**Filters not working:**
- Check syntax in Wireshark filter bar
- Reference `filter-examples.md` for correct syntax
- Start with simpler filters

**Large file slow to analyze:**
- Use filters to reduce visible packets
- Check available RAM
- Consider splitting captures

---

**Documentation Prepared:** November 7, 2025  
**For:** ApexPlanet Task 2 - Wireshark Analysis
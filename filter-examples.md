# Wireshark Display Filter Examples & Reference

**Document Date:** November 7, 2025  
**Purpose:** Quick reference for Wireshark filters used in Task 2 analysis

---

## Table of Contents

1. [Basic Protocol Filters](#basic-protocol-filters)
2. [IP Address Filters](#ip-address-filters)
3. [Port Filters](#port-filters)
4. [Application Layer Filters](#application-layer-filters)
5. [Attack Detection Filters](#attack-detection-filters)
6. [Useful Combinations](#useful-combinations)
7. [Filter Logic](#filter-logic)

---

## Basic Protocol Filters

### Individual Protocols

| Filter | Description | Usage |
|--------|-------------|-------|
| `http` | HTTP traffic only | Browse web traffic |
| `https` | HTTPS traffic only | Encrypted web traffic |
| `ftp` | FTP protocol | File transfer analysis |
| `dns` | DNS queries and responses | Domain resolution |
| `tcp` | TCP packets | Connection-oriented traffic |
| `udp` | UDP packets | Connectionless traffic |
| `icmp` | ICMP (ping) traffic | Network diagnostics |
| `arp` | ARP packets | Address resolution |
| `ssh` | SSH protocol | Secure shell |
| `smtp` | Email protocol | Mail traffic |
| `pop` | POP3 email retrieval | Email downloads |
| `imap` | IMAP email protocol | Email access |

### Examples

**View only HTTP traffic:**
```
http
```

**View only FTP traffic:**
```
ftp
```

**View all DNS traffic:**
```
dns
```

---

## IP Address Filters

### Source and Destination Filtering

| Filter | Description |
|--------|-------------|
| `ip.addr == 192.168.100.100` | Traffic to/from specific IP |
| `ip.src == 192.168.100.10` | Traffic from specific source |
| `ip.dst == 192.168.100.100` | Traffic to specific destination |
| `ip.addr == 192.168.100.10 && ip.addr == 192.168.100.100` | Between two IPs |
| `ip.addr != 192.168.100.10` | Exclude specific IP |
| `ip.addr == 192.168.100.0/24` | Entire subnet |

### Examples

**Capture FTP traffic from Kali to Metasploitable:**
```
ftp && ip.src == 192.168.100.10 && ip.dst == 192.168.100.100
```

**View all traffic except from Kali:**
```
ip.src != 192.168.100.10
```

---

## Port Filters

### TCP and UDP Ports

| Filter | Description |
|--------|-------------|
| `tcp.port == 80` | TCP port 80 (HTTP) |
| `tcp.srcport == 21` | Source port 21 (FTP) |
| `tcp.dstport == 443` | Destination port 443 (HTTPS) |
| `tcp.port >= 1024` | Ports 1024 and above |
| `tcp.port <= 1024` | System ports only |
| `udp.port == 53` | UDP port 53 (DNS) |
| `port == 22` | Either TCP or UDP port 22 |

### Examples

**View only HTTP port 80:**
```
tcp.port == 80
```

**View SSH traffic (port 22):**
```
tcp.port == 22
```

**View DNS queries (port 53):**
```
udp.port == 53
```

---

## Application Layer Filters

### HTTP Specific

| Filter | Description |
|--------|-------------|
| `http.request` | HTTP requests only |
| `http.response` | HTTP responses only |
| `http.request.method == "GET"` | GET requests |
| `http.request.method == "POST"` | POST requests |
| `http.response.code == 200` | HTTP 200 OK |
| `http.response.code == 404` | HTTP 404 Not Found |
| `http.host == "example.com"` | Requests to domain |
| `http.user_agent contains "Mozilla"` | Specific user agents |

### FTP Specific

| Filter | Description |
|--------|-------------|
| `ftp.request.command == "USER"` | FTP USER commands |
| `ftp.request.command == "PASS"` | FTP PASS commands |
| `ftp.response.code == 230` | FTP authentication success |
| `ftp.response.code == 530` | FTP authentication failed |

### DNS Specific

| Filter | Description |
|--------|-------------|
| `dns.qry.name == "google.com"` | DNS queries for domain |
| `dns.flags.response == 0` | DNS queries only |
| `dns.flags.response == 1` | DNS responses only |
| `dns.resp.type == "A"` | A record responses |

### Examples

**View only HTTP GET requests:**
```
http.request.method == "GET"
```

**View HTTP POST (form submission):**
```
http.request.method == "POST"
```

**View failed HTTP responses:**
```
http.response.code == 404 || http.response.code == 500
```

---

## Attack Detection Filters

### SYN Flood Detection

| Filter | Description |
|--------|-------------|
| `tcp.flags.syn == 1` | All SYN packets |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | SYN without ACK |
| `tcp.flags.syn == 1 && ip.dst == 192.168.100.100` | SYN to target |

**SYN Flood Filter for Attack Analysis:**
```
tcp.flags.syn == 1 && tcp.flags.ack == 0 && ip.dst == 192.168.100.100
```

### Port Scanning Detection

| Filter | Description |
|--------|-------------|
| `tcp.flags.syn == 1` | Connection attempts |
| `tcp.flags.reset == 1` | Reset packets (port closed) |
| `icmp.type == 3` | ICMP unreachable |

**Port Scan Filter:**
```
tcp.flags.syn == 1 && (tcp.flags.ack == 0 || tcp.flags.reset == 1)
```

### Malformed Packets

| Filter | Description |
|--------|-------------|
| `tcp.checksum.status == "Bad"` | TCP checksum errors |
| `ip.checksum.status == "Bad"` | IP checksum errors |
| `frame.protocols contains "malformed"` | Malformed frames |

---

## Useful Combinations

### Scenario 1: Analyze FTP Credential Theft

**Goal:** View FTP login credentials in plaintext

```
ftp && (ftp.request.command == "USER" || ftp.request.command == "PASS")
```

**Step-by-step:**
1. Apply filter above
2. Right-click on packet
3. Follow → TCP Stream
4. View credentials in plaintext

### Scenario 2: Detect SYN Flood Attack

**Goal:** Identify SYN flood patterns

```
tcp.flags.syn == 1 && tcp.flags.ack == 0 && ip.dst == 192.168.100.100
```

**Analysis:**
- High packet rate from single source
- Destination port 80/443
- No corresponding ACK responses
- Connection queue filling

### Scenario 3: Monitor HTTPS Connections

**Goal:** View encrypted connection handshakes

```
ssl.handshake.type == 1
```

**Observations:**
- Client Hello packets
- Server Hello responses
- Certificate exchange
- Handshake completion

### Scenario 4: Find Unencrypted Credentials

**Goal:** Search for username/password in HTTP

```
http && (tcp.payload contains "password" || tcp.payload contains "login")
```

### Scenario 5: Analyze Web Traffic

**Goal:** View complete HTTP sessions

```
http.request || http.response
```

**Refine to specific domain:**
```
http && ip.dst == 192.168.100.100
```

---

## Filter Logic

### AND Operator (&&)

**Meaning:** Both conditions must be true

```
http && tcp.port == 80
```
Shows HTTP traffic on port 80 only

### OR Operator (||)

**Meaning:** Either condition can be true

```
tcp.port == 80 || tcp.port == 443
```
Shows traffic on port 80 OR 443

### NOT Operator (!)

**Meaning:** Exclude condition

```
!arp
```
Show everything except ARP traffic

### Parentheses for Grouping

```
(tcp.port == 80 || tcp.port == 443) && ip.addr == 192.168.100.100
```
Show port 80/443 traffic from specific IP

### Contains Operator

**Search for string in packet:**
```
tcp.payload contains "admin"
```

### Comparison Operators

```
tcp.window_size > 65535   # Greater than
tcp.len < 100              # Less than
ip.ttl >= 64               # Greater than or equal
frame.number <= 1000       # Less than or equal
```

---

## Quick Reference Commands

### For Task 2 Lab Work

**1. Monitor FTP login attempts:**
```
ftp.request.command == "USER" || ftp.request.command == "PASS"
```

**2. Detect port scans (Nmap):**
```
tcp.flags == 0x002 && tcp.flags.ack == 0
```

**3. View DNS queries:**
```
dns.qry.name != ""
```

**4. Monitor DVWA access:**
```
http && ip.dst == 192.168.100.100 && http contains "dvwa"
```

**5. Capture all connections:**
```
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

**6. Filter by conversation:**
```
ip.addr == 192.168.100.10 && ip.addr == 192.168.100.100
```

**7. Exclude broadcast/multicast:**
```
!broadcast && !multicast
```

**8. Recently captured packets:**
```
frame.number > 1000
```

---

## Tips for Effective Filtering

1. **Start Simple, Then Refine**
   - Begin with basic protocol filter
   - Add IP/port specifics
   - Narrow to exact criteria

2. **Use Color Rules**
   - Assign colors to filtered results
   - Easier visual identification
   - Set View → Coloring Rules

3. **Save Filters**
   - Frequently used filters → Save as preset
   - Access via filter toolbar dropdown
   - Create team standards

4. **Follow TCP Stream**
   - Right-click packet
   - Select Follow → TCP Stream
   - View complete conversation
   - Great for analyzing HTTP/FTP

5. **Export Filtered Results**
   - Apply filter
   - File → Export Specified Packets
   - Choose filtered packets only
   - Save for reporting

---

## Common Mistakes to Avoid

| Mistake | Wrong | Correct |
|---------|-------|---------|
| Missing operator | `http tcp.port == 80` | `http && tcp.port == 80` |
| Wrong IP syntax | `192.168.1.1` | `ip.addr == 192.168.1.1` |
| String without quotes* | `http.host contains example.com` | `http.host contains "example.com"` |
| Logic error | `tcp.port == 80 && 443` | `tcp.port == 80 \|\| tcp.port == 443` |
| Case sensitivity | `HTTP` | `http` (lowercase) |

---

**Document Prepared By:** [Your Name]  
**Last Updated:** November 7, 2025  
**For:** ApexPlanet Task 2 - Network Scanning & Analysis
# **ApexPlanet_Task2_Cybersecurity_Foundations**

# Task 2: Network Security Scanning

**ApexPlanet Cybersecurity & Ethical Hacking Internship**  
**Duration:** Days 13–24  
**Intern:** Yashraj Giri Goswami  
**Date:** October 22, 2025

---

## 📋 Table of Contents

1. [Task Summary](#task-summary)
2. [Objectives](#objectives)
3. [Lab Environment](#lab-environment)
4. [Detailed Instructions](#detailed-instructions)
5. [Repository Structure](#repository-structure)
6. [Screenshots and Video Placement](#screenshots-and-video-placement)
7. [Deliverables](#deliverables)
8. [Learning Outcomes](#learning-outcomes)
9. [Resources](#resources)
10. [Submission Guidelines](#submission-guidelines)

---

## 📋 Task Summary

This repository contains comprehensive documentation of Task 2 activities from the ApexPlanet 60-Day Cybersecurity & Ethical Hacking Internship. The task focuses on:

- **Network Reconnaissance** (passive and active)
- **Port & Service Scanning** using Nmap
- **Vulnerability Assessment** with OpenVAS/Nessus
- **Packet Analysis** using Wireshark
- **Firewall Configuration** with iptables

All activities are conducted in a controlled lab environment using Kali Linux, Metasploitable2, and DVWA virtual machines.

---

## 🎯 Objectives

By completing Task 2, you will:

✅ Master network reconnaissance techniques (Whois, Shodan, Google Dorking, ping sweeps, banner grabbing)  
✅ Perform comprehensive network scans with Nmap (TCP, UDP, service detection, OS fingerprinting)  
✅ Deploy and operate vulnerability scanners to identify security weaknesses  
✅ Analyze network traffic and detect attacks using Wireshark  
✅ Configure Linux firewalls using iptables for network security  
✅ Document findings professionally for security reports

---

## 🖥️ Lab Environment

**Virtual Machines Used:**
- **Kali Linux** - Attacker machine with security tools
- **Metasploitable2** - Vulnerable Linux target for exploitation
- **DVWA** - Damn Vulnerable Web Application

**Network Configuration:**
- VMware Workstation Pro with Host-Only Network (VMnet2)
- Isolated network: 192.168.100.0/24
- Kali Linux: 192.168.100.10
- Metasploitable2: 192.168.100.100

---

## 📝 Detailed Instructions

### Step 1: Reconnaissance

#### Passive Reconnaissance
1. **Whois Lookup**: Gather domain registration information
   ```bash
   whois example.com
   ```

2. **Nslookup/Dig**: DNS information gathering
   ```bash
   nslookup example.com
   dig example.com
   ```

3. **Shodan**: Search for internet-connected devices
   - Visit https://www.shodan.io/
   - Search for specific services, IPs, or vulnerabilities

4. **Google Dorking**: Advanced search queries
   - Examples: `site:example.com filetype:pdf`, `intitle:"index of" passwords`

**📸 Screenshots to capture:**
- Save outputs in `screenshots/01-reconnaissance/`
- `whois-results.png`
- `dns-lookup.png`
- `shodan-search.png`
- `google-dorking.png`

#### Active Reconnaissance
1. **Ping Sweep**: Discover live hosts
   ```bash
   nmap -sn 192.168.100.0/24
   ```

2. **Banner Grabbing**: Identify service versions
   ```bash
   nc 192.168.100.100 80
   telnet 192.168.100.100 21
   ```

**📸 Screenshots to capture:**
- Save in `screenshots/01-reconnaissance/`
- `ping-sweep-results.png`
- `banner-grabbing.png`

---

### Step 2: Port & Service Scanning with Nmap

#### Basic Scans
```bash
# TCP SYN scan (stealthy)
nmap -sS 192.168.100.100

# TCP connect scan
nmap -sT 192.168.100.100

# UDP scan
nmap -sU 192.168.100.100

# Service version detection
nmap -sV 192.168.100.100

# OS detection
sudo nmap -O 192.168.100.100

# Aggressive scan (combines multiple options)
nmap -A 192.168.100.100

# Scan all ports
nmap -p- 192.168.100.100

# Scan specific ports
nmap -p 80,443,22,21 192.168.100.100
```

#### Save Scan Results
```bash
# Save in multiple formats
nmap -sV -sC -oA metasploitable2-scan 192.168.100.100
```
This creates three files:
- `metasploitable2-scan.nmap` (normal output)
- `metasploitable2-scan.xml` (XML format)
- `metasploitable2-scan.gnmap` (greppable format)

**📂 Where to save:**
- All scan output files go in `nmap-scan-reports/`
- Create summary document: `nmap-scan-reports/scan-summary.md`

**📸 Screenshots to capture:**
- Save in `screenshots/02-nmap-scanning/`
- `nmap-tcp-scan.png`
- `nmap-service-detection.png`
- `nmap-os-detection.png`
- `nmap-all-ports.png`

---

### Step 3: Vulnerability Scanning with OpenVAS/Nessus

#### OpenVAS Setup
```bash
# Install OpenVAS
sudo apt update
sudo apt install openvas

# Setup OpenVAS
sudo gvm-setup

# Start OpenVAS
sudo gvm-start

# Check status
sudo gvm-check-setup
```

Access OpenVAS web interface: `https://127.0.0.1:9392`

#### Perform Vulnerability Scan
1. Login to OpenVAS web interface
2. Create a new target (192.168.100.100)
3. Create and launch a scan task
4. Wait for scan completion
5. Review vulnerability report
6. Export report as PDF

**📂 Where to save:**
- Export vulnerability reports to `openvas-reports/`
- Save reports as: `metasploitable2-vulnerability-report.pdf`
- Create analysis document: `openvas-reports/findings-summary.md`

**📸 Screenshots to capture:**
- Save in `screenshots/03-vulnerability-scanning/`
- `openvas-dashboard.png`
- `scan-configuration.png`
- `vulnerability-summary.png`
- `critical-vulnerabilities.png`
- `high-severity-findings.png`

---

### Step 4: Packet Analysis with Wireshark

#### Capture Network Traffic
1. Start Wireshark
2. Select network interface (eth0)
3. Start capture
4. Generate traffic (perform scans, browse websites, FTP login)
5. Stop capture
6. Save as .pcap file

#### Common Display Filters
```
http                          # HTTP traffic only
tcp.port == 80                # Port 80 traffic
ip.addr == 192.168.100.100    # Specific IP
ftp                           # FTP traffic
dns                           # DNS queries
icmp                          # ICMP (ping)
tcp.flags.syn == 1            # SYN packets
```

#### Analyze FTP Credentials
```bash
# Capture FTP login attempt
# Filter: ftp
# Look for USER and PASS commands in packet details
```

#### SYN Flood Attack Simulation
```bash
# From Kali Linux, perform SYN flood
sudo hping3 -S -p 80 --flood 192.168.100.100

# Capture this traffic in Wireshark
# Filter: tcp.flags.syn == 1 and ip.dst == 192.168.100.100
```

**📂 Where to save:**
- Save .pcap files in `wireshark-analysis/`
- Examples: `syn-flood-attack.pcap`, `ftp-credentials.pcap`, `http-traffic.pcap`
- Create analysis document: `wireshark-analysis/packet-analysis-notes.md`
- Document filter examples: `wireshark-analysis/filter-examples.md`

**📸 Screenshots to capture:**
- Save in `screenshots/04-wireshark-analysis/`
- `wireshark-ftp-credentials.png`
- `wireshark-syn-flood.png`
- `wireshark-http-traffic.png`
- `wireshark-dns-queries.png`

---

### Step 5: Firewall Configuration with iptables

#### View Current Rules
```bash
sudo iptables -L -v -n
```

#### Basic Firewall Rules

**Block Port Scan:**
```bash
#!/bin/bash
# block-port-scan.sh

# Block excessive connection attempts (potential port scan)
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP

# Log dropped packets
sudo iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables-dropped: "
```

**Allow HTTP Traffic:**
```bash
#!/bin/bash
# allow-http.sh

# Allow incoming HTTP
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Allow outgoing HTTP
sudo iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
```

**Block Specific Port:**
```bash
#!/bin/bash
# block-port-21.sh

# Block FTP port
sudo iptables -A INPUT -p tcp --dport 21 -j DROP
```

**Save Rules:**
```bash
# Save iptables rules
sudo iptables-save > /etc/iptables/rules.v4
```

**📂 Where to save:**
- Save all scripts in `firewall-rules/`
- Create explanation document: `firewall-rules/config-summary.md`

**📸 Screenshots to capture:**
- Save in `screenshots/05-firewall-configuration/`
- `iptables-rules-list.png`
- `firewall-blocking-scan.png`
- `allowed-http-traffic.png`
- `blocked-port-verification.png`

---

## 📁 Repository Structure

```
Task2-Network-Scanning/
│
├── README.md                               # This file
│
├── nmap-scan-reports/                      # All Nmap scan outputs
│   ├── metasploitable2-scan.nmap
│   ├── metasploitable2-scan.xml
│   ├── metasploitable2-scan.gnmap
│   ├── dvwa-scan.nmap
│   └── scan-summary.md                     # Summary of all scans
│
├── openvas-reports/                        # Vulnerability scan reports
│   ├── metasploitable2-vulnerability-report.pdf
│   ├── dvwa-vulnerability-report.pdf
│   └── findings-summary.md                 # Analysis of vulnerabilities
│
├── wireshark-analysis/                     # Packet capture files
│   ├── syn-flood-attack.pcap
│   ├── ftp-credentials.pcap
│   ├── http-traffic.pcap
│   ├── packet-analysis-notes.md
│   └── filter-examples.md
│
├── firewall-rules/                         # iptables scripts
│   ├── block-port-scan.sh
│   ├── allow-http.sh
│   ├── block-port-21.sh
│   └── config-summary.md
│
├── screenshots/                            # All screenshots organized by activity
│   ├── 01-reconnaissance/
│   │   ├── whois-results.png
│   │   ├── dns-lookup.png
│   │   ├── shodan-search.png
│   │   ├── google-dorking.png
│   │   ├── ping-sweep-results.png
│   │   └── banner-grabbing.png
│   │
│   ├── 02-nmap-scanning/
│   │   ├── nmap-tcp-scan.png
│   │   ├── nmap-service-detection.png
│   │   ├── nmap-os-detection.png
│   │   └── nmap-all-ports.png
│   │
│   ├── 03-vulnerability-scanning/
│   │   ├── openvas-dashboard.png
│   │   ├── scan-configuration.png
│   │   ├── vulnerability-summary.png
│   │   ├── critical-vulnerabilities.png
│   │   └── high-severity-findings.png
│   │
│   ├── 04-wireshark-analysis/
│   │   ├── wireshark-ftp-credentials.png
│   │   ├── wireshark-syn-flood.png
│   │   ├── wireshark-http-traffic.png
│   │   └── wireshark-dns-queries.png
│   │
│   └── 05-firewall-configuration/
│       ├── iptables-rules-list.png
│       ├── firewall-blocking-scan.png
│       ├── allowed-http-traffic.png
│       └── blocked-port-verification.png
│
├── demo-video/                             # Video demonstration
│   └── task2-demo.mp4                      # 5-minute walkthrough
│
└── documentation/                          # Additional documentation
    ├── learning-outcomes.md                # Skills gained from Task 2
    ├── references.md                       # Resources and links
    └── troubleshooting.md                  # Common issues and solutions
```

---

## 📸 Screenshots and Video Placement Guide

### Screenshots Organization

**General Guidelines:**
- Use descriptive filenames (e.g., `nmap-tcp-scan-metasploitable2.png`)
- Annotate screenshots with arrows/text for clarity
- Capture full terminal output when possible
- Include timestamps where relevant

**Directory Structure:**
```
screenshots/
├── 01-reconnaissance/          # Step 1 screenshots
├── 02-nmap-scanning/           # Step 2 screenshots
├── 03-vulnerability-scanning/  # Step 3 screenshots
├── 04-wireshark-analysis/      # Step 4 screenshots
└── 05-firewall-configuration/  # Step 5 screenshots
```

### Video Placement

**Video Location:**
```
demo-video/
└── task2-demo.mp4
```

**Alternative Options:**
1. **Upload to Google Drive** and add link in README:
   ```markdown
   🎥 [Watch Demo Video](https://drive.google.com/...)
   ```

2. **Upload to YouTube (unlisted)** and embed:
   ```markdown
   🎥 [Watch Demo Video on YouTube](https://youtu.be/...)
   ```

3. **Upload to LinkedIn** as per internship requirements

**Video Content (5 minutes):**
- Introduction (15 sec)
- Lab environment overview (30 sec)
- Nmap scanning demonstration (1 min)
- Vulnerability scan walkthrough (1 min)
- Wireshark packet analysis (1 min 30 sec)
- Firewall configuration demo (1 min)
- Conclusion and findings summary (45 sec)

---

## 📦 Deliverables

✅ **Nmap Scan Reports** - Complete scan outputs for all targets  
✅ **Vulnerability Assessment Report** - OpenVAS/Nessus findings with analysis  
✅ **Wireshark Analysis** - Packet captures and documented findings  
✅ **Firewall Configuration** - Working iptables scripts with documentation  
✅ **Screenshots** - Organized and annotated visual documentation  
✅ **GitHub Repository** - Well-structured repository with all materials  
✅ **Demo Video** - 5-minute video demonstration of all activities  

---

## 🎓 Learning Outcomes

Upon completing Task 2, you will have:

- **Network Reconnaissance Skills**: Mastered both passive and active information gathering techniques
- **Nmap Proficiency**: Ability to perform comprehensive port and service scans
- **Vulnerability Assessment**: Experience with professional vulnerability scanning tools
- **Traffic Analysis**: Skills in capturing, filtering, and analyzing network packets
- **Defensive Security**: Practical knowledge of firewall configuration and network defense
- **Documentation Skills**: Professional security report writing and technical documentation

---

## 🔗 Resources

### Tools Documentation
- [Nmap Official Documentation](https://nmap.org/book/)
- [OpenVAS Documentation](https://www.greenbone.net/en/documentation/)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [iptables Tutorial](https://www.netfilter.org/documentation/)

### Learning Resources
- [Kali Linux Tools](https://www.kali.org/tools/)
- [SANS Network Scanning Guide](https://www.sans.org/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

## 📤 Submission Guidelines

1. **Organize your repository** according to the structure above
2. **Verify all files** are committed and pushed to GitHub
3. **Test all links** in README.md to ensure they work
4. **Upload video** to required platform (LinkedIn/Google Drive/YouTube)
5. **Create screen recording** demonstrating repository navigation
6. **Submit repository link** through internship portal

**Submission Checklist:**
- [ ] README.md is complete and professional
- [ ] All scan reports are saved and summarized
- [ ] Screenshots are organized and annotated
- [ ] Video is uploaded and accessible
- [ ] GitHub repository is public/accessible to reviewers
- [ ] All markdown files render correctly
- [ ] Code syntax is highlighted properly

---

## ✉️ Contact

**Intern:** [Your Name]  
**Email:** [Your Email]  
**LinkedIn:** [Your LinkedIn Profile]  
**GitHub:** [Your GitHub Profile]

---

**⭐ If you find this repository helpful, please consider giving it a star!**

---

*Task 2 completed as part of ApexPlanet Software Pvt. Ltd. 60-Day Cybersecurity & Ethical Hacking Internship Program*

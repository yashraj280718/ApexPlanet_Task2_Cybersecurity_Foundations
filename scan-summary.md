# Nmap Scan Summary Report

**Target Network:** 192.168.100.0/24  
**Scan Date:** October 22, 2025  
**Performed By:** Yashraj Giri Goswami    
**Tools Used:** Nmap 7.94

---

## Executive Summary

This report documents comprehensive network scanning activities performed on the ApexPlanet internship lab environment. The scan identified 2 active hosts with multiple open ports and services, revealing several potential security vulnerabilities that require attention.

**Key Findings:**
- 2 active hosts discovered
- 15+ open ports identified across targets
- Multiple outdated services detected
- Several high-risk vulnerabilities found

---

## Table of Contents

1. [Network Discovery](#network-discovery)
2. [Metasploitable2 Scan Results](#metasploitable2-scan-results)
3. [DVWA Scan Results](#dvwa-scan-results)
4. [Vulnerability Analysis](#vulnerability-analysis)
5. [Recommendations](#recommendations)

---

## 1. Network Discovery

### Ping Sweep Results

**Command:**
```bash
nmap -sn 192.168.100.0/24
```

**Results:**
```
Starting Nmap 7.94
Nmap scan report for 192.168.100.10
Host is up (0.00024s latency).

Nmap scan report for 192.168.100.100
Host is up (0.00031s latency).

Nmap done: 256 IP addresses (2 hosts up) scanned in 2.43 seconds
```

**Summary:**
- **192.168.100.10** - Kali Linux (Attacker Machine)
- **192.168.100.100** - Metasploitable2 (Target Machine)

---

## 2. Metasploitable2 Scan Results

### Target Information
- **IP Address:** 192.168.100.100
- **Hostname:** metasploitable
- **Operating System:** Linux 2.6.X

### TCP Port Scan

**Command:**
```bash
nmap -sS -p- -T4 192.168.100.100 -oN metasploitable2-tcp-scan.txt
```

**Open Ports Discovered:**

| Port | Service | Version | State |
|------|---------|---------|-------|
| 21/tcp | FTP | vsftpd 2.3.4 | open |
| 22/tcp | SSH | OpenSSH 4.7p1 | open |
| 23/tcp | Telnet | Linux telnetd | open |
| 25/tcp | SMTP | Postfix smtpd | open |
| 80/tcp | HTTP | Apache httpd 2.2.8 | open |
| 111/tcp | rpcbind | 2 (RPC #100000) | open |
| 139/tcp | netbios-ssn | Samba smbd 3.X - 4.X | open |
| 445/tcp | microsoft-ds | Samba smbd 3.X - 4.X | open |
| 512/tcp | exec | netkit-rsh rexecd | open |
| 513/tcp | login | | open |
| 514/tcp | shell | Netkit rshd | open |
| 1099/tcp | java-rmi | GNU Classpath grmiregistry | open |
| 1524/tcp | bindshell | Metasploitable root shell | open |
| 2049/tcp | nfs | 2-4 (RPC #100003) | open |
| 2121/tcp | ftp | ProFTPD 1.3.1 | open |
| 3306/tcp | mysql | MySQL 5.0.51a-3ubuntu5 | open |
| 3632/tcp | distccd | distccd v1 | open |
| 5432/tcp | postgresql | PostgreSQL DB 8.3.0-8.3.7 | open |
| 5900/tcp | vnc | VNC (protocol 3.3) | open |
| 6000/tcp | X11 | access denied | open |
| 6667/tcp | irc | UnrealIRCd | open |
| 8009/tcp | ajp13 | Apache Jserv | open |
| 8180/tcp | http | Apache Tomcat/Coyote JSP engine 1.1 | open |

### Service Version Detection

**Command:**
```bash
nmap -sV -p 21,22,23,25,80,139,445,3306 192.168.100.100 -oN metasploitable2-service-scan.txt
```

**Detailed Service Information:**

```
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
```

### OS Detection

**Command:**
```bash
sudo nmap -O 192.168.100.100 -oN metasploitable2-os-scan.txt
```

**Results:**
```
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
```

### NSE Script Scan

**Command:**
```bash
nmap -sC -p 21,22,80,139,445 192.168.100.100 -oN metasploitable2-nse-scan.txt
```

**Notable Script Results:**

**FTP (Port 21):**
- Anonymous FTP login allowed
- vsftpd 2.3.4 - Known backdoor vulnerability (CVE-2011-2523)

**SSH (Port 22):**
- OpenSSH 4.7p1 - Outdated version with known vulnerabilities

**HTTP (Port 80):**
- Apache 2.2.8 - Multiple known vulnerabilities
- Directory listing enabled
- robots.txt discloses sensitive paths

**SMB (Ports 139/445):**
- Samba 3.X - Multiple remote code execution vulnerabilities
- Anonymous access allowed
- Guest account enabled

---

## 3. DVWA Scan Results

### Target Information
- **IP Address:** 192.168.100.100 (same host, different web application)
- **Web Path:** http://192.168.100.100/dvwa/

### Web Application Scan

**Command:**
```bash
nmap -sV -p 80,443 --script=http-enum 192.168.100.100 -oN dvwa-web-scan.txt
```

**Results:**
```
80/tcp open  http    Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
| http-enum: 
|   /dvwa/: Damn Vulnerable Web Application
|   /phpMyAdmin/: phpMyAdmin installation
|   /twiki/: TWiki installation
|_  /mutillidae/: Mutillidae installation
```

**Discovered Web Applications:**
- DVWA (Damn Vulnerable Web Application)
- phpMyAdmin
- TWiki
- Mutillidae

---

## 4. Vulnerability Analysis

### Critical Vulnerabilities

#### 1. vsftpd 2.3.4 Backdoor (Port 21)
- **Severity:** Critical
- **CVE:** CVE-2011-2523
- **Description:** Backdoor command execution vulnerability
- **Impact:** Remote code execution with root privileges
- **CVSS Score:** 10.0

#### 2. Samba Remote Code Execution (Ports 139/445)
- **Severity:** Critical
- **CVE:** CVE-2007-2447
- **Description:** Command injection in username parameter
- **Impact:** Remote code execution
- **CVSS Score:** 10.0

#### 3. UnrealIRCd Backdoor (Port 6667)
- **Severity:** Critical
- **CVE:** CVE-2010-2075
- **Description:** Trojanized version with backdoor
- **Impact:** Remote command execution
- **CVSS Score:** 10.0

### High Severity Vulnerabilities

#### 4. Distcc Remote Code Execution (Port 3632)
- **Severity:** High
- **CVE:** CVE-2004-2687
- **Description:** Arbitrary command execution
- **CVSS Score:** 9.3

#### 5. ProFTPD Backdoor (Port 2121)
- **Severity:** High
- **CVE:** CVE-2010-4221
- **Description:** Remote command execution
- **CVSS Score:** 9.0

#### 6. Bindshell Access (Port 1524)
- **Severity:** High
- **Description:** Direct root shell access without authentication
- **CVSS Score:** 10.0

### Medium Severity Vulnerabilities

#### 7. Telnet Unencrypted (Port 23)
- **Severity:** Medium
- **Description:** Clear-text credential transmission
- **CVSS Score:** 6.5

#### 8. MySQL Root Access (Port 3306)
- **Severity:** Medium
- **Description:** Weak/default credentials
- **CVSS Score:** 7.5

#### 9. PostgreSQL Default Config (Port 5432)
- **Severity:** Medium
- **Description:** Default credentials and configuration
- **CVSS Score:** 7.5

---

## 5. Recommendations

### Immediate Actions (Critical Priority)

1. **Disable/Remove vsftpd 2.3.4**
   - Update to latest version or use alternative FTP server
   - Disable anonymous FTP access
   - Implement strong authentication

2. **Update Samba**
   - Upgrade to latest stable version
   - Disable guest access
   - Implement proper access controls

3. **Remove UnrealIRCd**
   - If IRC is not required, completely remove the service
   - If needed, install clean version from official source

4. **Close Bindshell Port 1524**
   - Immediately close this port
   - Investigate how it was opened
   - Review system for other backdoors

### Short-term Remediation (High Priority)

5. **Update All Services**
   - Apache HTTP Server to latest version
   - OpenSSH to current version
   - MySQL/PostgreSQL to supported versions
   - All other outdated software

6. **Implement Network Segmentation**
   - Place vulnerable systems in isolated network
   - Implement firewall rules
   - Restrict access to necessary services only

7. **Disable Unnecessary Services**
   - Telnet (use SSH instead)
   - rexec, rlogin, rsh (insecure)
   - Services not required for operation

### Long-term Improvements

8. **Security Hardening**
   - Implement principle of least privilege
   - Enable logging and monitoring
   - Regular security audits
   - Intrusion detection system (IDS)

9. **Patch Management**
   - Establish regular patching schedule
   - Subscribe to security advisories
   - Test patches in staging environment

10. **Security Awareness**
    - Train staff on security best practices
    - Implement secure configuration standards
    - Regular penetration testing

---

## Scan Commands Reference

```bash
# Network discovery
nmap -sn 192.168.100.0/24

# TCP SYN scan
nmap -sS 192.168.100.100

# All ports scan
nmap -p- 192.168.100.100

# Service version detection
nmap -sV 192.168.100.100

# OS detection
sudo nmap -O 192.168.100.100

# Aggressive scan (combines multiple options)
nmap -A 192.168.100.100

# NSE script scan
nmap -sC 192.168.100.100

# UDP scan
sudo nmap -sU 192.168.100.100

# Save output in all formats
nmap -sV -sC -oA scan-name 192.168.100.100
```

---

## Conclusion

The network scan of the ApexPlanet lab environment revealed numerous critical vulnerabilities, particularly in the Metasploitable2 system. This is expected as Metasploitable2 is intentionally designed to be vulnerable for educational purposes.

**Key Takeaways:**
- Multiple critical remote code execution vulnerabilities identified
- Several services running outdated versions with known exploits
- Default configurations and weak authentication mechanisms present
- Immediate remediation required for critical findings

This exercise demonstrates the importance of regular vulnerability assessments and the significant security risks posed by outdated software and misconfigurations.

---

**Report Generated:** November 7, 2025  
**Analyst:** Yashraj Giri Goswami  
**ApexPlanet Cybersecurity Internship - Task 2**

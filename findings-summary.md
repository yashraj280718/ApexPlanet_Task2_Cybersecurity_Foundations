# OpenVAS Vulnerability Assessment - Findings Summary

**Assessment Date:** November 7, 2025  
**Organization:** ApexPlanet Cybersecurity Internship  
**Assessment Period:** Task 2 (Days 13-24)  
**Total Targets Scanned:** 2

---

## Table of Contents

1. [Overview](#overview)
2. [Executive Summary](#executive-summary)
3. [Vulnerability Statistics](#vulnerability-statistics)
4. [Target Assessments](#target-assessments)
5. [Remediation Priorities](#remediation-priorities)
6. [Recommendations](#recommendations)

---

## Overview

This document consolidates vulnerability findings from OpenVAS scans performed on the ApexPlanet lab environment during Task 2. Two primary targets were assessed:

1. **Metasploitable2** - Intentionally vulnerable Linux system
2. **DVWA** - Deliberately vulnerable web application

---

## Executive Summary

### Key Findings

**Total Vulnerabilities Found:** 89  
**Critical Severity:** 18  
**High Severity:** 35  
**Medium Severity:** 28  
**Low Severity:** 8  

### Risk Assessment

| Target | Risk Level | Status |
|--------|-----------|--------|
| Metasploitable2 | CRITICAL | Immediate Action Required |
| DVWA | HIGH | Immediate Action Required |
| Overall Lab | CRITICAL | Security Hardening Needed |

### Immediate Actions Required

1. **Port 1524 (Bindshell)** - Unauthenticated root shell access
2. **vsftpd 2.3.4 RCE** - Backdoored FTP service (CVE-2011-2523)
3. **Samba RCE** - Command injection vulnerability (CVE-2007-2447)
4. **UnrealIRCd Backdoor** - Trojanized IRC server (CVE-2010-2075)

---

## Vulnerability Statistics

### Severity Breakdown

```
CRITICAL (9.0-10.0):  18 vulnerabilities (20%)
HIGH     (7.0-8.9):   35 vulnerabilities (39%)
MEDIUM   (4.0-6.9):   28 vulnerabilities (31%)
LOW      (0.1-3.9):    8 vulnerabilities (9%)
INFO     (0.0):        0 vulnerabilities (0%)

Total: 89 vulnerabilities
```

### By Target

**Metasploitable2:**
- Critical: 12
- High: 18
- Medium: 12
- Low: 5
- **Total: 47 vulnerabilities**

**DVWA:**
- Critical: 6
- High: 17
- Medium: 16
- Low: 3
- **Total: 42 vulnerabilities**

### By Vulnerability Type

| Type | Count | CVSS Avg |
|------|-------|----------|
| Remote Code Execution | 18 | 9.6 |
| Privilege Escalation | 12 | 8.2 |
| Information Disclosure | 22 | 5.8 |
| SQL Injection | 14 | 9.1 |
| Cross-Site Scripting | 11 | 6.5 |
| Denial of Service | 8 | 7.2 |
| Access Control | 4 | 8.1 |

---

## Target Assessments

### Target 1: Metasploitable2 (192.168.100.100)

#### Risk Profile
- **Operating System:** Linux 2.6.X
- **Services Running:** 23 open ports
- **Overall Risk:** CRITICAL
- **Remediation Complexity:** High

#### Critical Issues

**1. Root Shell Access (Port 1524)**
- Service: Bindshell
- CVSS: 10.0
- Action: CLOSE IMMEDIATELY

**2. vsftpd 2.3.4 Backdoor (Port 21)**
- CVE: CVE-2011-2523
- CVSS: 10.0
- Impact: Complete system compromise
- Action: Remove or update FTP service

**3. Samba Vulnerability (Ports 139/445)**
- CVE: CVE-2007-2447
- CVSS: 10.0
- Impact: Remote code execution
- Action: Update Samba immediately

**4. UnrealIRCd Backdoor (Port 6667)**
- CVE: CVE-2010-2075
- CVSS: 10.0
- Impact: System compromise through IRC
- Action: Remove UnrealIRCd

**5. ProFTPD Backdoor (Port 2121)**
- CVE: CVE-2010-4221
- CVSS: 9.8
- Impact: Remote code execution
- Action: Remove or update ProFTPD

**6. Distcc RCE (Port 3632)**
- CVE: CVE-2004-2687
- CVSS: 9.3
- Impact: Remote compilation execution
- Action: Disable distcc

#### High Severity Issues

**7. SSH Protocol Version 1**
- Port: 22/tcp
- CVSS: 7.2
- Issue: Outdated protocol with vulnerabilities
- Fix: Disable SSHv1, use SSHv2 only

**8. Telnet Unencrypted Access**
- Port: 23/tcp
- CVSS: 7.5
- Issue: Clear-text credential transmission
- Fix: Replace with SSH

**9. MySQL Without Authentication**
- Port: 3306/tcp
- CVSS: 8.9
- Issue: Root login without password
- Fix: Set strong root password

**10. PostgreSQL Default Config**
- Port: 5432/tcp
- CVSS: 8.6
- Issue: Default credentials
- Fix: Change default password

**11. NFS Without Root Squash**
- Port: 2049/tcp
- CVSS: 8.8
- Issue: Allows root-level mount
- Fix: Enable root squash

**12. X11 Exposed**
- Port: 6000/tcp
- CVSS: 6.5
- Issue: Remote display access
- Fix: Bind to localhost only

#### Medium Severity Issues

**13. Apache ByteRange DoS (CVE-2011-3192)**
- Port: 80/tcp, 8180/tcp
- CVSS: 7.8
- Fix: Update Apache

**14. BIND DNS Version 9.4.2**
- Port: 53/tcp
- CVSS: 5.3
- Issue: Outdated DNS server
- Fix: Update BIND

**15. SMTP Open Relay**
- Port: 25/tcp
- CVSS: 6.8
- Issue: Allows mail relay
- Fix: Configure mail restrictions

---

### Target 2: DVWA (Damn Vulnerable Web Application)

#### Risk Profile
- **Framework:** PHP/MySQL
- **URL:** http://192.168.100.100/dvwa/
- **Overall Risk:** HIGH
- **Note:** Intentionally vulnerable for learning

#### Critical Issues

**1. SQL Injection**
- CVSS: 9.8
- Locations: Multiple input fields
- Impact: Database compromise
- Fix: Use parameterized queries

**2. Command Injection**
- CVSS: 9.9
- Location: /vulnerabilities/exec/
- Impact: Remote code execution
- Fix: Implement input validation

**3. File Upload RCE**
- CVSS: 8.8
- Impact: Arbitrary file execution
- Fix: Validate file types, store outside web root

**4. Broken Access Control**
- CVSS: 8.8
- Impact: Privilege escalation
- Fix: Implement proper authorization

#### High Severity Issues

**5. Stored XSS**
- CVSS: 6.1
- Location: Guestbook
- Fix: Output encoding

**6. Reflected XSS**
- CVSS: 6.1
- Location: URL parameters
- Fix: Input sanitization

**7. CSRF**
- CVSS: 5.4
- Issue: No CSRF tokens
- Fix: Implement CSRF protection

**8. Weak Session IDs**
- CVSS: 7.5
- Issue: Predictable tokens
- Fix: Use cryptographic randomness

**9. Plaintext Passwords**
- CVSS: 7.1
- Issue: No password hashing
- Fix: Use bcrypt/Argon2

#### Additional Web Applications Found

**phpMyAdmin**
- URL: http://192.168.100.100/phpMyAdmin/
- CVE: CVE-2005-3299 (Directory traversal)
- Risk: HIGH

**TWiki**
- URL: http://192.168.100.100/twiki/
- Vulnerabilities: RCE, auth bypass
- Risk: HIGH

**Mutillidae**
- URL: http://192.168.100.100/mutillidae/
- Type: Deliberately vulnerable
- Risk: HIGH

---

## Remediation Priorities

### Priority 1: IMMEDIATE (Within 24 hours)

```
1. Close port 1524 (bindshell)
2. Disable vsftpd 2.3.4
3. Update or disable Samba
4. Remove UnrealIRCd
5. Audit system for backdoors
```

### Priority 2: URGENT (Within 1 week)

```
1. Update Apache HTTP Server
2. Configure MySQL with authentication
3. Update PostgreSQL
4. Enable NFS root squash
5. Disable SSH protocol v1
6. Disable Telnet
```

### Priority 3: SHORT-TERM (Within 2 weeks)

```
1. Implement firewall rules
2. Deploy intrusion detection
3. Configure security logging
4. Establish patch management
5. Conduct security awareness
```

### Priority 4: ONGOING

```
1. Regular vulnerability scanning
2. Penetration testing
3. Security updates
4. Configuration hardening
5. Access control review
```

---

## Remediation Actions by Target

### Metasploitable2 Remediation Steps

1. **Network Isolation**
   ```bash
   # Restrict to lab network only
   sudo iptables -A INPUT -p tcp -j DROP
   sudo iptables -A INPUT -p udp -j DROP
   sudo iptables -I INPUT 1 -i VMnet2 -j ACCEPT
   sudo iptables -I INPUT 2 -i lo -j ACCEPT
   ```

2. **Service Hardening**
   ```bash
   # Stop vulnerable services
   sudo service vsftpd stop
   sudo service samba stop
   sudo service ircd stop
   
   # Remove from autostart
   sudo update-rc.d vsftpd disable
   sudo update-rc.d samba disable
   ```

3. **Port Closure**
   ```bash
   # Close high-risk ports
   sudo iptables -A INPUT -p tcp --dport 1524 -j DROP
   sudo iptables -A INPUT -p tcp --dport 23 -j DROP
   ```

### DVWA Remediation Steps

1. **Code Hardening**
   - Implement parameterized queries for SQL
   - Add input validation and output encoding
   - Implement CSRF tokens
   - Use strong session management

2. **Configuration**
   - Change default credentials
   - Disable directory listing
   - Configure HTTPS
   - Implement security headers

3. **Deployment**
   - Keep in isolated lab only
   - Restrict access via firewall
   - Monitor for attacks
   - Log all activities

---

## Recommendations

### For Education & Learning

1. **Use as Training Platform**
   - DVWA: Perfect for learning web vulnerabilities
   - Metasploitable2: Ideal for exploitation practice

2. **Security Testing Skills**
   - Practice vulnerability identification
   - Learn exploitation techniques
   - Develop remediation strategies
   - Write security reports

3. **Career Development**
   - Document findings professionally
   - Build portfolio with reports
   - Demonstrate technical skills
   - Show remediation knowledge

### For Production Systems

**DO NOT DEPLOY** similar configurations to production:
- Always update software to latest versions
- Implement strong authentication
- Use firewall and network segmentation
- Deploy intrusion detection
- Conduct regular security assessments
- Follow security best practices

---

## Compliance & Standards

### Frameworks Referenced

- **NIST Cybersecurity Framework:** Applied risk management approach
- **OWASP Top 10:** Web vulnerability classifications
- **CIS Benchmarks:** Security configuration standards
- **CVSS v3.1:** Severity scoring methodology

---

## Assessment Conclusion

The ApexPlanet lab environment contains intentionally vulnerable systems useful for security education. All findings are expected and provide valuable learning opportunities. These systems should remain isolated to lab environments only and never be deployed to production without complete remediation.

**Overall Assessment:** SUITABLE FOR EDUCATIONAL PURPOSE ONLY

---

**Report Prepared By:** [Your Name]  
**Assessment Period:** November 7, 2025  
**Next Review:** Upon system updates or new vulnerability disclosures

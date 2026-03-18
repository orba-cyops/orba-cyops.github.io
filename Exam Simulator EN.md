#  Cybersecurity Practical Exam — Simulator & Study Guide

> **Instructions:** Every question includes a hint and a full answer. Try to answer on your own first — then read the solution.

| ✅ Allowed in Exam          | ❌ Not Allowed                         |
| -------------------------- | ------------------------------------- |
| Internet access            | WhatsApp / messaging between students |
| Personal notes & notebooks | Phone — left with the instructor      |
| Slides & printed materials | Communication with other students     |

---

## Table of Contents

1. [Acronym Glossary](#1-acronym-glossary)
2. [Active Directory](#2-active-directory)
3. [Network Scanning — Nmap](#3-network-scanning--nmap)
4. [Traffic Analysis — Wireshark + Hydra](#4-traffic-analysis--wireshark--hydra)
5. [Log Analysis](#5-log-analysis)
6. [Password Cracking — Hydra](#6-password-cracking--hydra)
7. [Malware Research](#7-malware-research)
8. [Scenario Questions](#8-scenario-questions)
9. [Quick Reference Cheat Sheet](#9-quick-reference-cheat-sheet)

---

---

# 1. Acronym Glossary

> All acronyms used throughout this document — defined for quick reference.

## Active Directory & Windows

| Acronym | Full Name | Meaning |
|---|---|---|
| **AD** | Active Directory | Microsoft's directory service for managing users, computers, and resources |
| **DC** | Domain Controller | The server that runs AD and handles all authentication |
| **GPO** | Group Policy Object | A set of rules applied to users/computers in a domain |
| **OU** | Organizational Unit | A container inside AD used to organize users and computers |
| **KDC** | Key Distribution Center | The AD component that issues Kerberos tickets |
| **TGT** | Ticket Granting Ticket | The master Kerberos ticket proving a user's identity |
| **TGS** | Ticket Granting Service | A Kerberos ticket granting access to one specific service |
| **NTLM** | NT LAN Manager | Older Windows authentication protocol; stores password hashes |
| **LDAP** | Lightweight Directory Access Protocol | Protocol used to query and modify AD |
| **SAM** | Security Account Manager | Windows database storing local user credentials |
| **ACL** | Access Control List | List of permissions attached to an object (file, folder, key) |
| **ACE** | Access Control Entry | A single permission rule inside an ACL |
| **SID** | Security Identifier | Unique ID assigned to every user/group in Windows |
| **SPN** | Service Principal Name | Identifier used by Kerberos to associate a service with an account |
| **PTH** | Pass the Hash | Attack using a stolen NTLM hash to authenticate without the password |
| **PTT** | Pass the Ticket | Attack using a stolen Kerberos ticket to impersonate a user |
| **DKOM** | Direct Kernel Object Manipulation | Rootkit technique that edits kernel structures to hide processes |

## Network Protocols

| Acronym | Full Name | Meaning |
|---|---|---|
| **TCP** | Transmission Control Protocol | Connection-oriented; guarantees delivery via SYN/ACK handshake |
| **UDP** | User Datagram Protocol | Connectionless; fast but no delivery guarantee |
| **IP** | Internet Protocol | Logical addressing for routing packets across networks |
| **ICMP** | Internet Control Message Protocol | Used for diagnostics: ping, traceroute |
| **ARP** | Address Resolution Protocol | Maps IP addresses to MAC addresses on a LAN |
| **DNS** | Domain Name System | Translates hostnames to IP addresses |
| **DHCP** | Dynamic Host Configuration Protocol | Auto-assigns IP addresses to devices on a network |
| **SNMP** | Simple Network Management Protocol | Manages/monitors network devices (ports 161/162) |
| **NTP** | Network Time Protocol | Synchronizes clocks across networked devices (port 123) |
| **SMB** | Server Message Block | Windows file/printer sharing protocol (ports 139, 445) |
| **RDP** | Remote Desktop Protocol | Microsoft remote GUI access protocol (port 3389) |
| **SSH** | Secure Shell | Encrypted remote terminal protocol (port 22) |
| **FTP** | File Transfer Protocol | Transfers files; credentials sent in plaintext (ports 20/21) |
| **HTTP** | HyperText Transfer Protocol | Web protocol; unencrypted (port 80) |
| **HTTPS** | HTTP Secure | HTTP encrypted with TLS (port 443) |
| **TLS** | Transport Layer Security | Cryptographic protocol securing data in transit |
| **VLAN** | Virtual Local Area Network | Logical network segment within a physical network |
| **NAT** | Network Address Translation | Maps private IPs to a public IP for internet access |
| **VPN** | Virtual Private Network | Encrypted tunnel for secure network communication |
| **DMZ** | Demilitarized Zone | Network segment between internal network and the internet |
| **TTL** | Time To Live | Counter limiting how long a packet or DNS record lives |
| **MAC** | Media Access Control | Hardware address of a network interface card |

## Security Concepts & Tools

| Acronym | Full Name | Meaning |
|---|---|---|
| **IOC** | Indicator of Compromise | Artifact suggesting a system has been breached |
| **TTPs** | Tactics, Techniques, and Procedures | Attacker behavior patterns (used in MITRE ATT&CK) |
| **C2** | Command and Control | Infrastructure attackers use to communicate with malware |
| **CVE** | Common Vulnerabilities and Exposures | Public database of known security vulnerabilities |
| **PoC** | Proof of Concept | Working demonstration of an exploit or vulnerability |
| **RCE** | Remote Code Execution | Vulnerability allowing an attacker to run code remotely |
| **LFI** | Local File Inclusion | Web vulnerability allowing reading of server files |
| **RFI** | Remote File Inclusion | Web vulnerability allowing execution of remote files |
| **SQLi** | SQL Injection | Attack injecting malicious SQL into a database query |
| **XSS** | Cross-Site Scripting | Attack injecting malicious scripts into web pages |
| **CSRF** | Cross-Site Request Forgery | Tricks a user's browser into making unintended requests |
| **MFA** | Multi-Factor Authentication | Login requiring two or more verification methods |
| **IDS** | Intrusion Detection System | Monitors network/system for suspicious activity |
| **IPS** | Intrusion Prevention System | Monitors AND blocks suspicious traffic in real time |
| **WAF** | Web Application Firewall | Filters malicious HTTP traffic to web applications |
| **AV** | Antivirus | Software detecting and removing malware |
| **EDR** | Endpoint Detection and Response | Advanced security agent detecting threats on endpoints |
| **SIEM** | Security Information and Event Management | Centralized log collection and correlation platform |
| **SOC** | Security Operations Center | Team monitoring and responding to security incidents |
| **IR** | Incident Response | Structured process for handling a security breach |
| **OSINT** | Open Source Intelligence | Gathering information from publicly available sources |
| **CTF** | Capture The Flag | Security competition involving hacking challenges |
| **APT** | Advanced Persistent Threat | Sophisticated, long-term targeted attack (often nation-state) |
| **DGA** | Domain Generation Algorithm | Malware technique generating random C2 domain names |

## Malware & Binary Analysis

| Acronym | Full Name | Meaning |
|---|---|---|
| **RAT** | Remote Access Trojan | Malware giving an attacker remote control over a system |
| **PE** | Portable Executable | Windows executable file format (.exe, .dll) |
| **DLL** | Dynamic Link Library | Shared code library loaded by Windows processes |
| **API** | Application Programming Interface | Set of functions exposed by an OS or application |
| **IAT** | Import Address Table | PE table listing external functions used by the program |
| **EAT** | Export Address Table | Table listing functions a DLL exposes to other programs |
| **SSDT** | System Service Descriptor Table | Kernel table mapping syscall numbers to handler functions |
| **VAD** | Virtual Address Descriptor | Kernel structure tracking memory regions of a process |
| **PEB** | Process Environment Block | User-mode structure holding process metadata |
| **ASM** | Assembly Language | Low-level programming language close to machine code |

## Cryptography & Hashing

| Acronym | Full Name | Meaning |
|---|---|---|
| **MD5** | Message Digest 5 | 128-bit hash function (not secure for cryptography today) |
| **SHA** | Secure Hash Algorithm | Family of cryptographic hash functions (SHA1, SHA256) |
| **AES** | Advanced Encryption Standard | Symmetric encryption algorithm (128/192/256-bit) |
| **RSA** | Rivest–Shamir–Adleman | Asymmetric encryption used in TLS, SSH, VPN |
| **NSE** | Nmap Scripting Engine | Built-in Lua scripting system for Nmap automation |
| **PCAP** | Packet Capture | File format storing captured network traffic |

---

---

# 2. Active Directory

> 📖 Source: [TryHackMe — Windows AD Basics](https://tryhackme.com/room/winadbasics)

---

### Question 2.1 — Domain Controller (DC)

**What is the role of the Domain Controller (DC) in an Active Directory environment?**

<details>
<summary>💡 Hint</summary>

Think about who manages all users, permissions, and identity verification on the corporate network.
</details>

✅ Full Answer
The **Domain Controller (DC)** is the central server running Active Directory. Its responsibilities:

- **Authentication:** Verifies usernames and passwords against the AD database
- **Authorization:** Determines what each user is allowed to do on the network
- **Policy Enforcement:** Applies Group Policy Objects (GPOs) to all domain-joined machines
- **Replication:** Syncs changes with other DCs in the organization
- **DNS:** Usually also acts as the internal DNS server

**AD Hierarchy:**
```
Forest
└── Domain (example.local)
    ├── Domain Controller (DC)
    ├── Organizational Units (OUs)
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    └── Group Policy Objects (GPOs)
```



---

### Question 2.2 — Security Group vs Distribution Group

**What is the difference between a Security Group and a Distribution Group in AD?**

<details>
<summary>💡 Hint</summary>

One is for permissions, the other is for email.
</details>

✅ Full Answer

| Property | Security Group | Distribution Group |
|---|---|---|
| Primary purpose | Manage permissions and access control | Send email to a group of users |
| Assign file/resource permissions | ✅ Yes | ❌ No |
| Apply to GPOs | ✅ Yes | ❌ No |
| Exchange / email | ✅ Possible | ✅ Main purpose |
| Network security use | ✅ Primary | ❌ Not applicable |

**Example:** `IT-Admins` as a Security Group controls server access. `All-Staff` as a Distribution Group sends newsletters.



---

### Question 2.3 — Kerberos Authentication

**Describe the Kerberos authentication process in AD. What are TGT and TGS?**

<details>
<summary>💡 Hint</summary>

There are 3 actors: the user, the KDC (Key Distribution Center), and the target server.
</details>

✅ Full Answer

**Kerberos — Step by Step:**
```
Step 1: User logs in (AS-REQ) → KDC (= DC)
        KDC returns TGT — Ticket Granting Ticket
        TGT = "master ticket" proving user identity (encrypted)

Step 2: User wants to access a file server (TGS-REQ)
        Sends TGT + request for specific service to KDC
        KDC returns TGS — Ticket Granting Service
        TGS = "access ticket" for that specific server only

Step 3: User presents TGS to the file server → access granted
```

**Security notes:**

| Security Note | Details |
|---|---|
| TGT validity | 10 hours — must be renewed after that |
| Password in transit | Never sent — everything is encrypted |
| Pass-the-Ticket (PTT) | Stealing a TGT from memory to impersonate a user |
| Kerberoasting | Requesting a TGS and cracking the service password offline |

</details>

---

### Question 2.4 — GPO Abuse

**What is a GPO and how can an attacker abuse it?**

<details>
<summary>💡 Hint</summary>

A GPO is a management tool — but for an attacker with write access it is a powerful persistence mechanism.
</details>

✅ Full Answer
A **GPO** (Group Policy Object) is a collection of policy settings applied to Users and Computers in the domain.

**Legitimate uses:** block USB drives, deploy software, enforce password policy, configure Windows Firewall.

**Malicious use:**
```
1. Attacker gains Write permissions on a GPO
2. Creates a malicious Startup script inside the GPO
3. Script downloads a reverse shell payload
4. GPO is applied to all computers in the target OU
→ Every reboot = every computer in that OU executes malicious code

This is Lateral Movement + Persistence in one step.

Tools: BloodHound, PowerView, SharpGPOAbuse
```



---

---

# 3. Network Scanning — Nmap

> 📖 Source: [TryHackMe — Further Nmap](https://tryhackme.com/room/furthernmap)

---

### Question 3.1 — SYN Scan vs TCP Connect Scan

**What is the difference between a SYN Scan and a TCP Connect Scan? When would you use each?**

<details>
<summary>💡 Hint</summary>

One completes the handshake, the other does not. This affects stealth and required privileges.
</details>

✅ Full Answer
**TCP Three-Way Handshake:**
```
Client → SYN       → Target
Client ← SYN / ACK ← Target
Client → ACK       → Target   ← connection established
Client → RST       → Target   ← SYN Scan stops HERE (never completes)
```

| Property | SYN Scan (`-sS`) | TCP Connect Scan (`-sT`) |
|---|---|---|
| Handshake | Half — sends RST before completing | Full — completes 3-way handshake |
| Requires privileges | Root / Admin | None required |
| Logged by target | Low (stealthier) | Higher (full connection logged) |
| Speed | Faster | Slower |
| Default when | Running with `sudo` | Running without `sudo` |

**Conclusion:**
- **SYN Scan** — always prefer when you have privileges. Less likely to be detected.
- **TCP Connect** — use when you don't have root, or when SYN scan is blocked.



---

### Question 3.2 — Nmap Flags

**Explain these Nmap flags: `-sV`, `-O`, `-A`, `-p-`, `--script`, `-T4`**

<details>
<summary>💡 Hint</summary>

Each flag adds a different layer of information about the target.
</details>

✅ Full Answer

| Flag | Name | Function |
|---|---|---|
| `-sV` | Version Detection | Identifies service versions on open ports (e.g. Apache 2.4.41) |
| `-O` | OS Detection | Fingerprints the target operating system |
| `-A` | Aggressive Scan | Combines: `-sV -O --script=default --traceroute` |
| `-p-` | All Ports | Scans all 65,535 ports (default scans only top 1,000) |
| `--script` | NSE Scripts | Runs Nmap Scripting Engine scripts (e.g. `--script=vuln`) |
| `-T4` | Timing Template | Speed: T0=slowest → T3=default → T4=fast → T5=aggressive |

**Comprehensive scan example:**
```bash
nmap -A -sV -O -p- -T4 --script=vuln 10.10.10.10
```



---

### Question 3.3 — UDP Scan

**What is the difference between UDP and TCP scanning? Why is UDP harder to scan?**

<details>
<summary>💡 Hint</summary>

UDP is connectionless — there is no acknowledgment message confirming receipt.
</details>

✅ Full Answer

```
TCP: Send → Receive → Acknowledge. Open port = clear response.

UDP: Send → Receive → No obligation to reply.
     Closed port → ICMP "Port Unreachable"
     Open port   → Silence (or app-specific response)
```

**Why UDP is difficult:**
- No ACK — must wait for timeout on each port
- UDP scan is ~10x slower than TCP
- Result `open|filtered` — cannot confirm if truly open
- Firewalls often block ICMP → results become unreliable

**Important UDP ports:**

| Port | Service | Why It Matters |
|---|---|---|
| 53 | DNS | Can reveal internal hostnames |
| 67/68 | DHCP | May be misconfigured |
| 161/162 | SNMP | Often leaks device information |
| 123 | NTP | NTP amplification DDoS vector |

```bash
sudo nmap -sU -p 53,67,68,69,123,161 10.10.10.10
```



---

### Question 3.4 — NSE Scripts for SMB

**Demonstrate how to use NSE (Nmap Scripting Engine) to identify SMB vulnerabilities.**

<details>
<summary>💡 Hint</summary>

NSE has categories: `vuln`, `exploit`, `safe`, `auth`, `discovery`.
</details>

✅ Full Answer

```bash
# General SMB vulnerability scan
nmap -p 445,139 --script=smb-vuln* 10.10.10.10

# EternalBlue (MS17-010) — exploited by WannaCry and NotPetya
nmap -p 445 --script=smb-vuln-ms17-010 10.10.10.10

# OS discovery via SMB
nmap -p 445 --script=smb-os-discovery,smb-security-mode 10.10.10.10

# All vulnerability category scripts
nmap --script=vuln -p 445 10.10.10.10
```

**Positive EternalBlue result:**
```
| smb-vuln-ms17-010:
|   VULNERABLE:
|   Remote Code Execution in Microsoft SMBv1
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
→ Machine is vulnerable! WannaCry and NotPetya both exploited this.
```



---

---

# 4. Traffic Analysis — Wireshark + Hydra

> 📖 Source: [TryHackMe — H4cked](https://tryhackme.com/room/h4cked)

---

### Question 4.1 — Identifying Brute Force in a PCAP

**In a PCAP file captured during an attack, how do you identify a brute-force attempt on FTP?**

<details>
<summary>💡 Hint</summary>

Look for repetition — many login attempts in a short time from the same IP address.
</details>

✅ Full Answer
**In Wireshark — Step by Step:**

**Step 1:** Filter all FTP traffic
```
ftp
```

**Step 2:** Show only failed logins
```
ftp.response.code == 530
```
→ Code 530 = "Login incorrect"

**Step 3:** Go to Statistics → Conversations → TCP
- Look for one IP with hundreds of connections to port 21

**Step 4:** Right-click a connection → Follow TCP Stream

**Brute-Force pattern in the stream:**
```
USER admin → PASS admin123     → 530 Login incorrect
USER admin → PASS password     → 530 Login incorrect
USER admin → PASS 123456       → 530 Login incorrect
...  (repeats hundreds of times — automated tool!)
USER admin → PASS letmein      → 230 Login successful  ← BREACH!
```

**Useful filter to find the successful login:**
```
ip.src == 10.10.10.X && ftp.response.code == 230
```



---

### Question 4.2 — Analysing FTP Data Channel

**After the attacker logs into FTP, how do you identify what files were uploaded or downloaded?**

<details>
<summary>💡 Hint</summary>

FTP uses two channels: port 21 for commands, port 20 for actual data. Look for RETR and STOR commands.
</details>

✅ Full Answer

**FTP — Two Channels:**
```
Port 21 = Control Channel  (commands: USER, PASS, RETR, STOR)
Port 20 = Data Channel     (actual file data)
```

**In Wireshark:**

**Step 1:** Filter for commands
```
ftp.request.command == "RETR"   ← file downloaded FROM the server
ftp.request.command == "STOR"   ← file uploaded TO the server
```

**Step 2:** Right-click → Follow TCP Stream on the connection

**Step 3:** Extract all transferred files:
```
File → Export Objects → FTP-DATA
```

**What to look for:**
- `.php`, `.py`, `.sh` files **uploaded** → Web Shell / Backdoor
- `/etc/passwd`, `/etc/shadow` **downloaded** → credential theft
- Config files downloaded → sensitive information leaked



---

### Question 4.3 — Identifying a Reverse Shell

**What is a Reverse Shell and how do you identify one in Wireshark?**

<details>
<summary>💡 Hint</summary>

In a Reverse Shell, the connection goes FROM the victim TO the attacker — bypassing inbound firewall rules.
</details>

✅ Full Answer

**Bind Shell vs Reverse Shell:**
```
Bind Shell:    Attacker → [connects to] → Victim
               Blocked by firewall (inbound rules)

Reverse Shell: Attacker ← [connects to] ← Victim
               Allowed by firewall (outbound rules usually open)
```

**Detection in Wireshark:**

**Step 1:** Filter for unusual ports
```
tcp.port == 4444 || tcp.port == 1234 || tcp.port == 9001
```

**Step 2:** Look for a long-lived TCP connection from the server to an external IP

**Step 3:** Follow TCP Stream — you will see:
```
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
whoami
www-data
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
→ Active reverse shell confirmed!
```

**Signs to look for:**
- Long TCP connection from server to external IP
- Non-standard destination port (4444, 1337, 9001)
- Linux commands appearing as responses in the stream

</details>

---

---

# 5. Log Analysis

> 📖 Source: [TryHackMe — Intro to Logs](https://tryhackme.com/room/introtologs)

---

### Question 5.1 — Windows Event Logs

**What are the main types of Windows event logs and where are they located?**

<details>
<summary>💡 Hint</summary>

Event Viewer — three main channels, each recording different types of events.
</details>

✅ Full Answer

**File location:** `C:\Windows\System32\winevt\Logs\`

**The three main logs:**

| Log Name | File | What It Records |
|---|---|---|
| **System** | System.evtx | OS events: driver errors, service crashes, hardware failures |
| **Application** | Application.evtx | App errors, installation events, crashes |
| **Security** | Security.evtx | ⭐ Most important: logins, policy changes, privilege use |

**Critical Security Event IDs:**

| Event ID | Meaning | Why It Matters |
|---|---|---|
| 4624 | Successful logon | Normal — but investigate unusual hours or sources |
| 4625 | ⚠️ Failed logon | Many in short time = Brute Force attack |
| 4648 | Logon with explicit credentials | May indicate lateral movement |
| 4688 | ⚠️ New process created | Find malicious child processes (e.g. `cmd.exe` from `Word.exe`) |
| 4698 | 📌 Scheduled Task created | Common persistence technique |
| 7045 | 📌 New service installed | Another common persistence / backdoor method |
| 4720 | New user account created | Attacker may be adding a backdoor account |
| 4732 | User added to Admins group | Privilege escalation indicator |

</details>

---

### Question 5.2 — Analysing an SSH Log

**Analyse the following SSH log and identify what happened:**

```
Jan 15 03:22:11 sshd[1234]: Failed password for root from 192.168.1.105 port 52341 ssh2
Jan 15 03:22:13 sshd[1235]: Failed password for root from 192.168.1.105 port 52342 ssh2
Jan 15 03:22:14 sshd[1236]: Failed password for root from 192.168.1.105 port 52343 ssh2
Jan 15 03:22:16 sshd[1240]: Failed password for admin from 192.168.1.105 port 52350 ssh2
Jan 15 03:22:18 sshd[1245]: Accepted password for jenny from 192.168.1.105 port 52355 ssh2
Jan 15 03:22:19 sshd[1245]: session opened for user jenny
Jan 15 03:24:33 sudo: jenny : USER=root ; COMMAND=/bin/bash
```

<details>
<summary>💡 Hint</summary>

Pay attention to the frequency, the usernames tried, and what happened immediately after the successful login.
</details>

✅ Full Analysis
**Timeline of what happened:**
```
03:22:11 – 03:22:16  →  SSH Brute Force
  ├── 4 failed attempts within ~5 seconds
  ├── Source IP: 192.168.1.105
  ├── Tried usernames: root, admin
  └── 1 new connection per second = automated tool (Hydra!)

03:22:18  →  BREACH! Credentials found: user=jenny

03:22:19  →  Session opened for jenny

03:24:33  →  PRIVILEGE ESCALATION
              jenny ran: sudo /bin/bash
              → jenny now has a root shell
```

**Conclusions:**
1. SSH Brute Force attack from `192.168.1.105`
2. Credential found: `user=jenny` (weak password in a wordlist)
3. Privilege Escalation: `jenny` → `root` via sudo
4. `jenny` can run `/bin/bash` as root — **this is a misconfiguration!**

**Incident Response steps:**
- Block `192.168.1.105` immediately at the firewall
- Lock jenny's account and reset credentials
- Audit `/etc/sudoers` — why can jenny run `/bin/bash` as root?
- Investigate what root did after `03:24:33`

</details>

---

### Question 5.3 — Log Levels

**What is a log level? Explain the difference between DEBUG, INFO, WARNING, ERROR, and CRITICAL.**

✅ Full Answer

| Level | When Used | Example |
|---|---|---|
| **DEBUG** | Development only — very verbose | "Entered function calculate_hash() with arg=abc" |
| **INFO** | Normal operational events | "User john logged in successfully from 10.0.0.5" |
| **WARNING** | Unusual but not critical | "Login failed — attempt 2 of 3 for user admin" |
| **ERROR** | An operation failed | "Database connection timed out after 30 seconds" |
| **CRITICAL** | System-level failure or emergency | "Out of disk space — service shutting down" |

**For SOC work:** focus on WARNING (suspicious attempts), ERROR (exploitable failures), and CRITICAL (active incidents).

</details>

---

### Question 5.4 — Grep for Attack IPs

**Write a `grep` command that filters failed SSH attempts from `auth.log` and shows only the attacker IPs.**

<details>
<summary>💡 Hint</summary>

Use `grep` to find the lines, then `awk` to extract the IP field.
</details>

✅ Full Answer

```bash
# All failed SSH lines
grep "Failed password" /var/log/auth.log

# Extract only the IP address field
grep "Failed password" /var/log/auth.log | awk '{print $11}'

# Top attackers — sorted by frequency
grep "Failed password" /var/log/auth.log \
  | awk '{print $11}' \
  | sort | uniq -c | sort -rn | head -10

# Using regex for precise IP extraction
grep "sshd.*Failed" /var/log/auth.log \
  | grep -oP '(?<=from )\d+\.\d+\.\d+\.\d+' \
  | sort | uniq -c | sort -rn
```

**Sample output:**
```
    347  192.168.1.105   ← clear attack — block this IP!
     89  10.0.0.33
     12  172.16.1.8
```


---

---

# 6. Password Cracking — Hydra

> 📖 Source: [TryHackMe — Hydra](https://tryhackme.com/room/hydra)

---

### Question 6.1 — Dictionary Attack vs Brute Force

**What is the difference between a Dictionary Attack and a Brute Force Attack?**

<details>
<summary>💡 Hint</summary>

One tests a prepared list, the other tries every possible combination.
</details>

✅ Full Answer

| Type | Method | Speed | Effectiveness |
|---|---|---|---|
| **Dictionary Attack** | Tries words from a prepared wordlist | Fast | High for common passwords |
| **Brute Force** | Tries every possible combination | Very slow | 100% — but may take centuries |
| **Rule-based** | Wordlist + mutation rules (`P@ssw0rd`) | Medium | Very high |
| **Rainbow Table** | Looks up pre-computed hash values | Very fast | High (fails if salted) |

**Popular wordlists:**
- `/usr/share/wordlists/rockyou.txt` — 14 million real-world leaked passwords
- `/usr/share/wordlists/SecLists/` — large curated collection

**Brute force time calculation:**
```
8-character password, lowercase only:
26^8 = 208,827,064,576 combinations
At 1,000 attempts/second ≈ 6.6 years

But with a modern GPU: billions of hashes/second ← seconds!
→ This is why strong, long passwords matter.
```

</details>

---

### Question 6.2 — Hydra Syntax

**Explain the Hydra syntax. Give examples for SSH, FTP, and an HTTP-POST login form.**

<details>
<summary>💡 Hint</summary>

General structure: `hydra -l USER -P WORDLIST TARGET PROTOCOL`
</details>

✅ Full Answer

**Key flags:**

| Flag | Meaning |
|---|---|
| `-l username` | Single known username |
| `-L users.txt` | File containing list of usernames |
| `-p password` | Single password to test |
| `-P rockyou.txt` | Wordlist file of passwords |
| `-t 16` | Number of parallel threads |
| `-V` | Verbose — prints every attempt |
| `-f` | Stop on first successful login |
| `-s PORT` | Non-standard port |

---

**Example 1: SSH**
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt \
      10.10.10.10 ssh -t 4 -V
```

**Example 2: FTP**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
      10.10.10.10 ftp -V
```

**Example 3: HTTP POST Login Form**
```bash
hydra -l admin -P rockyou.txt \
      10.10.10.10 \
      http-post-form \
      "/login:username=^USER^&password=^PASS^:F=Invalid credentials" \
      -V
```

**HTTP-POST breakdown:**
```
"/login"                     ← path of the login form
"username=^USER^&..."        ← form parameters
"^USER^" and "^PASS^"        ← placeholders Hydra replaces each attempt
"F=Invalid credentials"      ← the failure string (F=fail)
                               Hydra looks for this to detect wrong passwords
```

**Example 4: HTTP Basic Auth**
```bash
hydra -l admin -P rockyou.txt \
      10.10.10.10 http-get /admin/ -V
```

</details>

---

### Question 6.3 — Defense in Depth

**In the H4cked scenario, the attacker brute-forced FTP and then launched a reverse shell. How could each stage have been stopped?**

<details>
<summary>💡 Hint</summary>

Think about Defense in Depth — multiple independent layers of protection.
</details>

✅ Full Answer

**Attack stages + prevention points:**

```
Stage 1: FTP Brute Force
  ✓ Fail2Ban — block IP after N failed attempts
  ✓ Rate limiting on FTP connections
  ✓ Firewall rule: block FTP access from external networks
  ✓ Use SSH keys instead of passwords

Stage 2: Successful FTP Login
  ✓ Strong password (not in rockyou.txt wordlist)
  ✓ MFA / 2FA — second factor stops credential reuse
  ✓ IP allowlist on FTP service

Stage 3: Web Shell Uploaded via FTP
  ✓ FTP user should not have write access to web root
  ✓ PHP execution disabled in upload directories
  ✓ File Integrity Monitoring (Tripwire, AIDE)
  ✓ Web server runs with minimal OS permissions

Stage 4: Reverse Shell Callback
  ✓ Egress firewall filtering — block outbound TCP to unusual ports
  ✓ AppArmor / SELinux — restrict what the PHP process can execute
  ✓ IDS/IPS — detect reverse shell command patterns in traffic
```

</details>

---

---

# 7. Malware Research

> 📖 Sources: [TryHackMe — MalResearching](https://tryhackme.com/room/malresearching) + [MAL: Introductory](https://tryhackme.com/room/malmalintroductory)

---

### Question 7.1 — Static vs Dynamic Analysis

**What is the difference between Static Analysis and Dynamic Analysis of malware?**

<details>
<summary>💡 Hint</summary>

One examines the code without running it, the other executes it in isolation and observes what happens.
</details>

✅ Full Answer

| Criterion | Static Analysis | Dynamic Analysis |
|---|---|---|
| Definition | Analyse the file without executing it | Execute in a controlled, isolated environment |
| Risk level | ✅ Zero — never runs | ⚠️ High — requires sandboxed VM |
| What you find | Structure, strings, imports, PE headers | Live behaviour: C2, files created, network calls |
| Main tools | `strings`, `file`, `xxd`, PE Explorer, IDA Pro | Procmon, Wireshark, RegShot, Cuckoo Sandbox |
| Bypasses obfuscation | ❌ Difficult | ✅ See real behaviour regardless of packing |
| Speed | ✅ Fast | ⏱ Takes more time |
| Anti-VM detection | ✅ Not relevant | ❌ Malware may detect sandbox and stay dormant |

> **Recommended approach:** Run Static first to understand structure. Then Dynamic to confirm real-world behaviour.



---

### Question 7.2 — The `strings` Command

**What can you find using the `strings` command on a malware file? Give examples.**

✅ Full Answer

```bash
strings malware.exe | less
strings -n 6 malware.exe    # minimum 6-character strings
strings -a malware.exe      # scan all binary sections

# Targeted search
strings malware.exe | grep -iE "(http|ftp|\.exe|\.dll|HKEY|cmd|powershell)"
```

**What you can find:**

```
# C2 URLs and IPs
http://evil-c2.com/gate.php
192.168.100.5:4444

# Registry persistence keys
SOFTWARE\Microsoft\Windows\CurrentVersion\Run

# Shell commands
cmd.exe /c net user hacker P@ss /add
net localgroup administrators hacker /add

# Suspicious filenames
svchost32.exe
C:\Users\Public\payload.dll

# Forgotten debug messages (developer mistakes)
"Error connecting to C2"
"File encryption complete"

# Mutex names (prevent double execution)
Global\MUTEX_MALWARE_INSTANCE_1
```



---

### Question 7.3 — Malware Types

**Explain the following malware types: Trojan, Worm, Ransomware, RAT, Rootkit. What distinguishes each?**

✅ Full Answer

| Type | Description | Propagation | Goal |
|---|---|---|---|
| **Trojan** | Disguises itself as legitimate software | Manual — user executes it | Open a backdoor |
| **Worm** | Spreads autonomously across networks | Self-replicating via exploits | Mass infection |
| **Ransomware** | Encrypts files, demands payment | Various — email, exploit kits | Financial gain 💰 |
| **RAT** | Remote Access Trojan — full remote control | Usually via a Trojan | Espionage, data theft |
| **Rootkit** | Hides itself at the kernel/OS level | Bundled with other malware | Persistence and stealth |
| **Keylogger** | Records all keystrokes | Trojan / RAT | Steal passwords |
| **Spyware** | Monitors and exfiltrates user activity | Drive-by / Trojan | Data collection |
| **Botnet** | Network of infected machines | Worm / Trojan | DDoS, spam, crypto mining |

**Well-known RATs:** Poison Ivy, njRAT (common in the Middle East), AsyncRAT, Cobalt Strike (commercial, often cracked/leaked).



---

### Question 7.4 — IOC (Indicators of Compromise)

**What are IOCs? Give examples from each category.**

✅ Full Answer
**IOCs** are artifacts that indicate a system has been compromised:

**Category 1: Network**
```
- IP addresses:   45.33.32.156  (C2 server)
- Domains:        evil-c2.net, d4b2k.xyz  (DGA domain)
- URLs:           http://evil.com/payload.exe
- User-Agent:     Mozilla/5.0 (not-a-real-browser)
- Ports:          TCP connections to port 4444
```

**Category 2: Files**
```
- MD5:   d41d8cd98f00b204e9800998ecf8427e
- SHA256: 3b4c5d...
- Names:  svchost32.exe, winlogon64.exe, update.ps1
- Paths:  C:\Temp\, C:\Users\Public\, %APPDATA%\
```

**Category 3: System**
```
- Registry: HKCU\...\Run\UpdateService = "malware.exe"
- Scheduled Task: "Windows Update Helper"
- New Service:    "WindowsUpdateSvc" pointing to C:\Temp\
- Mutex:          Global\MUTEX_MALWARE_123
- Process:        chrome.exe with parent = malware.exe
```

**Category 4: Logs**
```
- Event 4625 × 500 in 5 minutes = Brute Force
- Event 7045 = new service installed
- Successful login immediately after many failures
```

**Tools to look up IOCs:**
- **VirusTotal** — hash / URL / IP against 70+ AV engines
- **AbuseIPDB** — IP reputation and abuse reports
- **Shodan** — information about internet-facing IPs
- **MISP** — open-source threat intelligence sharing platform



---

### Question 7.5 — Persistence Techniques on Windows

**What are the most common persistence mechanisms malware uses on Windows?**

<details>
<summary>💡 Hint</summary>

Persistence = surviving a reboot. The malware must register itself to run automatically on startup.
</details>


✅ Full Answer

**1. Registry Run Keys**
```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
→ Every value here runs automatically on every login / boot
```

**2. Scheduled Tasks**
```bash
schtasks /create /tn "WindowsUpdate" /tr "C:\temp\malware.exe" /sc onlogon
```

**3. Services**
```bash
sc create "UpdateHelper" binpath="C:\temp\malware.exe" start=auto
```

**4. Startup Folder**
```
C:\Users\<User>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\
```

**5. DLL Hijacking**
```
Place a malicious DLL in a path an application searches first.
When the application launches, it loads the malicious DLL instead of the real one.
```

**6. WMI Event Subscriptions**
```
Advanced — very hard to detect even with Autoruns.
WMI runs a script on a trigger (e.g., every 5 minutes, on login).
```

> **Detection tool:** `Autoruns` by Sysinternals — shows everything configured to run at startup, colour-coded by verification status.



---

### Question 7.6 — Sandbox Analysis

**What is Sandbox Analysis? Name common tools and explain how to read a sandbox report.**


✅ Full Answer

A **sandbox** is an isolated virtual environment that executes malware safely and records all its actions.

**Common tools:**

| Tool | URL | Strengths |
|---|---|---|
| **Any.run** | any.run | Interactive in real time — click around during analysis |
| **VirusTotal** | virustotal.com | 70+ AV engines, community comments, YARA rules |
| **Hybrid Analysis** | hybrid-analysis.com | CrowdStrike Falcon-powered, free tier available |
| **Cuckoo Sandbox** | cuckoosandbox.org | Open source, self-hosted, fully customisable |
| **Joe Sandbox** | joesandbox.com | Most detailed reports, MITRE ATT&CK mapping |

**Reading a sandbox report — what to look for:**

```
1. Network Activity
   DNS Queries    → What domains did it contact?
   HTTP Requests  → Full URLs and response codes
   TCP Connects   → IPs and ports (C2 server?)

2. File System Changes
   Files Created  → Where did it copy itself? (persistence)
   Files Modified → Did it tamper with system files?
   Files Deleted  → Covering its tracks?

3. Registry Changes
   Run keys added? → Persistence confirmed

4. Process Activity
   Child processes: cmd.exe / powershell.exe spawned?
   Process injection: CreateRemoteThread into explorer.exe?

5. Red Flags to investigate immediately:
   ⚠️  powershell.exe -EncodedCommand  (obfuscated payload)
   ⚠️  cmd.exe /c "net user hacker /add"
   ⚠️  CreateRemoteThread → system process
   ⚠️  DNS queries to random-looking DGA domains
```



---

---

# 8. Scenario Questions

> These questions combine multiple topics — just like the real exam.

---

### Scenario A — Compromised Web Server

```
Alert received: company web server responding very slowly.
Initial check: unusual outbound traffic detected from the server.
Available: Wireshark PCAP, auth.log, SSH access to the server.
```

**Describe your investigation process — where do you start and what do you check?**

✅ Full Investigation Process

```
Step 1: Wireshark — What is leaving the network?
──────────────────────────────────────────────────
□ Filter: ip.src == [SERVER_IP]
□ Statistics → Conversations → Who is the Top Talker?
□ Look for TCP connections to unusual ports: 4444, 1337, 9001
□ Follow TCP Stream on any suspicious connections
□ File → Export Objects → HTTP → review all transferred files

Step 2: auth.log — Who logged in?
──────────────────────────────────
□ grep "Accepted" /var/log/auth.log | tail -50
□ Identify unfamiliar source IP addresses
□ Check for logins at unusual hours (3:00 AM?)
□ grep "sudo" /var/log/auth.log — who escalated privileges?

Step 3: Live Server Investigation
──────────────────────────────────
□ ps aux               ← list active processes
□ netstat -antp        ← show all open connections
□ ls -la /tmp/         ← check temporary files
□ find / -mtime -1     ← files created in last 24 hours
□ crontab -l           ← scheduled tasks (persistence?)
□ cat /etc/passwd      ← any new user accounts?

Step 4: Build a Timeline
──────────────────────────
□ When was the PHP file created?
□ Did the FTP login happen at the same time?
□ What happened in the next 5 minutes after the login?
□ Connect the dots into a coherent attack story.
```



---

### Scenario B — Malware on a Workstation

```
Employee reports: computer running very slowly, AV sent an alert.
Filename: WindowsUpdate_KB4578291.exe
Location: C:\Users\john\Downloads\
```

**Describe both a Static and a Dynamic analysis of this file.**

✅ Full Analysis

**Static Analysis:**
```bash
# Step 1: Hash the file
md5sum WindowsUpdate_KB4578291.exe
sha256sum WindowsUpdate_KB4578291.exe
→ Search hash on VirusTotal immediately!

# Step 2: Identify file type
file WindowsUpdate_KB4578291.exe
→ PE32 executable (GUI) Intel 80386

# Step 3: Extract strings
strings WindowsUpdate_KB4578291.exe | grep -iE "(http|cmd|HKEY|powershell)"

# Step 4: PE header inspection — look for suspicious imports:
   CreateRemoteThread + WriteProcessMemory → code injection
   VirtualAllocEx → allocating memory in another process
   CryptEncrypt → ransomware?
```

**Red flags found (example):**
```
Strings reveals:
- "http://update-ms.info/check"  ← fake Microsoft domain
- "cmd.exe /c net user admin2 P@ss /add"
- "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"

VirusTotal: 45/70 detections
```

**Dynamic Analysis (Sandbox — Any.run):**
```
Observed behaviour:

Network:  DNS query → update-ms.info  (fake Microsoft domain!)
          TCP connect 185.x.x.x:443   (C2 callback)

Files:    C:\Windows\Temp\svc32.exe created
          Files in C:\Users\john\Documents\ are now encrypted!

Registry: HKCU\...\Run\WinUpdateSvc = "C:\Temp\svc32.exe"

Processes: cmd.exe → net user admin2 P@ss123 /add
           net localgroup administrators admin2 /add

Verdict: Backdoor + Ransomware + Persistence + Hidden Admin account
```

**Immediate Incident Response:**
1. Isolate the machine from the network (do NOT power off — preserve memory!)
2. Acquire a memory image using DumpIt
3. Check if user `admin2` was created on other machines in the domain
4. Search DNS logs — which other hosts contacted `update-ms.info`?
5. Identify all encrypted files and check for Volume Shadow Copies



---

---

# 9. Quick Reference Cheat Sheet

```bash
# ─── NMAP ──────────────────────────────────────────────────────────
nmap -sV -O -A -p- -T4 TARGET                    # Full comprehensive scan
nmap -sS TARGET                                   # SYN Scan (stealth)
nmap -sU -p 53,161 TARGET                         # UDP scan
nmap --script=vuln TARGET                         # Vulnerability scan
nmap -p 445 --script=smb-vuln-ms17-010 TARGET     # EternalBlue check

# ─── HYDRA ─────────────────────────────────────────────────────────
hydra -l admin -P rockyou.txt TARGET ssh
hydra -l admin -P rockyou.txt TARGET ftp
hydra -l admin -P rockyou.txt TARGET \
  http-post-form "/login:user=^USER^&pass=^PASS^:F=Wrong" -V

# ─── WIRESHARK FILTERS ─────────────────────────────────────────────
ftp.response.code == 530        # FTP login failure
ftp.response.code == 230        # FTP login success
tcp.port == 4444                # Common reverse shell port
http.request.method == "POST"   # POST requests (login forms)
ip.src == X.X.X.X && tcp        # All TCP from specific source IP

# ─── LOG ANALYSIS ──────────────────────────────────────────────────
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn
grep "Accepted" /var/log/auth.log              # Successful logins
grep "sudo" /var/log/auth.log                  # Privilege escalation

# ─── MALWARE STATIC ANALYSIS ───────────────────────────────────────
strings malware.exe | grep -iE "(http|cmd|HKEY|powershell)"
file malware.exe                                # Identify file type
md5sum malware.exe && sha256sum malware.exe     # Get hashes for VirusTotal
```

---

## Summary Table

| Topic | Key Concepts | Primary Tool |
|---|---|---|
| Active Directory | DC, GPO, Kerberos TGT/TGS, Security Groups, DKOM | PowerShell, BloodHound |
| Network Scanning | SYN/TCP/UDP scans, NSE scripts, all major flags | Nmap |
| Traffic Analysis | Display filters, Follow TCP Stream, FTP data extraction | Wireshark |
| Log Analysis | Windows Event IDs, grep + awk, auth.log patterns | grep, awk, Event Viewer |
| Password Cracking | Dictionary vs Brute Force, HTTP-POST form syntax | Hydra |
| Malware Research | Static vs Dynamic, IOC categories, Sandbox reports, Persistence | strings, VirusTotal, Any.run |

---

> 💪 **Good luck on the exam!**
> The tools are in front of you — the question is knowing how to use them correctly.

---
*Practical Exam Simulator — Cybersecurity Study Edition*
*Sources: TryHackMe — AD Basics · Further Nmap · H4cked · Intro to Logs · Hydra · MalResearching · MAL Introductory*

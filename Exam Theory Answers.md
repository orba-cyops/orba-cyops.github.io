# Theoretical Exam — Study Guide & Answers
> Closed-book | English answers | Short & precise

---

## 1. Linux Basic Commands

| Command | What It Does |
|---------|-------------|
| `ls` | Lists files and folders in the current directory |
| `cd` | Changes the current directory |
| `pwd` | Prints the full path of the current working directory |
| `mkdir` | Creates a new directory |
| `rm` | Deletes files or directories |
| `cp` | Copies files or directories |
| `mv` | Moves or renames files/directories |
| `whoami` | Displays the currently logged-in username |
| `chmod` | Changes file/directory permissions |
| `chown` | Changes ownership of a file/directory |
| `ifconfig` / `ip` | Displays network interface and IP address information |
| `grep` | Searches for a text pattern inside files |
| `tar` | Compresses or extracts archive files |
| `man` | Displays the manual/help page for a command |

### What Is a Linux Distribution?
A **Linux distribution (distro)** is a version of the Linux OS that packages the Linux kernel together with a package manager, system tools, and pre-installed software, tailored for a specific use case.

| Distro | Best Known For |
|--------|---------------|
| **Ubuntu** | Beginner-friendly, desktop and servers, based on Debian |
| **Debian** | Extremely stable, suited for servers and advanced users |
| **Fedora** | Cutting-edge features, backed by Red Hat |
| **CentOS / AlmaLinux** | Enterprise server use, RHEL-based |
| **Arch Linux** | Lightweight, highly customizable, rolling release model |
| **Kali Linux** | Penetration testing and information security |
| **Manjaro** | Arch-based but more beginner-friendly |

---

## 2. Recon (Reconnaissance)

**Recon** — The process of gathering intelligence about a target before an attack.

### Passive vs. Active

| Type | Definition | Examples |
|------|-----------|---------|
| **Passive** | Collecting info without directly interacting with the target | OSINT, Google Hacking, Whois, LinkedIn, company website |
| **Active** | Collecting info through direct interaction with the target | Port scanning (Nmap), phishing, social engineering |

### Passive Recon Sources
- Company website (career page, About Us)
- Search engines — **Google Hacking** (advanced operators)
- **Whois** — domain registration data
- Social networks (LinkedIn, Twitter)
- **Shodan** — search engine for internet-connected devices

### Active Recon Sources
- **Nmap** — port scanning and service enumeration
- Phishing / impersonation emails
- Social engineering (direct questioning while impersonating someone)
- DoS/DDoS probing — reveals available resources and defenses

---

## 3. Port Scanner — Nmap

**Nmap** — A network exploration and security auditing tool used to discover hosts, open ports, running services, and OS information on a network.

### Key Nmap Functions

| Flag | Description |
|------|-------------|
| (default) | Host discovery and port scanning |
| `-sV` | Detects service name and **version** on open ports |
| `-sC` | Runs default NSE scripts for deeper checks |
| `-O` | OS fingerprinting |
| `-A` | Aggressive scan — OS, version, scripts, traceroute |

### How to Get a Service Version?
```
nmap -sV <target_ip>
```
The `-sV` flag probes open ports to identify the service name and exact version running on them.

---

## 4. Exploit

**Exploit** — Code or a technique that takes advantage of a security vulnerability to gain unauthorized access or cause unintended behavior.

### Key Terms

| Term | Definition |
|------|-----------|
| **Payload** | The part of the exploit code that performs the actual action on the target (open a shell, create a backdoor, escalate privileges) |
| **Metasploit** | An exploitation framework with two main components: msfvenom and msfconsole |
| **msfvenom** | Metasploit tool used to generate malicious payloads/files |
| **msfconsole** | Metasploit's interactive control interface — launches exploits and listens for incoming connections |
| **Meterpreter** | An advanced payload that provides a remote shell on the target, commonly used for post-exploitation and privilege escalation |

---

## 5. Password Cracking

**Password Cracking** — Recovering passwords from stored hashes or by attacking live authentication services.

### Tools

| Tool | How It Works |
|------|-------------|
| **Hydra** | Online brute-force — tries credentials against a live service (SSH, FTP, HTTP login, etc.) |
| **Hashcat** | Offline hash cracker — hashes wordlist entries and compares them against captured password hashes |

### Attack Types

| Attack | Definition |
|--------|-----------|
| **Dictionary Attack** | Uses a pre-built wordlist of common passwords; varies by culture, region, and context |
| **Brute Force** | Tries every possible combination within a defined character set and length — exhaustive but slow |

---

## 6. Web Application Security

### SQL
**SQL (Structured Query Language)** — A programming language used to manage and query relational databases.

### SQL Injection
**SQL Injection** — Inserting malicious SQL code into an input field where only data is expected, manipulating the database query to leak, modify, or delete data.

### XSS (Cross-Site Scripting)

| Question | Answer |
|----------|--------|
| **What is XSS?** | Injecting malicious JavaScript code into a web page |
| **Language** | JavaScript |
| **Where does it occur?** | Inside the web application (input fields, URL parameters, comment boxes) |
| **Who is affected?** | Other users visiting the compromised page — their browser executes the injected script |

---

## 7. Malware Analysis

**Malware** — Malicious software designed to damage, disrupt, or gain unauthorized access (e.g., viruses, worms, ransomware, keyloggers).

### Static vs. Dynamic Analysis

| | **Static Analysis** | **Dynamic Analysis** |
|--|-------------------|---------------------|
| **Definition** | Examining the file's contents **without executing it** | Observing system behavior **while the file runs** |
| **Risk** | No risk — file never executes | Requires isolated environment (sandbox / VM) |
| **What you find** | Strings, imports, hashes, file structure, compiler info | Registry changes, new processes, network connections, file modifications |

### Tools

| Phase | Tool | Purpose |
|-------|------|---------|
| **Static** | **ExeInfo PE** | Shows file info: packer, compiler, architecture |
| **Static** | **HxD** | Hex editor — view and edit raw file bytes |
| **Dynamic** | **Autoruns** | Lists all programs configured to run automatically at startup |
| **Dynamic** | **Procmon** (Process Monitor) | Real-time monitoring of processes, file system, and registry activity |
| **Dynamic** | **Regshot** | Takes registry snapshots before and after execution and compares the differences |

---

## 8. Network Attacks

| Attack | Definition |
|--------|-----------|
| **DDoS** (Distributed Denial of Service) | Flooding a target with traffic from many devices simultaneously, overwhelming it and denying service to legitimate users |
| **MITM** (Man-in-the-Middle) | Attacker secretly intercepts and relays communication between two parties — each believes they are communicating directly with the other |
| **ARP Spoofing** | Sending fake ARP messages on a local network to map the attacker's MAC address to a legitimate IP, redirecting traffic through the attacker — commonly used to enable a MITM attack |

---

## Quick-Reference Cheat Sheet

| Topic | Key One-Liner |
|-------|--------------|
| `grep` | Search for text patterns inside files |
| `chmod` | Change file permissions |
| Passive Recon | Gather info with no direct contact with the target |
| Active Recon | Gather info through direct interaction (scanning, probing) |
| `nmap -sV` | Detect service name and version on open ports |
| Payload | The action-performing part of an exploit |
| Meterpreter | Post-exploitation shell enabling privilege escalation |
| Hydra | Online brute-force against live login services |
| Hashcat | Offline cracking by comparing hashed wordlist to captured hashes |
| SQL Injection | Malicious SQL in input fields to manipulate the database |
| XSS | Malicious JavaScript injected into a site, affecting other users |
| Static Analysis | Inspect malware file without running it |
| Dynamic Analysis | Run malware in sandbox and observe system behavior |
| ARP Spoofing | Fake ARP replies to redirect local traffic, enabling MITM |
| DDoS | Distributed flood attack from many devices to deny service |

---

*Good luck on the exam! Answers are short, precise, and in English.*

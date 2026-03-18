# Or Ben Ari | Cybersecurity Portfolio

This repository contains my single-file cybersecurity portfolio site together with the lab documentation and study material that support the project section.

The site is designed for GitHub Pages and presents my work in a clean, recruiter-friendly format while still giving technical reviewers direct access to the underlying lab write-ups.

## What is in this repository

- `index.html` — the main static portfolio page
- course lab readmes in Markdown
- study guides and exam-prep notes from the cyber course

## Portfolio focus

The portfolio highlights practical, lab-driven cybersecurity work across:

- SOC and incident response fundamentals
- detection and visibility workflows
- malware analysis and threat-hunting practice
- Linux, Windows, and Active Directory administration
- networking and infrastructure security
- documentation, runbooks, and repeatable operating procedures

## Cyber course labs included

### 1. Networking Enterprise Lab
File: [CHEETSHEET-NETWORKING-EXAM.md](<./CHEETSHEET-NETWORKING-EXAM.md>)

Covers a multi-site network build with VLAN segmentation, Router-on-a-Stick, OSPF, PAT/NAT, DHCP relay, STP root selection, port security, and end-to-end connectivity validation.

### 2. Windows Server & Active Directory Runbook
File: [RunBook - System - DONE.md](<./RunBook - System - DONE.md>)

A full lab runbook for building a Windows domain environment with DC promotion, replication, OU design, users and groups, delegated permissions, DNS, DHCP, GPO, shares, backups, and validation steps.

### 3. Linux Essentials Flashcards
File: [Linux Essentials – Flashcards - CheetSheet.md](<./Linux Essentials – Flashcards - CheetSheet.md>)

Fast-review Linux study notes with command-line questions, short answers, memory tricks, and Hebrew explanations for exam preparation.

### 4. Cyber Theory Study Guide
File: [Exam Theory Answers.md](<./Exam Theory Answers.md>)

Short, structured answers covering recon, Nmap, exploitation, password cracking, web application attacks, malware analysis, and network attack concepts.

### 5. Practical Exam Simulator
File: [Exam Simulator EN.md](<./Exam Simulator EN.md>)

Scenario-based practice guide with hints and answers for Active Directory, Wireshark, Hydra, Nmap, malware research, traffic analysis, and broader blue-team workflows.

## Site structure

The portfolio currently includes these main sections:

- About
- Core capabilities
- Projects, portfolio work, and course labs
- Professional experience
- Certifications, training, and tools
- Contact

The projects area now includes a tabbed layout:

- **Featured portfolio work** — recruiter-friendly summaries of selected projects
- **Cyber course labs** — cards for the lab readmes with direct links to the Markdown files

## Local usage

Open `index.html` in a browser to preview the site locally.

For GitHub Pages deployment, keep `index.html` at the repository root and place the Markdown lab files in the same repository so the project links continue to work.

## Customization notes

To add another lab later:

1. Add the new Markdown file to the repository
2. Add a new lab card inside the projects tab in `index.html`
3. Add the same project to this README so the repository overview stays in sync

## Author

**Or Ben Ari**  
Cybersecurity portfolio focused on defensive workflows, investigation discipline, and practical lab execution.

# System Analysis & Troubleshooting in Network Administration

**Course:** ITEC 2210 – Network Administration, York University
**Assignment:** Assignment 2 – System Analysis & Troubleshooting
**Author:** Golnaz R
**Term:** Summer 2026

---# Network-Administration-and-troubleshooting-assign2-Golnaz R
ITEC 2210 Network Administration project at York University. It explains HTTPS step by step, designs forward and reverse proxies for inspecting TLS traffic, covers RSA vs. ECC vs. post-quantum cryptography, and analyzes the root cause of the 2024 CrowdStrike outage.
![SenarioDiagram](images/diagramA)

## Overview

This assignment analyzes real-world network administration problems from the viewpoint of a York University IT (UIT) system administrator. It covers three areas:

1. **How HTTPS works, from start to finish:** a full walkthrough of a secure file upload
2. **Inspecting encrypted traffic:** network designs for decrypting and inspecting HTTPS traffic, both outbound and inbound
3. **Cryptography and incident analysis:** the move to post-quantum cryptography, and a root-cause analysis of the July 2024 CrowdStrike outage

Each part includes technical explanations, protocol-level steps, architecture diagrams and recommendations an administrator could act on.

---

## Contents

### Part 1: How HTTPS Works (Case One)

A step-by-step explanation of what happens when a student uploads `A2.pdf` to `https://drive.google.com`, starting from a PC that already has an IP address.

| Step | Topic | Key concepts |
|------|-------|--------------|
| 1 | DNS lookup | Local cache, hosts file, recursive resolver, Root → TLD → Authoritative servers, A/AAAA records |
| 2 | TCP 3-way handshake | SYN, SYN-ACK, ACK on port 443 |
| 3 | TLS handshake | ClientHello / ServerHello, SNI, cipher suites, ECDHE key share, certificate verification, CA trust list, digital signatures, session key derivation, Finished messages |
| 4 | Symmetric encryption | RSA/ECC vs. AES/ChaCha20, SHA-256/384 hashing, integrity tags |
| 5 | File transfer | Encrypted HTTPS POST in AES-GCM TLS records |

Includes an **HTTPS traffic flow diagram**.

### Part 2: Inspecting Encrypted Traffic

#### Scenario A: Outbound inspection (YorkU device → internet server)
Design for a **forward proxy with TLS interception**:
- Client provisioning with DHCP and Active Directory **Group Policy** (internal Root CA pushed to the Trusted Root store)
- Proxy routing via PAC file, WPAD, WCCP or policy-based routing
- Split TLS sessions (client ↔ proxy, proxy ↔ real server) with certificates generated on the fly
- Content inspection through **ICAP** (antivirus, DLP, content filtering)
- Supporting services: **NTP**, **Syslog → SIEM**, **NetFlow**, **SNMP** (MIBs/OIDs, traps), **RADIUS/TACACS+** for admin access

#### Scenario B: Inbound inspection (internet user → `eclass.yorku.ca`)
Design for a **reverse proxy with a TLS-terminating load balancer**:
- Public DNS pointing to the proxy instead of the backend servers
- TLS termination using eClass's real public-CA certificate (no changes needed on the client)
- Decryption and inspection (WAF/ICAP) before traffic reaches the backend
- **Mutual TLS (mTLS)** to the backend servers, following zero-trust practice
- Load-balancer health checks, NTP, SIEM logging, NetFlow (DDoS detection), SNMP monitoring, centralized admin authentication

Each scenario includes an **architecture diagram**.

### Part 3: Research

#### 3.1 Post-Quantum Cryptography (Executive Order 14412)
- Why the order was issued: Shor's algorithm and the "harvest now, decrypt later" threat
- Why RSA has been giving way to ECC, and why both now need to be replaced by PQC
- An admin recommendation on whether YorkU should migrate to PQC right away (risk-based, phased approach)
- The mathematics behind each:
  - **RSA:** integer factorization (`n = p × q`)
  - **ECC:** elliptic curve discrete logarithm problem (ECDLP)
  - **PQC:** lattice-based, hash-based and code-based problems

#### Case Two: CrowdStrike Global Outage (July 19, 2024)
- **Root cause:** a faulty Falcon sensor channel file caused an out-of-bounds memory read in kernel mode, leading to BSOD and a boot loop
- **Why only Windows was affected:** the Windows kernel driver architecture, and the difference between kernel-mode and user-mode failures
- **Remediation:** Safe Mode / WinRE, deleting `C-00000291*.sys`, recovering BitLocker keys, and recovery at scale (PXE, WinPE, Intune/SCCM, cloud recovery scripts)
- **Prevention recommendations for UIT:** staged rollouts, out-of-band management (iDRAC/KVM), central storage of BitLocker keys, avoiding dependence on a single vendor, incident response playbooks, and limiting kernel-level access

---

## Skills Demonstrated

- Network protocol analysis (DNS, TCP, TLS 1.3, HTTPS)
- Applied cryptography (asymmetric vs. symmetric encryption, PKI, hashing, PQC)
- Enterprise network design (forward/reverse proxies, load balancing, mTLS)
- Active Directory and Group Policy
- Network monitoring and logging (SNMP, NetFlow, Syslog, SIEM)
- Incident root-cause analysis and disaster recovery planning
- Technical writing and network diagramming

---

## Repository Structure

```
.
├── README.md
├── A2-System-Analysis-and-Troubleshooting.pdf   # Full assignment report
└── diagrams/                                    # (optional) exported diagrams
    ├── https-traffic-flow.png
    ├── scenario-a-forward-proxy.png
    └── scenario-b-reverse-proxy.png
```

---

## References

- The White House. (2026, June 22). *Executive Order 14412: Securing the Nation Against Advanced Cryptographic Attacks.*
- Cisco. (2025, December 5). *What is post-quantum cryptography?*
- CrowdStrike. (2024, July). *Falcon Content Update Remediation and Guidance Hub.*
- Weston, D. (2024, July 20). *Helping our customers through the CrowdStrike outage.* The Official Microsoft Blog.

---

## Academic Integrity

This repository is shared as a portfolio sample of coursework completed for ITEC 2210 at York University. Please do not copy or submit it as your own work.

# 🏠 Enterprise Home Lab Infrastructure

This is my personal Home Lab environment, built out of pure passion for IT infrastructure and systems engineering. While pursuing my Fachinformatiker für Systemintegration (FISI) apprenticeship in Germany, I created this enterprise-grade simulation to gain hands-on experience with virtualization, networking, and security concepts that go beyond pure theory.

## 🎯 Project Goal

The primary goal of this repository is to simulate a complete, secure, and segmented enterprise environment. All decisions are based on Enterprise Best Practices, including network separation (VLANs), storage tiering, and secure remote access strategies.

## 🏗️ Hardware & Virtualization

- **Hypervisor:** Geekom A8 Mini PC (Proxmox VE, 32GB DDR5 RAM)
- **Networking Hardware:** 
  - MikroTik RB5009 (Router / Firewall / Inter-VLAN Routing)
  - TP-Link SG2008P (L2+ Managed PoE+ Switch)
  - TP-Link EAP610 (Wi-Fi 6 Access Point with Multi-SSID)

### 💾 Storage Strategy (3-Tier)
To optimize performance and cost, storage is segmented into three tiers:
- **Tier 1 (Hot):** 1 TB NVMe M.2 SSD (For Proxmox OS and active VM/LXC root disks)
- **Tier 2 (Warm):** 1 TB NVMe via 10 Gbps USB-C (For VM backups, Docker volumes)
- **Tier 3 (Cold):** 128 GB MicroSD (For log archives, ISO files, templates)

## 🕸️ Network & VLAN Design

The environment operates behind a **Double NAT**, meaning traditional port forwarding is not possible. All services are isolated via VLANs:

| VLAN | Name | Subnet | Tag | Purpose |
|---|---|---|---|---|
| **10** | Mgmt | `10.0.10.0/24` | Mgmt | Management Network (Proxmox, Router, Switches) |
| **21** | WinServer | `10.0.21.0/24` | WinS | Active Directory & Windows Server Lab |
| **22** | LinuxLab | `10.0.22.0/24` | LinS | Linux VMs/LXCs, Docker & AI Services |
| **30** | Haus | `10.0.30.0/24` | HLab | Primary Home Network |
| **40** | IoT | `10.0.40.0/24` | IoT | Isolated Smart Home & IoT Devices |
| **60** | Printer | `10.0.60.0/24` | Printer | Network Printers |
| **99** | Kali | `10.0.99.0/24` | KLan | Penetration Testing & Security Lab |

## 🧠 Architectural Decisions

- **LXC vs. Docker (Hybrid Approach):** Infrastructure services (e.g., Tailscale, DNS) run as standalone LXCs for maximum resilience. Application services run in Docker (inside an LXC) for easy deployment.
- **Tailscale Isolation:** The VPN gateway (Tailscale) is installed in a dedicated, isolated LXC. If another service crashes, remote access to the lab remains intact.
- **DNS Standard:** The environment uses **`.mylab`** as the local Top-Level Domain (TLD) to completely avoid conflicts with mDNS (`.local`).
- **No Services on the Proxmox Host:** The Proxmox host is strictly used as a hypervisor. No packages are installed directly on the host to ensure easy recovery.

## <ctrl42> Roadmap & Planned Services

This ecosystem is continuously expanding. Current status of infrastructure services:

- [x] **Proxmox Hypervisor & VLAN Setup** (Completed)
- [ ] **Pi-hole (DNS & Adblocking)** (Planned)
- [ ] **Windows Server Active Directory** (Planned, VLAN 21)
- [ ] **Nginx Proxy Manager** (Planned)
- [ ] **Tailscale VPN (Subnet Router)** (Planned)
- [ ] **Local LLM (Qwen 2.5 7B AI)** (Planned, with GPU Passthrough)
- [ ] **Kali Linux Pentesting Lab** (Planned, VLAN 99)

---
*This section is continuously updated by my autonomous workflow scripts whenever a new service is deployed.*

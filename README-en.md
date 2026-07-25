# 🏠 Personal Home Lab — System Architecture

This is my personal Home Lab environment, created out of a genuine interest in IT infrastructure and systems engineering. Alongside my training as a Fachinformatiker für Systemintegration (FISI) in Germany, I built this lab setup to explore new technologies and gain practical experience with virtualization, networking, and security concepts beyond theory.

## 🎯 Project Goal & Motivation

The main goal of this repository is to gain practical experience in network and systems administration while exploring modern tools firsthand. Rather than focusing only on theory, this Home Lab serves as a personal sandbox to test and understand practical IT concepts — such as VLAN segmentation, 3-tier storage, and structured access control (e.g., IGDLA) — in a compact environment step by step.

## 🏗️ Hardware & Virtualization

- **Hypervisor:** Geekom A8 Mini PC (AMD Ryzen 8000 Series, Proxmox VE, 32GB DDR5 RAM, Radeon 780M iGPU)
- **Networking Hardware:** 
  - MikroTik RB5009UG+S+IN (Router / Firewall / Inter-VLAN Routing)
  - TP-Link SG2008P (L2+ Managed PoE+ Switch)
  - TP-Link EAP610 (Wi-Fi 6 Access Point with Multi-SSID)

### 📷 Hardware Setup Gallery

<div align="center">
  <table border="0">
    <tr>
      <td align="center" colspan="4" style="padding: 10px;">
        <img src="images/real-hardware-setup.jpg" width="480" alt="Home Lab Physical Hardware Stack"><br/>
        <sub><b>📷 Physical Lab Setup:</b> Geekom A8 (Proxmox Node), MikroTik RB5009, TP-Link Omada Switch & EAP610 AP</sub>
      </td>
    </tr>
    <tr>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/geekom-a8.png" width="160" alt="Geekom A8 Mini PC"><br/>
        <sub><b>Geekom A8 Mini PC</b><br/>Proxmox VE Server Node (32GB DDR5)</sub>
      </td>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/mikrotik-rb5009.png" width="180" alt="MikroTik RB5009"><br/>
        <sub><b>MikroTik RB5009</b><br/>Router & Inter-VLAN Firewall</sub>
      </td>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/tplink-sg2008p.png" width="180" alt="TP-Link SG2008P"><br/>
        <sub><b>TP-Link SG2008P</b><br/>L2+ Managed PoE+ Switch</sub>
      </td>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/tplink-eap610.png" width="140" alt="TP-Link EAP610"><br/>
        <sub><b>TP-Link EAP610</b><br/>Wi-Fi 6 Access Point</sub>
      </td>
    </tr>
  </table>
</div>

### 💾 Storage Strategy (3-Tiering)
To optimize performance and organize resources efficiently, storage is divided into three tiers:
- **Tier 1 (Hot - `local-lvm`):** 1 TB NVMe M.2 SSD (For Proxmox OS and active VM/LXC root disks)
- **Tier 2 (Warm - `usb-ssd`):** 1 TB NVMe via 10 Gbps USB-C (For VM backups, Docker volumes, large VM disks)
- **Tier 3 (Cold - `sd-storage`):** 128 GB MicroSD (A2/V30) (For log archives, ISO files, templates, test disks)

<div align="center">
  <table border="0">
    <tr>
      <td align="center" width="50%" style="padding: 10px;">
        <img src="images/samsung-990-evo-plus.png" width="210" alt="Samsung 990 EVO Plus 1TB NVMe SSD"><br/>
        <sub><b>Samsung 990 EVO Plus 1 TB NVMe SSD</b><br/>Tier 2 Warm Storage (VM Backups, Docker Volumes)</sub>
      </td>
      <td align="center" width="50%" style="padding: 10px;">
        <img src="images/nvme-enclosure-10gbps.png" width="190" alt="10Gbps USB-C NVMe Enclosure"><br/>
        <sub><b>USB-C 10 Gbps NVMe Enclosure</b><br/>External Connection for Tier 2 Storage</sub>
      </td>
    </tr>
  </table>
</div>

### 🛡️ Test Hardware for Wi-Fi & Security (VLAN 99 - Kali)

<div align="center">
  <table border="0">
    <tr>
      <td align="center" style="padding: 10px;">
        <img src="images/alfa-awus036ach-wifi-adapter.png" width="220" alt="Alfa AWUS036ACH Dual-Band Wi-Fi Adapter"><br/>
        <sub><b>Alfa AWUS036ACH Dual-Band Wi-Fi Adapter</b><br/>Packet Analysis & Wi-Fi Security Audits (Cisco Ethical Hacker Course)</sub>
      </td>
    </tr>
  </table>
</div>

## 🕸️ Network & VLAN Design

The network operates behind a **Double NAT**, so traditional port forwarding is not available. Individual areas are separated using clear VLAN rules:

| VLAN | Name | Subnet | Tag | Purpose |
|---|---|---|---|---|
| **10** | Mgmt | `10.0.10.0/24` | Mgmt | Management Network (Proxmox, Router, Switches, Pi-hole) |
| **21** | WinServer | `10.0.21.0/24` | WinS | Active Directory & Windows Server Lab |
| **22** | LinuxLab | `10.0.22.0/24` | LinS | Linux VMs/LXCs, Docker, AI Gateway & Proxy Services |
| **30** | Haus | `10.0.30.0/24` | HLab | Primary Home Network (Access to AI Gateway allowed) |
| **40** | IoT | `10.0.40.0/24` | IoT | Isolated Smart Home & IoT Devices |
| **60** | Printer | `10.0.60.0/24` | Printer | Network Printers |
| **99** | Kali | `10.0.99.0/24` | KLan | Penetration Testing & Security Lab |

## 🧠 Practical Decisions & Approaches

- **LXC vs. Docker (Combination):** Core infrastructure services (like Tailscale, Pi-hole) run in standalone LXC containers for stability. Additional application services run in Docker inside an LXC.
- **Tailscale Remote Access:** The VPN access is installed in a separate LXC. This ensures remote network access remains reliable even when testing other services.
- **Windows Server Access Structure:** In the Windows Lab (VLAN 21), access permissions follow the FISI standard **IGDLA** (Identities, Global groups, Domain Local groups, Access).
- **Local AI & GPU Passthrough:** The language model (Qwen 2.5 7B) runs in an unprivileged LXC with hardware acceleration via AMD Radeon 780M iGPU (`/dev/dri`).
- **Website AI Integration:** Connecting the local model via **Cloudflare Tunnel & Turnstile** — allowing secure AI responses on my website without opening ports on the router.
- **DNS Standard:** **`.mylab`** is used as the local Top-Level Domain.

## 🗺️ Roadmap & Next Steps

This learning environment is built step by step. Current status:

- [x] **Proxmox Hypervisor & Storage Tiering** (Configured)
- [x] **Windows Server 2025 DC & Windows 11 Client** (Configured, VLAN 21)
- [ ] **Pi-hole (DNS & Adblocker)** (Planned - Step 1)
- [ ] **Nginx Proxy Manager (Reverse Proxy & SSL)** (Planned - Step 2)
- [ ] **Tailscale Subnet Router (VPN Access)** (Planned - Step 3)
- [ ] **OmniRoute AI Gateway (Model Routing)** (Planned - Step 4)
- [ ] **Uptime Kuma & Status Overview** (Planned)
- [ ] **Vaultwarden & Automated Backup** (Planned)
- [ ] **Kali Linux Pentesting Lab** (Planned, VLAN 99)

---
*This section is updated as new services are set up.*

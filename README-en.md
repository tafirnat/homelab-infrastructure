# 🏠 Personal Home Lab — System Architecture

This is my personal Home Lab environment, built out of pure passion for IT infrastructure and systems engineering. While pursuing my Fachinformatiker für Systemintegration (FISI) apprenticeship in Germany, I created this enterprise-grade simulation to gain hands-on experience with virtualization, networking, and security concepts that go beyond pure theory.

## 🎯 Project Goal

The primary goal of this repository is to simulate a complete, secure, and segmented enterprise environment. All decisions are based on Enterprise Best Practices, including network separation (VLANs), storage tiering, German permission standards (IGDLA), and secure remote access strategies.

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
      <td align="center" colspan="3" style="padding: 10px;">
        <img src="images/real-hardware-setup.jpg" width="480" alt="Home Lab Physical Hardware Stack"><br/>
        <sub><b>📷 Real-World Hardware Setup:</b> MikroTik RB5009, Geekom A8 (Proxmox Node), TP-Link Omada Switch & EAP610 AP</sub>
      </td>
    </tr>
    <tr>
      <td align="center" width="33%" style="padding: 10px;">
        <img src="images/mikrotik-rb5009.png" width="220" alt="MikroTik RB5009"><br/>
        <sub><b>MikroTik RB5009UG+S+IN</b><br/>Core Router & Inter-VLAN Firewall</sub>
      </td>
      <td align="center" width="33%" style="padding: 10px;">
        <img src="images/tplink-sg2008p.png" width="220" alt="TP-Link SG2008P"><br/>
        <sub><b>TP-Link SG2008P</b><br/>L2+ Managed PoE+ Switch</sub>
      </td>
      <td align="center" width="33%" style="padding: 10px;">
        <img src="images/tplink-eap610.png" width="160" alt="TP-Link EAP610"><br/>
        <sub><b>TP-Link EAP610</b><br/>Wi-Fi 6 Access Point</sub>
      </td>
    </tr>
  </table>
</div>

### 💾 Storage Strategy (3-Tiering)
To optimize performance and resource allocation, storage is segmented into three tiers:
- **Tier 1 (Hot - `local-lvm`):** 1 TB NVMe M.2 SSD (For Proxmox OS and active VM/LXC root disks)
- **Tier 2 (Warm - `usb-ssd`):** 1 TB NVMe via 10 Gbps USB-C (For VM backups, Docker volumes, large VM disks)
- **Tier 3 (Cold - `sd-storage`):** 128 GB MicroSD (A2/V30) (For log archives, ISO files, templates, test disks)

## 🕸️ Network & VLAN Design

The environment operates behind a **Double NAT**, meaning traditional port forwarding is not possible. All services are isolated via strict VLAN ACLs:

| VLAN | Name | Subnet | Tag | Purpose |
|---|---|---|---|---|
| **10** | Mgmt | `10.0.10.0/24` | Mgmt | Management Network (Proxmox, Router, Switches, Pi-hole) |
| **21** | WinServer | `10.0.21.0/24` | WinS | Active Directory & Windows Server Lab |
| **22** | LinuxLab | `10.0.22.0/24` | LinS | Linux VMs/LXCs, Docker, AI Gateway & Proxy Services |
| **30** | Haus | `10.0.30.0/24` | HLab | Primary Home Network (Can access AI Gateway) |
| **40** | IoT | `10.0.40.0/24` | IoT | Isolated Smart Home & IoT Devices |
| **60** | Printer | `10.0.60.0/24` | Printer | Network Printers |
| **99** | Kali | `10.0.99.0/24` | KLan | Penetration Testing & Security Lab |

## 🧠 Architectural Decisions & Best Practices

- **LXC vs. Docker (Hybrid Approach):** Infrastructure services (e.g., Tailscale, Pi-hole) run as standalone LXCs for maximum resilience. Application services (NPM, OmniRoute) run in Docker inside an LXC.
- **Tailscale Remote Access:** The VPN gateway is installed in a dedicated, isolated LXC. If another subsystem crashes, administrative remote access to the lab remains intact.
- **Windows Server IGDLA Principle:** Access permissions in the Windows Server Lab (VLAN 21) follow the German industry standard **IGDLA** (Identities, Global groups, Domain Local groups, Access).
- **Local AI & GPU Passthrough:** The local LLM (Qwen 2.5 7B) runs in an unprivileged LXC with hardware acceleration via AMD Radeon 780M iGPU (`/dev/dri`).
- **Portfolio AI Integration:** Local AI is integrated with **Cloudflare Tunnel & Turnstile** — serving free, secure AI chatbot responses on my portfolio site without opening any ports.
- **DNS Standard:** The environment uses **`.mylab`** as the local Top-Level Domain (TLD).

## 🗺️ Roadmap & Planned Services

This ecosystem is continuously expanding. Current status of infrastructure services:

- [x] **Proxmox Hypervisor & Storage Tiering** (Completed)
- [x] **Windows Server 2025 DC & Windows 11 Client** (Completed, VLAN 21)
- [ ] **Pi-hole (DNS & Adblocking)** (Planned - Step 1)
- [ ] **Nginx Proxy Manager (Reverse Proxy & SSL)** (Planned - Step 2)
- [ ] **Tailscale Subnet Router (Zero-Trust VPN)** (Planned - Step 3)
- [ ] **OmniRoute AI Gateway (Groq / NVIDIA NIM / Local LLM)** (Planned - Step 4)
- [ ] **Uptime Kuma & Status Badges** (Planned)
- [ ] **Vaultwarden & Automated Encrypted Backup** (Planned)
- [ ] **Kali Linux Pentesting Lab** (Planned, VLAN 99)

---
*This section is automatically updated whenever new modules are implemented.*

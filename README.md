# 🏠 Personal Home Lab — System Architecture

Dies ist meine persönliche Home-Lab-Umgebung, die aus purer Leidenschaft für IT-Infrastruktur und Systemtechnik entstanden ist. Parallel zu meiner Ausbildung zum Fachinformatiker für Systemintegration (FISI) habe ich diese Testumgebung aufgebaut, um neue Technologien zu entdecken und praktische Erfahrungen (Hands-on) mit Virtualisierung, Netzwerktechnik und IT-Sicherheit zu sammeln.

## 🎯 Projektziel & Motivation

Das Hauptziel dieses Repositories ist es, praktische Erfahrungen im Bereich der Netzwerk- und Systemadministration zu sammeln und moderne Technologien hautnah zu entdecken. Anstatt auf reiner Theorie aufzubauen, dient dieses Home Lab als persönliches Testfeld, um praxisnahe Konzepte — wie VLAN-Segmentierung, 3-Tier Storage und strukturierte Rechtevergabe (z.B. IGDLA) — in einer kompakten Umgebung auszuprobieren und tiefgehend zu verstehen.

## 🏗️ Hardware & Virtualisierung

- **Hypervisor:** Geekom A8 Mini PC (AMD Ryzen 8000 Series, Proxmox VE, 32GB DDR5 RAM, Radeon 780M iGPU)
- **Netzwerk-Hardware:** 
  - MikroTik RB5009UG+S+IN (Router / Firewall / Inter-VLAN Routing)
  - TP-Link SG2008P (L2+ Managed PoE+ Switch)
  - TP-Link EAP610 (Wi-Fi 6 Access Point mit Multi-SSID)

### 📷 Hardware Setup Galerie

<div align="center">
  <table border="0">
    <tr>
      <td align="center" colspan="4" style="padding: 10px;">
        <img src="images/real-hardware-setup.jpg" width="480" alt="Home Lab Physical Hardware Stack"><br/>
        <sub><b>📷 Real-World Hardware Setup:</b> Geekom A8 (Proxmox Node), MikroTik RB5009, TP-Link Omada Switch & EAP610 AP</sub>
      </td>
    </tr>
    <tr>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/geekom-a8.png" width="160" alt="Geekom A8 Mini PC"><br/>
        <sub><b>Geekom A8 Mini PC</b><br/>Proxmox VE Hypervisor Node (32GB DDR5)</sub>
      </td>
      <td align="center" width="25%" style="padding: 8px;">
        <img src="images/mikrotik-rb5009.png" width="180" alt="MikroTik RB5009"><br/>
        <sub><b>MikroTik RB5009</b><br/>Core Router & Inter-VLAN Firewall</sub>
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

### 💾 Storage-Strategie (3-Tiering)
Um die Leistung zu optimieren und Ressourcen effizient zu nutzen, ist der Speicher in drei Schichten unterteilt:
- **Tier 1 (Hot - `local-lvm`):** 1 TB NVMe M.2 SSD (Für Proxmox OS und aktive VM/LXC Root-Disks)
- **Tier 2 (Warm - `usb-ssd`):** 1 TB NVMe über 10 Gbps USB-C (Für VM-Backups, Docker-Volumes, große VM-Disks)
- **Tier 3 (Cold - `sd-storage`):** 128 GB MicroSD (A2/V30) (Für Log-Archive, ISO-Dateien, Templates, Test-Disks)

<div align="center">
  <table border="0">
    <tr>
      <td align="center" width="50%" style="padding: 10px;">
        <img src="images/samsung-990-evo-plus.png" width="210" alt="Samsung 990 EVO Plus NVMe SSD"><br/>
        <sub><b>Samsung 990 EVO Plus NVMe SSD</b><br/>Tier 2 Warm Storage (VM Backups, Docker Volumes)</sub>
      </td>
      <td align="center" width="50%" style="padding: 10px;">
        <img src="images/nvme-enclosure-10gbps.png" width="190" alt="10Gbps USB-C NVMe Enclosure"><br/>
        <sub><b>USB-C 10 Gbps NVMe Enclosure</b><br/>High-Speed External Interface (Mount Point)</sub>
      </td>
    </tr>
  </table>
</div>

### 🛡️ Security & Wireless Audit Hardware (VLAN 99 - Kali)

<div align="center">
  <table border="0">
    <tr>
      <td align="center" style="padding: 10px;">
        <img src="images/alfa-awus036ach-wifi-adapter.png" width="220" alt="Alfa AWUS036ACH Dual-Band Wi-Fi Adapter"><br/>
        <sub><b>Alfa AWUS036ACH Dual-Band Wi-Fi Adapter</b><br/>Paket-Analyse & WLAN-Sicherheitsprüfungen (Cisco Ethical Hacker Lab)</sub>
      </td>
    </tr>
  </table>
</div>

## 🕸️ Netzwerk & VLAN Design

Die Umgebung läuft hinter einem **Double NAT**, weshalb keine klassischen Portfreigaben (Port Forwarding) möglich sind. Alle Dienste sind durch strikte VLAN-ACLs isoliert:

| VLAN | Name | Subnet | Tag | Verwendungszweck |
|---|---|---|---|---|
| **10** | Mgmt | `10.0.10.0/24` | Mgmt | Management-Netzwerk (Proxmox, Router, Switche, Pi-hole) |
| **21** | WinServer | `10.0.21.0/24` | WinS | Active Directory & Windows Server Lab |
| **22** | LinuxLab | `10.0.22.0/24` | LinS | Linux VMs/LXCs, Docker, AI Gateway & Proxy Dienste |
| **30** | Haus | `10.0.30.0/24` | HLab | Haupt-Heimnetzwerk (Können auf AI Gateway zugreifen) |
| **40** | IoT | `10.0.40.0/24` | IoT | Isolierte Smart-Home & IoT-Geräte |
| **60** | Printer | `10.0.60.0/24` | Printer | Netzwerkdrucker |
| **99** | Kali | `10.0.99.0/24` | KLan | Penetration Testing & Sicherheits-Lab |

## 🧠 Architektur-Entscheidungen & Best Practices

- **LXC vs. Docker (Hybrid-Ansatz):** Infrastrukturdienste (z.B. Tailscale, Pi-hole) laufen als Standalone LXC für maximale Ausfallsicherheit. Applikationsdienste (NPM, OmniRoute) laufen in Docker innerhalb eines LXC.
- **Tailscale Remote-Zugang:** Der VPN-Zugang ist in einem isolierten LXC installiert. Fällt ein anderes Subsystem aus, bleibt der administrative Fernzugriff auf das Heimnetz unberührt.
- **Windows Server IGDLA-Prinzip:** Im Windows Server Lab (VLAN 21) werden Zugriffsberechtigungen nach dem deutschen FISI-Standard **IGDLA** (Identities, Global groups, Domain Local groups, Access) vergeben.
- **Local AI & GPU-Passthrough:** Das lokale Sprachmodell (Qwen 2.5 7B) läuft in einem unprivilegierten LXC mit Hardware-Beschleunigung über AMD Radeon 780M iGPU (`/dev/dri`).
- **Portfolio AI Integration:** Integration von Local AI mit **Cloudflare Tunnel & Turnstile** — ermöglicht kostenlose, sichere KI-Chatbot-Antworten auf meiner Website, ohne Ports im Modem zu öffnen.
- **DNS-Standard:** Die Umgebung verwendet **`.mylab`** als lokale Top-Level-Domain (TLD).

## 🗺️ Roadmap & Geplante Services

Dieses Ökosystem wird kontinuierlich erweitert. Status der Infrastruktur-Dienste:

- [x] **Proxmox Hypervisor & Storage Tiering** (Abgeschlossen)
- [x] **Windows Server 2025 DC & Windows 11 Client** (Abgeschlossen, VLAN 21)
- [ ] **Pi-hole (DNS & Adblocking)** (Geplant - Schritt 1)
- [ ] **Nginx Proxy Manager (Reverse Proxy & SSL)** (Geplant - Schritt 2)
- [ ] **Tailscale Subnet Router (Zero-Trust VPN)** (Geplant - Schritt 3)
- [ ] **OmniRoute AI Gateway (Groq / NVIDIA NIM / Local LLM)** (Geplant - Schritt 4)
- [ ] **Uptime Kuma & Status Badges** (Geplant)
- [ ] **Vaultwarden & Automated Encrypted Backup** (Geplant)
- [ ] **Kali Linux Pentesting Lab** (Geplant, VLAN 99)

---
*Dieser Bereich wird automatisch aktualisiert, sobald neue Module implementiert werden.*

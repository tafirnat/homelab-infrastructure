# 🏠 Enterprise Home Lab Infrastructure

Dies ist meine persönliche Home-Lab-Umgebung, die aus purer Leidenschaft für IT-Infrastruktur und Systemtechnik entstanden ist. Parallel zu meiner Ausbildung zum Fachinformatiker für Systemintegration (FISI) habe ich diese Enterprise-Simulation aufgebaut, um echte, praktische Erfahrungen (Hands-on) mit Virtualisierung, Netzwerktechnik und IT-Sicherheit zu sammeln, die über die Theorie hinausgehen.

## 🎯 Projektziel

Das primäre Ziel dieses Repositories ist die Simulation einer vollständigen, sicheren und segmentierten Unternehmensumgebung. Alle Entscheidungen basieren auf Enterprise Best Practices, einschließlich Netzwerktrennung (VLANs), Storage-Tiering und sicheren Remote-Zugriffsstrategien.

## 🏗️ Hardware & Virtualisierung

- **Hypervisor:** Geekom A8 Mini PC (Proxmox VE, 32GB DDR5 RAM)
- **Netzwerk-Hardware:** 
  - MikroTik RB5009 (Router / Firewall / Inter-VLAN Routing)
  - TP-Link SG2008P (L2+ Managed PoE+ Switch)
  - TP-Link EAP610 (Wi-Fi 6 Access Point mit Multi-SSID)

### 💾 Storage-Strategie (3-Tier)
Um die Leistung zu optimieren, ist der Speicher in drei Schichten (Tiers) unterteilt:
- **Tier 1 (Hot):** 1 TB NVMe M.2 SSD (Für Proxmox OS und aktive VM/LXC Root-Disks)
- **Tier 2 (Warm):** 1 TB NVMe über 10 Gbps USB-C (Für VM-Backups, Docker-Volumes)
- **Tier 3 (Cold):** 128 GB MicroSD (Für Log-Archive, ISO-Dateien, Templates)

## 🕸️ Netzwerk & VLAN Design

Die Umgebung läuft hinter einem **Double NAT**, weshalb keine klassischen Portfreigaben (Port Forwarding) möglich sind. Alle Dienste sind durch VLANs isoliert:

| VLAN | Name | Subnet | Tag | Verwendungszweck |
|---|---|---|---|---|
| **10** | Mgmt | `10.0.10.0/24` | Mgmt | Management-Netzwerk (Proxmox, Router, Switche) |
| **21** | WinServer | `10.0.21.0/24` | WinS | Active Directory & Windows Server Lab |
| **22** | LinuxLab | `10.0.22.0/24` | LinS | Linux VMs/LXCs, Docker & AI Dienste |
| **30** | Haus | `10.0.30.0/24` | HLab | Haupt-Heimnetzwerk |
| **40** | IoT | `10.0.40.0/24` | IoT | Isolierte Smart-Home & IoT-Geräte |
| **60** | Printer | `10.0.60.0/24` | Printer | Netzwerkdrucker |
| **99** | Kali | `10.0.99.0/24` | KLan | Penetration Testing & Sicherheits-Lab |

## 🧠 Architektur-Entscheidungen

- **LXC vs. Docker (Hybrid-Ansatz):** Infrastrukturdienste (z.B. Tailscale, DNS) laufen als Standalone LXC für maximale Ausfallsicherheit. Applikationsdienste laufen in Docker (innerhalb eines LXC) für einfache Verwaltung.
- **Tailscale Isolation:** Der VPN-Zugang (Tailscale) ist in einem dedizierten LXC installiert. Fällt ein anderer Dienst aus, bleibt der Fernzugriff intakt.
- **DNS-Standard:** Die Umgebung verwendet **`.mylab`** als lokale Top-Level-Domain (TLD), um Konflikte mit mDNS (`.local`) zu vermeiden.
- **Keine Dienste auf dem Proxmox Host:** Der Proxmox Host wird ausschließlich als Hypervisor genutzt. Es werden keine Pakete direkt auf dem Host installiert.

## 🗺️ Roadmap & Geplante Services

Dieses Ökosystem wird kontinuierlich erweitert. Der aktuelle Status der Infrastruktur-Dienste:

- [x] **Proxmox Hypervisor & VLAN Setup** (Abgeschlossen)
- [ ] **Pi-hole (DNS & Adblocking)** (Geplant)
- [ ] **Windows Server Active Directory** (Geplant, VLAN 21)
- [ ] **Nginx Proxy Manager** (Geplant)
- [ ] **Tailscale VPN (Subnet Router)** (Geplant)
- [ ] **Local LLM (Qwen 2.5 7B AI)** (Geplant, mit GPU-Passthrough)
- [ ] **Kali Linux Pentesting Lab** (Geplant, VLAN 99)

---
*Dieser Bereich wird durch meine autonomen Workflow-Skripte laufend aktualisiert, sobald ein neuer Dienst installiert wird.*

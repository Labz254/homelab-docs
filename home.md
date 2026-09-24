---
title: Welcome to Labz254
description: My homelab journey documenting Proxmox VE virtualization, self-hosted services, and network infrastructure built on enterprise-grade hardware with GNOME desktop integration
published: true
date: 2026-09-24T20:13:20.185Z
tags: introduction, homelab, proxmox, infrastructure, self-hosting, virtualization, documentation, getting-started, architecture, kenya, gnome, mikrotik
editor: markdown
dateCreated: 2026-09-22T10:22:24.840Z
---

# 🏠 Welcome to Labz254

> *"Building the future, one VM at a time."* 🇰🇪

---

## 👋 The Vision

Welcome to **Labz254**, my personal homelab, learning playground, and digital sanctuary. This project is about transforming a single, powerful workstation into a robust, enterprise-grade server environment. 

Here, I document my journey into self-hosting, virtualization, and network engineering. Everything you see here is built, configured, and maintained by me, from the bare metal up. No black boxes, no managed cloud dependencies—just pure, self-hosted freedom.

---

## 🖥️ The "One PC" Challenge: Why GNOME on Proxmox?

Most homelabs start with a dedicated, headless server tucked away in a closet. My constraint was different: **I only have one physical machine.** 

I needed this single HP workstation to serve two distinct, demanding purposes:
1. A **daily-driver workstation** for development, browsing, and daily tasks
2. A **24/7 enterprise-grade hypervisor** running 30+ critical services

**The Solution?** Installing a **GNOME Desktop Environment** directly on top of the Proxmox VE host (Debian). 

Instead of sacrificing the hypervisor's power or buying a second machine, I bridged the gap. By carefully installing `gdm3` and `task-gnome-desktop`, I gained a beautiful, functional GUI for my daily workflow, while meticulously verifying that core Proxmox services (`pveproxy`, `pve-cluster`, `pvedaemon`) remain completely unaffected. It is the best of both worlds: a sleek desktop experience sitting atop a rock-solid virtualization foundation.

---

## 🌟 Key Principles Behind Labz254

1. **Redundancy First:** Every critical component has a backup. RAID 1 everywhere.
2. **Security by Design:** SSO, password management, and intrusion prevention are built-in, not afterthoughts.
3. **Automation Over Manual Work:** If I do it twice, I automate it.
4. **Self-Hosted Freedom:** No cloud dependencies. My data, my control.
5. **Documentation is King:** If it is not documented, it does not exist.
6. **Learn by Doing:** Every error is a learning opportunity.
7. **Community Giving:** Everything is open-source and shareable.

---

## 🛠️ The Hardware Arsenal

I didn't just buy parts; I engineered a system with redundancy, performance, and future-proofing in mind.

### ⚙️ The Core: HP Z2 G9 Workstation

| Component | Specification | Why I Chose It |
|-----------|---------------|----------------|
| **CPU** | Intel Core i9-14900K | Massive core count for concurrent VMs, plus high single-thread speed for responsive desktop use |
| **RAM** | 32GB DDR4 3200MHz | The sweet spot for running 30+ lightweight containers and several heavier VMs simultaneously |
| **GPU (Dedicated)** | NVIDIA RTX 5070 12GB | Dedicated to host for AI workloads (Ollama Qwen models), Frigate AI detection, and media transcoding |
| **GPU (Integrated)** | Intel UHD Graphics 770 | Handles Jellyfin hardware transcoding independently, sharing the load with the RTX 5070 |
| **PSU** | 700W | Highly efficient power delivery to handle transient CPU/GPU spikes without breaking a sweat |
| **Network** | 3× 1GbE Ports | Expanded from default with additional PCIe network card for network segmentation and redundancy |

### 💾 Storage Architecture: Redundancy First

*Data loss is not an option. All storage arrays are configured in **RAID 1 (Mirror)** for maximum safety.*

| Array Purpose | Drive Configuration | Role in the Lab |
|---------------|---------------------|-----------------|
| **OS & Virtual Machines** | 2× 1TB NVMe Gen 4 | Blazing fast I/O for Proxmox host, VM disks, and container volumes |
| **Media & Cloud Data** | 2× 2TB SATA SSD | Silent, fast, and reliable access for Nextcloud, Immich, and Jellyfin libraries |
| **Surveillance Footage** | 2× 2TB HDD | High-capacity, continuous-write optimization for 24/7 CCTV recording |
| **External Backup** | 1× 2TB External HDD | Emergency backup and disaster recovery for critical configurations and data |

> 💡 **Total Usable Storage:** ~5TB across three fully redundant arrays + 2TB external backup

### 📹 Surveillance Hardware
*High-definition security for the perimeter.*

| Device | Model | Purpose |
|--------|-------|---------|
| **IP Cameras** | 4× Reolink CX410 | 4K PoE cameras for Frigate AI detection and 24/7 recording. |

### 🔌 Power Protection: LightWave UPS

*Clean power is non-negotiable for data integrity and hardware longevity.*

| Device | Specification | Purpose |
|--------|---------------|---------|
| **UPS** | LightWave 1.5KVA (900W) with AVR | Protects against power outages, surges, and voltage fluctuations. Provides graceful shutdown capability and runtime during brief outages |

> ⚡ **Why 1.5KVA?** Provides sufficient wattage (900W) to handle the 700W PSU load plus overhead for safe operation and battery runtime

### 🌐 Network Topology: Streamlined MikroTik Setup

*A professional-grade network with minimal hardware, maximum performance, fully wired with Cat 6a.*

```text
🌐 ISP Fiber
     │
     ▼
📡 ONT (Optical Network Terminal)
     │
     ▼ Cat 6a Backbone
┌────────────────────────────────────────────────────────────────┐
│  🔴 MikroTik RB5009UPr+S+IN                                    │
│  (All-in-one: Router + PoE+ Switch + 10G SFP+)                 │
└─┬────────────────────────────┬───────────────────────────┬─────┘
  │                            │                           │
  ▼ PoE+                       ▼ Cat 6a                    ▼ PoE+
┌──────────────────┐  ┌──────────────────────┐  ┌──────────────────────┐
│ 📡 MikroTik cAP ax│  │ 🖥️ Proxmox Server    │  │ 📹 4× Reolink CX410 │
│ (WiFi 6 / PoE)   │  │ (HP Z2 G9 / 3× 1GbE) │  │ (PoE IP Cameras)     │
└──────────────────┘  └──────────────────────┘  └──────────────────────┘
```
**Network Gear Breakdown:**

| Device | Model | Function |
|--------|-------|----------|
| **Router/Switch** | MikroTik RB5009UPr+S+IN | Core routing, firewall, DHCP, PoE+ switching, and 10G uplinks in one powerful device |
| **Wireless** | MikroTik cAP ax | WiFi 6 coverage, powered directly by the RB5009 via PoE for a clean, cable-free setup |
| **Server NICs** | HP Z2 G9 Built-in + PCIe Card | 3× 1GbE ports total for network segmentation, VM isolation, and future expansion |

---

## 🎮 GPU Architecture: Host-Centric Resource Management

### The Smart Approach
Instead of complex PCIe passthrough to individual VMs, I keep the **NVIDIA RTX 5070 entirely on the Proxmox host**. The only hardware acceleration utilized is the **Intel UHD Graphics 770 (Quick Sync)**, which is dedicated to Jellyfin media transcoding. 

**Host GPU Tasks (RTX 5070):**
- **GNOME Desktop:** Acts as the default display processor for a smooth, responsive graphical interface.
- **Local AI (Ollama):** Runs Qwen LLM models, which the **n8n Automation VM** accesses seamlessly via local network API calls.
- **AI Subtitle Generation:** Powers Whisper for fast, accurate subtitle creation.
- **Media Processing:** Handles heavy video conversions and encoding via Shutter Encoder.

**Edge AI Note:** The 4× Reolink CX410 cameras process their AI object detection locally on their own built-in hardware. Frigate acts purely as a Network Video Recorder (NVR) and stream manager, saving host GPU VRAM entirely.

### Why This Works:

✅ **Zero VM GPU Passthrough:** No IOMMU headaches, no VFIO configuration, and no resource locking. The Windows and Ubuntu VMs run smoothly without needing direct GPU access.  
✅ **Centralized Power:** The RTX 5070 serves all heavy host-level tasks (AI, encoding, display) from one centralized location, making it easier to monitor and maintain.  
✅ **Smart Load Balancing:** The Intel iGPU handles Jellyfin transcoding independently, leaving the RTX 5070's resources completely free for AI and desktop rendering.  
✅ **Edge AI Efficiency:** Offloading camera AI detection to the Reolink hardware means Frigate runs incredibly lightweight on the host.

> 💡 **Real-World Performance:** Jellyfin transcoding uses the Intel iGPU (0MB VRAM). The Reolink cameras handle their own AI processing. This leaves the RTX 5070's full 12GB of VRAM available for Ollama models, Whisper subtitle generation, Shutter Encoder tasks, and buttery-smooth GNOME desktop performance.

---

## 🗺️ The Virtual Ecosystem (9 VMs)

To maintain security, performance, and easy troubleshooting, I segment my services into **9 dedicated Virtual Machines**. Each VM has a specific role, preventing a single point of failure from taking down the entire lab.

### 🪟 VM 1: Windows Workstation
**General-purpose Windows environment for Windows-specific tasks.**
- **Purpose:** Windows-only applications, testing, and general desktop tasks
- **Configuration:** Lightweight VM without GPU passthrough
- **Access:** Remote Desktop and VNC
- **Note:** Separate gaming console used for GPU-intensive gaming to avoid host GPU contention

### 🐧 VM 2: Ubuntu Desktop
**Linux development and testing environment.**
- **Purpose:** Software development, Linux-specific applications, and testing
- **Configuration:** Full desktop environment with development tools
- **Access:** SPICE/VNC and SSH
- **Use Cases:** VS Code development, Docker testing, Linux application testing

### 🛡️ VM 3: The Gateway (Edge & Security)
**The front door to my network.** Handles all incoming traffic, authentication, and intrusion prevention.
- **OPNsense:** Firewall, routing, and advanced network security.
- **Traefik:** Dynamic reverse proxy and load balancer for all services.
- **Authentik:** Centralized Identity Provider (IdP) and Single Sign-On (SSO).
- **CrowdSec:** Collaborative intrusion prevention system (IPS) to block malicious IPs.
- **Docker Socket Proxy:** Securely exposes Docker API to Traefik without full root access.
- **mkcert & Alloy:** Local TLS certificates and centralized log/metric shipping.

### 🌐 VM 4: The Network Controller
**Manages internal routing, DNS filtering, and secure remote access.**
- **AdGuard Home:** Network-wide ad, tracker, and phishing protection via DNS.
- **Headscale:** Open-source, self-hosted Tailscale control server for secure remote access.
- **Headplane:** Modern web UI for managing Headscale configuration.
- **mkcert & Alloy:** Local TLS and logging.

### 📊 VM 5: The Observatory (Monitoring)
**The eyes and ears of the homelab.** If something breaks, this VM tells me about it.
- **Prometheus:** Time-series database for scraping metrics from all services.
- **Grafana:** Beautiful dashboards visualizing Prometheus and Loki data.
- **Loki:** Aggregates and stores logs from all containers for analysis.
- **Uptime Kuma:** Fancy, self-hosted monitoring tool with beautiful status pages.
- **pve-exporter:** Scrapes Proxmox VE host metrics (CPU, RAM, VM status) for Grafana.
- **mkcert & Alloy:** Local TLS and logging.

### 💾 VM 6: Core Storage & Productivity
**The digital vault for personal data, passwords, and documentation.**
- **Frigate:** AI-powered NVR and object detection for the 4× Reolink CX410 cameras.
- **Nextcloud:** Self-hosted cloud storage, calendar, contacts, and collaboration.
- **Immich:** High-performance, self-hosted photo and video backup.
- **Vaultwarden:** Lightweight, self-hosted Bitwarden-compatible password manager.
- **Wiki.js:** This documentation site!
- **Vikunja:** Self-hosted task management and to-do application.
- **Caddy:** Web server with automatic HTTPS and reverse proxy.
- **mkcert & Alloy:** Local TLS and logging.

### 🎬 VM 7: Media & Entertainment Hub
**The family entertainment center, fully automated.**
- **Jellyfin:** Media server with hardware-accelerated transcoding (Intel UHD 770 on host).
- **The *Arr Stack:** Sonarr (TV), Radarr (Movies), Lidarr (Music), Readarr (Books), Prowlarr (Indexers), Bazarr (Subtitles).
- **Seerr:** Modern, beautiful request management interface for family and friends.
- **qBittorrent:** Secure, automated torrent downloading.
- **mkcert & Alloy:** Local TLS and logging.

### 🤖 VM 8: Automation & AI
**Where the magic happens.** Workflows, local AI, and smart integrations.
- **n8n:** Powerful, node-based workflow automation tool.
- **Evolution-API:** WhatsApp and messaging API integration for custom notifications/bots.
- **SearXNG:** Privacy-respecting, self-hosted metasearch engine.
- **Ollama API Calls:** Connects to host-based Ollama for local LLM inference (RTX 5070).
- **Alloy:** Monitoring and logging.

### 🏠 VM 9: Smart Home Hub
**The brain of the physical house.**
- **Home Assistant (HAOS):** Running as a full, dedicated Virtual Machine (not a container) to ensure direct hardware access for Zigbee/Z-Wave dongles and maximum stability for home automation rules.

---

## 🚀 Current Service Stack Overview

I currently run **50+ services** across 9 VMs + host containers to automate my life, entertain my family, and learn cutting-edge technologies:

| Category | Services | Count |
|----------|----------|-------|
| 🔐 **Security & Identity** | OPNsense, Authentik, Vaultwarden, CrowdSec, AdGuard Home | 5 |
| 🎬 **Media & Entertainment** | Jellyfin, Sonarr, Radarr, Lidarr, Readarr, Prowlarr, Bazarr, Seerr, qBittorrent | 9 |
| ☁️ **Cloud & Productivity** | Nextcloud, Immich, Wiki.js, Vikunja | 4 |
| 📊 **Monitoring** | Grafana, Prometheus, Loki, Uptime Kuma, pve-exporter, Alloy (×9) | 13 |
| 🤖 **AI & Automation** | Ollama (Host), n8n, Evolution-API | 3 |
| 🏠 **Home Automation** | Home Assistant | 1 |
| 🌐 **Networking & Proxy** | Traefik, Headscale, Headplane, SearXNG, Caddy, Docker Socket Proxy | 6 |
| 🔧 **Infrastructure** | mkcert (×9 VMs), Frigate (VM 6) | 10 |

**Total Services:** 50+ across 9 VMs + host

---

## 📚 How to Use This Wiki

This documentation is designed to be a **living, breathing guide**. Here is how to navigate it:

- 📖 **Setup Guides:** Step-by-step commands and configurations (copy-paste ready).
- 🔧 **Troubleshooting:** Real-world errors I faced, the logs I checked, and how I fixed them.
- 🏗️ **Architecture:** Diagrams explaining how the network, VMs, and services communicate.
- 🛡️ **Security:** Best practices for hardening self-hosted services and managing secrets.

> 💡 **Pro Tip:** All documentation is written in Markdown and automatically synced via Git to my [GitHub Repository](https://github.com/Labz254/homelab-docs) for version control, backup, and public viewing.

---

## 🎯 Documentation Roadmap

Now that you know the **what** and the **why**, the upcoming sections will dive into the **how**:

### **Phase 1:** Proxmox Installation & System Hardening
- Initial Proxmox VE installation
- Network configuration
- Storage setup (RAID 1 arrays)
- System updates and security hardening

### **Phase 2:** Creating a Secure Non-Root Admin User (`cannz`)
- Why you should never use root for daily tasks
- Creating the `cannz` user
- Configuring sudo privileges
- Testing administrative access

### **Phase 3:** Safely Installing GNOME on the Proxmox Host
- Installing `task-gnome-desktop` and `gdm3`
- Ensuring Proxmox services remain unaffected
- Configuring display manager and boot targets
- Desktop customization with GNOME Tweaks

### **Phase 4:** Post-Reboot System Verification Checklist
- Kernel verification
- Service health checks
- Network connectivity tests
- Web interface validation
- Initial configuration backup

### **Phase 5:** System Setup & Hardening
- Firewall configuration (UFW/nftables)
- Network segmentation
- SSH hardening
- Fail2ban setup
- Security best practices

### **Phase 6:** Essential Desktop Applications
- Shutter Encoder (video encoding)
- VLC Media Player
- Visual Studio Code
- btop (system monitoring)
- Additional productivity tools

### **Phase 7:** The 9 VMs + Docker Deployment
- Docker installation and configuration
- Docker Compose setup
- Deploying all 9 VMs with their respective services
- Network segmentation and security
- Host services setup (Ollama, Frigate, Jellyfin, n8n)

---

## 🙏 Acknowledgments

This homelab journey would not have been possible without:
- **AI Assistance:** ChatGPT, Claude, Qwen, and other AI models for troubleshooting and guidance.
- **Online References:** Proxmox documentation, Linux man pages, and community forums.
- **Online Media:** YouTube tutorials, technical blogs, and homelab communities.
- **Open Source Community:** The incredible developers behind all the self-hosted services I use.

---

## 📞 Connect & Contribute

This lab is a continuous work in progress. Found a mistake? Have a better way to do something? 

- **GitHub Issues:** Open an issue on the repository.
- **Discussions:** Start a conversation about improvements.
- **Pull Requests:** Submit your own documentation enhancements.

---

**Built with ❤️, caffeine, and late-night terminal sessions in Kenya | 2026**

*"The only way to do great work is to love what you do."* — Steve Jobs

---

*Last Updated: September 2026 | Wiki.js Version: 2.x | Proxmox VE: 9.x*
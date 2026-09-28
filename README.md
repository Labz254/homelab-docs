#  Labz254 Homelab Documentation

> *"Building the future, one VM at a time."* 🇰🇪

This repository contains the source Markdown files for the **Labz254 Wiki**, a comprehensive documentation of my personal homelab journey. It covers everything from bare-metal BIOS configuration to deploying a complex, 10-VM virtualized ecosystem on Proxmox VE.

> 🌐 **Live Documentation:** [Visit the Labz254 Wiki](https://wiki.home.tail/en/home)

---

## ️ The Hardware Arsenal

This entire ecosystem runs on a single, powerful workstation acting as a Type-1 hypervisor, paired with enterprise-grade networking.

| Component | Specification |
|-----------|---------------|
| **Workstation** | HP Z2 G9 Tower |
| **CPU** | Intel Core i9-14900K |
| **RAM** | 32GB DDR4 3200MHz |
| **GPU** | NVIDIA RTX 5070 12GB (Host) + Intel UHD 770 (Transcoding) |
| **Storage** | 2x 1TB NVMe (OS/VMs), 2x 2TB SSD (Media), 2x 2TB HDD (CCTV) |
| **Network** | MikroTik RB5009UPr+S+IN (Router/Switch) + cAP ax (WiFi 6) |
| **Surveillance** | 4x Reolink CX410 4K PoE Cameras |

---

## 🗺️ The Virtual Ecosystem (10 VMs)

To maintain security and stability, services are segmented across 10 dedicated Virtual Machines:

1.  ** Windows Workstation:** General-purpose Windows tasks.
2.  **🐧 Ubuntu Desktop:** Linux development and testing.
3.  ** Kali Linux:** Penetration testing and security auditing.
4.  **🛡️ Gateway (OPNsense):** Firewall, Traefik, Authentik, CrowdSec.
5.  ** Network Controller:** AdGuard Home, Headscale.
6.  **📊 Observatory:** Prometheus, Grafana, Loki, Uptime Kuma.
7.  **💾 Storage & Productivity:** Frigate (NVR), Nextcloud, Immich, Vaultwarden, Wiki.js.
8.  **🎬 Media Hub:** Jellyfin, *Arr Stack, Seerr.
9.  ** Automation & AI:** n8n, Ollama (Local LLMs), SearXNG.
10. **🏠 Smart Home:** Home Assistant (HAOS).

---

## 📖 Documentation Roadmap

This wiki is structured as a step-by-step guide to replicating this setup:

*   **Phase 1:** BIOS Setup & Preparation
*   **Phase 2:** Proxmox Installation (ZFS RAID 1)
*   **Phase 3:** Secure Non-Root Admin User Setup
*   **Phase 4:** GNOME Desktop Installation on Proxmox Host
*   **Phase 5:** Post-Reboot Verification & System Hardening
*   **Phase 6:** Essential Desktop Applications & Drivers
*   **Phase 7:** The 10 VMs + Docker Deployment

---

## 🤝 Contributing

This is a personal documentation project, but if you spot a typo or have a suggestion for a better configuration, feel free to open an Issue or submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Built with ❤️ in Kenya | 2026**

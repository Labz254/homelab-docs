---
title: Welcome to Labz254
description: My homelab journey documenting Proxmox VE virtualization, self-hosted services, and network infrastructure built on enterprise-grade hardware with GNOME desktop integration
published: true
date: 2026-09-22T10:22:24.840Z
tags: introduction, homelab, proxmox, infrastructure, self-hosting, virtualization, documentation, getting-started, architecture, kenya, gnome, mikrotik
editor: markdown
dateCreated: 2026-09-22T10:22:24.840Z
---

# 🏠 Welcome to Labz254

> *"Building the future, one VM at a time."* 🇰🇪

---

# 📖 Part 1: The Vision & Philosophy

## 👋 The Vision
Welcome to **Labz254**, my personal homelab, learning playground, and digital sanctuary. This project is about transforming a single, powerful workstation into a robust, enterprise-grade server environment. 

Here, I document my journey into self-hosting, virtualization, and network engineering. Everything you see here is built, configured, and maintained by me, from the bare metal up. No black boxes, no managed cloud dependencies—just pure, self-hosted freedom.

## 🖥️ The "One PC" Challenge: Why GNOME on Proxmox?
Most homelabs start with a dedicated, headless server tucked away in a closet. My constraint was different: **I only have one physical machine.** 

I needed this single HP workstation to serve two distinct, demanding purposes:
1. A **daily-driver workstation** for development, browsing, and daily tasks.
2. A **24/7 enterprise-grade hypervisor** running 30+ critical services.

**The Solution?** Installing a **GNOME Desktop Environment** directly on top of the Proxmox VE host (Debian). 

Instead of sacrificing the hypervisor's power or buying a second machine, I bridged the gap. By carefully installing `gdm3` and `task-gnome-desktop`, I gained a beautiful, functional GUI for my daily workflow, while meticulously verifying that core Proxmox services (`pveproxy`, `pve-cluster`, `pvedaemon`) remain completely unaffected. It is the best of both worlds: a sleek desktop experience sitting atop a rock-solid virtualization foundation.

## 🌟 Key Principles Behind Labz254
1. **Redundancy First:** Every critical component has a backup. RAID 1 everywhere.
2. **Security by Design:** SSO, password management, and intrusion prevention are built-in, not afterthoughts.
3. **Automation Over Manual Work:** If I do it twice, I automate it.
4. **Self-Hosted Freedom:** No cloud dependencies. My data, my control.
5. **Documentation is King:** If it is not documented, it does not exist.

---

# 🏗️ Part 2: The Architecture & Setup

## 🛠️ The Hardware Arsenal
I didn't just buy parts; I engineered a system with redundancy, performance, and future-proofing in mind.

### ⚙️ The Core: HP Z2 G9 Workstation
| Component | Specification | Why I Chose It |
|-----------|---------------|----------------|
| **CPU** | Intel Core i9-14900K | Massive core count for concurrent VMs, plus high single-thread speed for responsive desktop use. |
| **RAM** | 32GB DDR4 3200MHz | The sweet spot for running 30+ lightweight containers and several heavier VMs simultaneously. |
| **GPU** | NVIDIA RTX 5070 12GB | Future-proofing for local AI workloads (Ollama) and hardware-accelerated media transcoding (Jellyfin). |
| **PSU** | 700W | Highly efficient power delivery to handle transient CPU/GPU spikes without breaking a sweat. |

### 💾 Storage Architecture: Redundancy First
*Data loss is not an option. All storage arrays are configured in **RAID 1 (Mirror)** for maximum safety.*

| Array Purpose | Drive Configuration | Role in the Lab |
|---------------|---------------------|-----------------|
| **OS & Virtual Machines** | 2× 1TB NVMe Gen 4 | Blazing fast I/O for Proxmox host, VM disks, and container volumes. |
| **Media & Cloud Data** | 2× **2TB** SATA SSD | Silent, fast, and reliable access for Nextcloud, Immich, and Jellyfin libraries. |
| **Surveillance Footage** | 2× **2TB** HDD | High-capacity, continuous-write optimization for 24/7 CCTV recording. |

> 💡 **Total Usable Storage:** ~5TB across three fully redundant arrays.

### 🌐 Network Topology: Streamlined MikroTik Setup
*A professional-grade network with minimal hardware, maximum performance, fully wired with Cat 6a.*

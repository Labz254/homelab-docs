---
title: phases/06-essential-desktop-apps-drivers
description: Installing essential hardware drivers (NVIDIA/Intel), media codecs, and core desktop applications for daily use on the Proxmox host.
published: true
date: 2026-10-02T19:56:28.810Z
tags: phase-6, desktop, drivers, nvidia, intel, applications, proxmox
editor: markdown
dateCreated: 2026-10-02T19:56:28.810Z
---

# 🖱️ Phase 6 - Essential Desktop Applications & Drivers

> *"A hypervisor with a desktop bolted on is only as good as what runs on top of it. This phase turns a bare GNOME install into an actual daily-driver workstation."*

Up to this point, the host has been treated purely as infrastructure: a hardened, hypervisor-first machine with a graphical session layered on top for convenience. Phase 6 shifts focus to usability. The goal here is to take that bare GNOME desktop and equip it with the hardware support and software tooling needed to use this machine comfortably day-to-day — whether that's editing video, writing code, or simply keeping an eye on system load while VMs and containers are running in the background.

This phase is intentionally split into two broad categories — **drivers** and **applications** — each covered at a high level here, with a dedicated, in-depth sub-phase for every individual item. This page exists as the index and rationale for that split; it does not walk through installation commands itself.

---

## 🎯 Objectives

- Ensure the GPU is properly recognized and accelerated, rather than falling back to an unaccelerated generic driver.
- Equip the desktop with a focused, practical application set rather than a bloated default install.
- Keep each tool's setup documented independently, so any one of them can be reinstalled, upgraded, or troubleshot without needing to re-read this entire phase.

---

## 🎮 Drivers

A desktop environment is only as responsive as the hardware driving it. Running GNOME on top of a generic or fallback driver leads to sluggish rendering, poor video playback performance, and — on a machine that's also expected to handle encoding or any GPU-accelerated workload — a significant waste of available hardware capability.

- **6a – GPU Drivers**: Identifying the installed GPU, installing the correct vendor driver (e.g. NVIDIA's proprietary driver), and verifying that hardware acceleration is actually active rather than assumed.

---

## 🧰 Applications

The application set installed here is deliberately narrow — each tool was chosen to serve a specific, recurring need rather than to fill out a generic "desktop essentials" list:

- **6b – Shutter Encoder**: A free, GPU-aware video encoding and conversion tool, useful for transcoding media without relying on a separate machine or cloud service.
- **6c – VLC**: The de facto standard media player, included for its near-universal format support and reliability when verifying media files or recordings produced elsewhere in the homelab.
- **6d – Visual Studio Code**: The primary development environment for any scripting, automation, or configuration work done directly on this host going forward.
- **6e – btop**: A modern, resource-efficient terminal monitor for keeping a constant eye on CPU, memory, disk, and network usage — especially valuable on a machine simultaneously running VMs, containers, and a desktop session.

---

## 🧩 How This Phase Is Structured

Each sub-phase (6a through 6e) will independently cover:

1. Confirming the relevant hardware or prerequisite.
2. Installing the driver or application.
3. Verifying the install actually works as expected, not just that it completed without error.
4. Any troubleshooting specific to that component.

This keeps the documentation modular — revisiting "just the GPU driver" or "just VS Code" later won't require wading through unrelated steps.

---

## 🎉 Phase Overview Complete!

With the scope and rationale defined, the following sub-phases will handle the actual implementation, one component at a time, starting with the GPU driver.

---

*Next Step: Phase 6a - GPU Drivers*
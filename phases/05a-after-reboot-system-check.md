---
title: Phase 5a - After Reboot System Check
description: Verifying kernel, services, storage, memory, and the Proxmox web UI after the GNOME install reboot.
published: true
date: 2026-10-02T17:10:15.514Z
tags: phase-5, phase-5.1, system-check, proxmox, verification
editor: markdown
dateCreated: 2026-10-02T16:58:00.744Z
---

# ✅ Phase 5a - After Reboot System Check

> *"Before we touch storage, networking, or security — confirm the box actually came back up healthy."*

This is the first sub-phase of Phase 5. After rebooting into the GNOME desktop, we need to confirm the kernel, system services, storage, memory, and Proxmox itself all survived the reboot cleanly before moving on to the remaining sub-phases (network configuration, storage setup, security hardening, and more). All commands below are run as the `cannz` user with `sudo`, not as `root`.

---

## 💾 Step 1: Create an Initial Backup of Configuration (Recommended)

Before installing additional software, it's useful to save copies of important configuration files:

```bash
sudo mkdir -p /root/config-backup
sudo cp /etc/hosts /root/config-backup/
sudo cp /etc/network/interfaces /root/config-backup/
sudo cp /etc/hostname /root/config-backup/
```

💡 **Why?** This provides a simple reference if you need to compare or restore configurations later.

---

## 🧬 Step 2: Verify the Installed Kernel

Display the running kernel:

```bash
uname -r
```

The version should correspond to the latest Proxmox kernel installed by the upgrade.

---

## 🩺 Step 3: Check System Status

Verify that all systemd services are healthy:

```bash
systemctl --failed
```

**Expected Output:**

```text
0 loaded units listed.
```

If failed services are listed, investigate them before proceeding.

---

## 📀 Step 4: Check Disk Usage

Verify filesystem usage:

```bash
df -h
```

Confirm:

- The root filesystem is mounted.
- There is sufficient free space.
- The 1 TB HDD has not been altered if you intentionally left it unused.

---

## 🧠 Step 5: Check Memory

Display memory usage:

```bash
free -h
```

Confirm that the system recognizes approximately 16 GB of RAM.

---

## 🌐 Step 6: Verify the Web Interface

From another computer, open:

```text
https://<your-server-ip>:8006
```

Log in as `cannz`.

Verify:

- Dashboard loads.
- Storage is visible.
- Node status is healthy.
- No critical alerts are present.

---

## 🖧 Step 7: Verify Hostname and IP Address

1. Check the hostname:

```bash
hostnamectl
```

Verify that the hostname matches what you configured during installation.

2. Check the IP address:

```bash
ip addr show
```

Confirm that the management interface has the expected IP address.

---

## 📡 Step 8: Check Network Connectivity

1. Check network connectivity:

```bash
ping -c 4 1.1.1.1
```

If successful, you should receive replies.

2. Then verify DNS resolution:

```bash
ping -c 4 google.com
```

If this succeeds, both network connectivity and DNS are functioning correctly.

---

## 🚨 Step 9: Recheck Proxmox Services

Confirm that Proxmox services are still running after the reboot.

Check the API service:

```bash
sudo systemctl status pveproxy
```

**Expected:**

```text
Active: active (running)
```

Check the cluster service:

```bash
sudo systemctl status pve-cluster
```

**Expected:**

```text
Active: active (running)
```

Check the daemon:

```bash
sudo systemctl status pvedaemon
```

**Expected:**

```text
Active: active (running)
```

If any of these services are inactive or failed, resolve the issue before continuing.

---

## 🎉 Sub-Phase Complete!

Your Proxmox host has been verified as healthy after the reboot — kernel, services, storage, memory, networking, and the Proxmox web UI are all confirmed working.

---

*Next Step: [Phase 5b - Network Configuration and Connectivity Tests](/phases/05b-network-config)*

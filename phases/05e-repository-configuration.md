---
title: Phase 5e - Repository Configuration
description: Configuring Proxmox and Debian repositories, disabling the paid enterprise repo, and enabling the free no-subscription repo.
published: true
date: 2026-10-02T19:22:34.259Z
tags: phase-5, phase-5e, repositories, apt, proxmox, configuration, debian
editor: markdown
dateCreated: 2026-10-02T19:22:34.259Z
---

# 📦 Phase 5e - Repository Configuration

> *"Free Proxmox shouldn't be pointing at a paid repo it can't authenticate against. Let's fix that properly."*

By default, Proxmox VE points at the paid Enterprise repository (and, if you ever enable Ceph, the Enterprise Ceph repo too). Since we're running the free, no-subscription version, both need to be disabled and replaced with their No-Subscription equivalents. This sub-phase uses the newer deb822 `.sources` format introduced with Proxmox VE 9.x / Debian trixie. All commands are run as `cannz` with `sudo`.

---

## 🚫 Step 1: Disable the Enterprise Repositories

```bash
sudo mv /etc/apt/sources.list.d/pve-enterprise.sources \
  /etc/apt/sources.list.d/pve-enterprise.sources.disabled

sudo mv /etc/apt/sources.list.d/ceph.sources \
  /etc/apt/sources.list.d/ceph.sources.disabled
```

This renames both files with a `.disabled` suffix so `apt` ignores them, without deleting anything.

---

## 📝 Step 2: Create the Proxmox No-Subscription Repository

```bash
sudo nano /etc/apt/sources.list.d/proxmox.sources
```

Paste:

```text
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
```

Save (`Ctrl+O`, Enter) and exit (`Ctrl+X`).

---

## 🐙 Step 3: Add the Ceph Repository (Optional)

Only do this if you actually use Ceph.

```bash
sudo nano /etc/apt/sources.list.d/ceph.list
```

Add:

```text
deb http://download.proxmox.com/debian/ceph-squid trixie no-subscription
```

If you don't use Ceph, you can simply leave the Ceph repository disabled (Step 1 already moved it out of the way).

---

## 🔄 Step 4: Update Package Lists and Verify

```bash
sudo apt update
```

**Expected Output:** No "No repository defined" or authentication errors, and the `pve-no-subscription` source listed among the repositories `apt` pulls from.

---

## ♻️ Recovery

If anything goes wrong with the repository change, you can simply restore the original files:

```bash
sudo rm /etc/apt/sources.list.d/proxmox.sources
sudo mv /etc/apt/sources.list.d/pve-enterprise.sources.disabled /etc/apt/sources.list.d/pve-enterprise.sources
sudo mv /etc/apt/sources.list.d/ceph.sources.disabled /etc/apt/sources.list.d/ceph.sources
```

---

## 🎉 Sub-Phase Complete!

The host now pulls updates from the free No-Subscription repository instead of the Enterprise repository, with a clean rollback path if anything needs to be undone.

---

*Next Step: Phase 5f - Storage Setup (ZFS RAID 1 for the remaining drives)*
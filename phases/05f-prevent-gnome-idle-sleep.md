---
title: Phase 5f - Preventing GNOME/GDM Idle Sleep
description: Disabling automatic screen blanking, sleep, and suspend features in GNOME and GDM to ensure the Proxmox host remains always-on.
published: true
date: 2026-10-02T19:30:21.296Z
tags: phase-5, phase-5f, gnome, gdm, power-management, sleep, proxmox, always-on
editor: markdown
dateCreated: 2026-10-02T19:30:21.296Z
---

# 🖥️ Phase 5f - Preventing GNOME/GDM Idle Sleep

> *"A homelab display that blanks out and refuses to wake is worse than no display at all. Let's stop it from sleeping, in every session that matters."*

GNOME can suspend or blank the screen from two separate places: your logged-in user session, and the GDM login screen itself (which runs as its own `Debian-gdm` session before you ever log in). Fixing only one of the two often leaves the problem half-solved. All commands below are run as `cannz` unless they specifically target the `Debian-gdm` user.

---

## 👤 Step 1: Check Your Logged-In GNOME Session

```bash
gsettings get org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type
gsettings get org.gnome.desktop.session idle-delay
```

**Expected:**

```text
'nothing'
uint32 0
```

If not, fix them:

```bash
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.desktop.session idle-delay 0
gsettings set org.gnome.settings-daemon.plugins.power idle-dim false
```

---

## 🔐 Step 2: Check the GDM Login Screen Settings

> ✅ **This was the issue in your case.** The logged-in session can be configured correctly while the GDM login screen (which runs before any user logs in) still has its own, separate power settings causing it to sleep.

Verify:

```bash
sudo -u Debian-gdm dbus-run-session gsettings get org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type
sudo -u Debian-gdm dbus-run-session gsettings get org.gnome.desktop.session idle-delay
```

**Expected:**

```text
'nothing'
uint32 0
```

If needed, fix them:

```bash
sudo -u Debian-gdm dbus-run-session gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
sudo -u Debian-gdm dbus-run-session gsettings set org.gnome.desktop.session idle-delay 0
sudo -u Debian-gdm dbus-run-session gsettings set org.gnome.settings-daemon.plugins.power idle-dim false
```

---

## ⚙️ Step 3: Verify systemd-logind

```bash
grep -v '^#' /etc/systemd/logind.conf | sed '/^$/d'
```

There should be no suspend-related options forcing sleep.

---

## 📜 Step 4: Check Whether the System Actually Suspended

```bash
sudo journalctl -b | grep -Ei "suspend|sleep|hibernate"
```

If nothing relevant appears, the system probably did not suspend.

---

## 🎉 Sub-Phase Complete!

Both the logged-in GNOME session and the GDM login screen are now confirmed to have idle-sleep and screen-blanking disabled, and `systemd-logind` has no conflicting suspend settings.

---

*Next Step: Phase 5g - Storage Setup (ZFS RAID 1 for the remaining drives)*
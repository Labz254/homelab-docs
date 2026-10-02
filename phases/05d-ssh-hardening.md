---
title: Phase 5d - SSH Hardening
description: Securing SSH with key-based authentication, disabling root login, and changing the default port.
published: true
date: 2026-10-02T19:13:22.815Z
tags: phase-5, phase-5d, ssh, security, hardening, proxmox
editor: markdown
dateCreated: 2026-10-02T19:10:59.612Z
---

# 🔑 Phase 5d - SSH Hardening

> *"SSH on port 22 with password auth and root login enabled is the first thing any scanner finds. Let's close that door properly."*

This sub-phase moves SSH access from password-based `root` login to key-based authentication under `cannz`, disables root SSH login entirely, and moves SSH off the default port. All commands on the server are run as `cannz` with `sudo` unless noted as a client-side command.

> 🚨 **CRITICAL WARNING:** Do not disable password authentication or close your current SSH session until you've confirmed key-based login works on a **separate, second session**. If you lock yourself out, you'll need console/physical access to recover.

---

## 🧰 Step 1: Generate an SSH Key Pair (on your client machine)

On the computer you'll be connecting **from** (not the Proxmox host), generate a key pair if you don't already have one:

```bash
ssh-keygen -t ed25519 -C "cannz@homelab"
```

Press Enter to accept the default save location, and set a passphrase when prompted (recommended).

---

## 📤 Step 2: Copy the Public Key to the Server

From your client machine:

```bash
ssh-copy-id cannz@<your-server-ip>
```

> ⚠️ **Troubleshooting: "ssh-copy-id: command not found (e.g. on Windows)"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> Copy the key manually instead. On the client, display your public key:
> <br><br>
> **Fix:**
> ```bash
> cat ~/.ssh/id_ed25519.pub
> ```
> Then on the server, append it to the authorized keys file:
> ```bash
> mkdir -p ~/.ssh
> echo "<paste-your-public-key-here>" >> ~/.ssh/authorized_keys
> chmod 700 ~/.ssh
> chmod 600 ~/.ssh/authorized_keys
> ```
> </details>

---

## ✅ Step 3: Test Key-Based Login

**In a new, separate terminal** (keep your current session open), test logging in with the key:

```bash
ssh cannz@<your-server-ip>
```

**Expected:** You log in without being prompted for `cannz`'s password (only your key's passphrase, if set).

Do not continue to the next step until this works.

---

## 💾 Step 4: Back Up the SSH Config Before Editing

On the server:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

---

## ✏️ Step 5: Edit the SSH Daemon Configuration

```bash
sudo nano /etc/ssh/sshd_config
```

Set or update the following:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
Port 2222
```

**Explanation:**

- `PermitRootLogin no` — root can no longer SSH in directly; use `cannz` + `sudo` instead
- `PasswordAuthentication no` — disables password login entirely; key-based auth only
- `Port 2222` — moves SSH off the default port 22 (choose any unused port above 1024; `2222` is an example)

> 💡 **Tip:** Avoid well-known alternate ports (2222, 2200) if you want to minimize automated scans even further — any free high port works.

---

## 🔍 Step 6: Validate the Configuration Syntax

Before restarting, check for syntax errors:

```bash
sudo sshd -t
```

**Expected Output:** No output means the config is valid. Any error must be fixed before proceeding.

---

## 🛡️ Step 7: Update the Firewall for the New SSH Port

Since Phase 5c's firewall rules only allow port `22`, update them to match the new port — otherwise you'll lock yourself out.

```bash
sudo nano /etc/pve/nodes/$(hostname)/host.fw
```

Replace:

```text
IN ACCEPT -p tcp -dport 22
```

With:

```text
IN ACCEPT -p tcp -dport 2222
```

---

## 🔄 Step 8: Restart the SSH Service

```bash
sudo systemctl restart sshd
```

---

## 📡 Step 9: Test Before Closing Your Original Session

**In a new, separate terminal**, confirm you can still connect on the new port with your key:

```bash
ssh -p 2222 cannz@<your-server-ip>
```

Only once this succeeds should you close your original session.

> ⚠️ **Troubleshooting: "Connection refused or times out after restart?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> If you still have your original session open, use it to recover:
> <br><br>
> **Fix:**
> ```bash
> sudo cp /etc/ssh/sshd_config.bak /etc/ssh/sshd_config
> sudo systemctl restart sshd
> ```
> This restores the previous working configuration. Re-check the firewall rule and `sshd_config` edits, then retry.
> <br><br>
> If you have no sessions open at all, you'll need console/physical access (keyboard + monitor, or the Proxmox host console) to fix `/etc/ssh/sshd_config` and `/etc/pve/nodes/<node>/host.fw` directly.
> </details>

---

## 🎉 Sub-Phase Complete!

SSH now requires key-based authentication, root login is disabled, and the service runs on a non-default port — all verified working from a second session before anything disruptive was finalized.

---

*Next Step: Phase 5e - Storage Setup (ZFS RAID 1 for the remaining drives)*
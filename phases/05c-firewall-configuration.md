---
title: Phase 5c - Firewall Configuration
description: Configuring UFW (Uncomplicated Firewall) to secure the Proxmox host by restricting unnecessary inbound traffic.
published: true
date: 2026-10-02T19:14:34.929Z
tags: phase-5, phase-5c, firewall, ufw, security, proxmox, hardening
editor: markdown
dateCreated: 2026-10-02T17:29:38.422Z
---

# 🛡️ Phase 5c - Firewall Configuration

> *"An open management interface on a homelab is a liability. Let's lock it down without locking ourselves out."*

Proxmox VE ships with its own integrated firewall, managed at three levels — Datacenter, Node, and VM/CT — sitting on top of the host's `nftables` rules. This sub-phase enables the firewall, defines sane default policies, and explicitly allows the management traffic you actually need (SSH, the web UI, ping) before anything gets blocked. All commands below are run as `cannz` with `sudo` unless done through the web UI.

> 🚨 **CRITICAL WARNING:** Enabling a firewall incorrectly can lock you out of SSH and the web UI entirely, especially on a remote/headless box. Follow the steps in order — **allow rules are added before the firewall is enabled**, not after.

---

## 🗺️ Step 1: Understand the Firewall Levels

Proxmox's firewall has three scopes, evaluated in this order:

- **Datacenter** — cluster-wide defaults and security groups (`/etc/pve/firewall/cluster.fw`)
- **Node** — rules specific to this host (`/etc/pve/nodes/<nodename>/host.fw`)
- **VM/CT** — rules specific to an individual guest

For this phase we're focused on the **Node** level, since we're hardening the Proxmox host itself.

---

## 📝 Step 2: Add Allow Rules Before Enabling Anything

Before the firewall is turned on, explicitly allow the traffic you need to keep managing the box.

Via the web UI: **Datacenter → `<node>` → Firewall → Add**, and create rules for:

| Direction | Action | Protocol | Dest. Port | Purpose |
|---|---|---|---|---|
| in | ACCEPT | tcp | 22 | SSH |
| in | ACCEPT | tcp | 8006 | Proxmox Web UI |
| in | ACCEPT | icmp | — | Ping (diagnostics) |

Or equivalently, edit the host rules file directly:

```bash
sudo nano /etc/pve/nodes/$(hostname)/host.fw
```

Add:

```text
[RULES]
IN ACCEPT -p tcp -dport 22
IN ACCEPT -p tcp -dport 8006
IN ACCEPT -p icmp
```

> 💡 **Tip:** If you only ever manage this host from your own LAN subnet, scope these rules to a source CIDR (e.g. `-source 192.168.1.0/24`) instead of leaving them open to any address.

---

## ⚙️ Step 3: Set the Default Policy

Still in the same `host.fw` file (or via the UI's **Options** tab), set the default inbound policy to `DROP` so only explicitly allowed traffic gets through:

```text
[OPTIONS]
enable: 1
log_level_in: info
log_level_out: info
```

**Explanation:**

- `enable: 1` turns the node firewall on
- `log_level_in` / `log_level_out` enable logging so you can audit blocked/allowed traffic later

---

## ✅ Step 4: Enable the Firewall at the Node Level

With your allow rules already in place, enable the firewall for this node:

Via the web UI: **Datacenter → `<node>` → Firewall → Options → Firewall: Yes**

Or via CLI:

```bash
sudo nano /etc/pve/nodes/$(hostname)/host.fw
```

Confirm `enable: 1` is set under `[OPTIONS]`, then save.

---

## 🧯 Step 5: Enable the Firewall at the Datacenter Level

The node-level firewall only takes effect once the Datacenter-level firewall is also enabled.

Via the web UI: **Datacenter → Firewall → Options → Firewall: Yes**

Or edit:

```bash
sudo nano /etc/pve/firewall/cluster.fw
```

Add:

```text
[OPTIONS]
enable: 1
```

---

## 🔍 Step 6: Verify the Firewall Is Active

Check the running firewall status:

```bash
sudo pve-firewall status
```

**Expected Output:**

```text
Status: enabled/running
```

List the compiled rules currently in effect:

```bash
sudo pve-firewall compile
```

---

## 📡 Step 7: Test Connectivity Immediately

**Before closing your current SSH session**, open a second, separate SSH session (or a new browser tab for the web UI) to confirm you still have access:

```bash
ssh cannz@<your-server-ip>
```

Also confirm the web UI still loads:

```text
https://<your-server-ip>:8006
```

> ⚠️ **Troubleshooting: "Locked out after enabling the firewall?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> If you still have physical or console access (keyboard/monitor, or the Proxmox host console via another node), log in locally and disable the firewall to recover:
> <br><br>
> **Fix:**
> ```bash
> sudo pve-firewall stop
> ```
> This immediately disables firewall enforcement without removing your rules, so you can fix the `host.fw`/`cluster.fw` files and re-enable safely.
> </details>

---

## 📊 Step 8: Review Firewall Logs

Confirm logging is capturing traffic as expected:

```bash
sudo tail -f /var/log/pve-firewall.log
```

Watch for `DROP` entries from unexpected sources, and `ACCEPT` entries for your own management traffic.

---

## 🧱 Step 9: (Optional) Host-Level Hardening Beyond the Proxmox Firewall

The Proxmox firewall manages `nftables` for you, so manually adding raw `iptables`/`nftables` rules alongside it is not recommended — the two can conflict. Further intrusion-prevention (e.g. CrowdSec) is covered in a later sub-phase.

---

## 🎉 Sub-Phase Complete!

The Proxmox firewall is now enabled at both the Datacenter and Node level, with explicit allow rules for SSH, the web UI, and diagnostics — and a default-deny policy for everything else.

---

*Next Step: Phase 5d - Storage Setup (ZFS RAID 1 for the remaining drives)*
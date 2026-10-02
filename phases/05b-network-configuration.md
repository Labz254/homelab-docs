---
title: Phase 5b - Network Configuration & Connectivity Tests
description: Configuring a static IP, setting up reliable DNS, and verifying network connectivity on the Proxmox host.
published: true
date: 2026-10-02T17:21:59.267Z
tags: phase-5, phase-5b, network, proxmox, configuration, dns
editor: markdown
dateCreated: 2026-10-02T17:21:59.267Z
---

# 🌐 Phase 5b - Network Configuration and Connectivity Tests

> *"The box is up — now let's make sure it talks to the rest of the network reliably, not just by accident."*

During Phase 2 we assigned a static IP, gateway, and DNS during the Proxmox installer. This sub-phase goes beyond that initial setup: we review the actual network configuration file, confirm the bridge interface is correct, (optionally) tune DNS and VLANs, and run a full set of connectivity tests so networking issues don't surface later while you're mid-way through a storage or security change. All commands below are run as `cannz` with `sudo`.

---

## 📄 Step 1: Review the Current Network Configuration

Proxmox manages networking via a standard Debian-style interfaces file. Review it before changing anything:

```bash
cat /etc/network/interfaces
```

Confirm you can see:

- A physical interface (e.g. `eno1` or `enp2s0`) set to `manual`
- A bridge (e.g. `vmbr0`) with your static IP, netmask/CIDR, and gateway
- The physical interface listed as a `bridge-ports` member of that bridge

---

## 🔌 Step 2: Verify Active Interfaces

Check which interfaces are up and their assigned addresses:

```bash
ip addr show
```

Then check link state specifically:

```bash
ip link show
```

**Expected:** Your bridge (e.g. `vmbr0`) shows `state UP` with the static IP you configured during installation.

---

## 🌉 Step 3: Verify the Bridge (vmbr0) Configuration

Confirm the bridge is correctly passing traffic through the physical NIC:

```bash
brctl show vmbr0
```

**Expected Output:** Your physical interface (e.g. `eno1`) listed as an interface under `vmbr0`.

> ⚠️ **Troubleshooting: "brctl: command not found"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> The `bridge-utils` package isn't installed by default on newer Proxmox/Debian releases.
> <br><br>
> **Fix:**
> ```bash
> sudo apt install bridge-utils
> ```
> Alternatively, use the modern replacement without installing anything extra:
> ```bash
> ip link show type bridge
> bridge link show
> ```
> </details>

---

## 🧭 Step 4: Confirm the Default Gateway

Verify the system knows how to reach the rest of your network:

```bash
ip route show
```

**Expected Output:** A `default via <gateway-ip>` line pointing to your router (e.g. your MikroTik RB5009 at `192.168.1.1`).

---

## 🗂️ Step 5: Verify DNS Resolution Configuration

Check which DNS servers the system is using:

```bash
cat /etc/resolv.conf
```

**Expected:** Your configured DNS server(s) (e.g. `1.1.1.1`, `8.8.8.8`, or your internal AdGuard Home IP).

If you need to change DNS servers, edit this file or, if you're using `resolvconf`, update it there instead so changes persist across reboots:

```bash
sudo nano /etc/resolv.conf
```

---

## 🔄 Step 6: Restart Networking and Verify

After any configuration change, apply it without a full reboot:

```bash
sudo ifreload -a
```

Then re-verify the interfaces came back up correctly:

```bash
ip addr show
```

> ⚠️ **Troubleshooting: "ifreload: command not found"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> `ifreload` comes from the `ifupdown2` package, which Proxmox uses by default. If it's missing:
> <br><br>
> **Fix:**
> ```bash
> sudo apt install ifupdown2
> ```
> As a fallback, you can bring interfaces down and up manually, though this is more disruptive:
> ```bash
> sudo ifdown vmbr0 && sudo ifup vmbr0
> ```
> </details>

---

## 📶 Step 7: Test Local Network Connectivity

Ping your gateway to confirm basic LAN connectivity:

```bash
ping -c 4 192.168.1.1
```

If successful, you should receive replies with low latency.

---

## 🌍 Step 8: Test Internet Connectivity

Ping a public IP to rule out DNS issues:

```bash
ping -c 4 1.1.1.1
```

Then confirm DNS resolution works:

```bash
ping -c 4 google.com
```

If both succeed, internet connectivity and DNS are functioning correctly.

---

## 🔎 Step 9: Verify DNS Resolution in Detail

For a more thorough DNS check:

```bash
dig google.com
```

**Expected Output:** An `ANSWER SECTION` with a resolved IP address and a reasonable query time.

---

## 🖥️ Step 10: Confirm the Web UI Is Reachable Over the Network

From another device on the same network, open:

```text
https://<your-server-ip>:8006
```

**Expected:** The Proxmox login page loads without timing out, confirming the bridge, firewall (if enabled), and routing are all working together correctly.

---

## 🎉 Sub-Phase Complete!

Networking has been reviewed and verified end-to-end: the bridge, gateway, DNS, and both LAN and internet connectivity are all confirmed working.

---

*Next Step: Phase 5c - Storage Setup (ZFS RAID 1 for the remaining drives)*
---
title: Phase 2 - Proxmox Installation
description: Creating a bootable USB with Ventoy and installing Proxmox VE with a ZFS RAID 1 array.
published: true
date: 2026-09-28T14:18:14.926Z
tags: phase-2, proxmox, installation, ventoy, zfs, raid1
editor: markdown
dateCreated: 2026-09-28T14:18:14.926Z
---

# 🐧 Phase 2 - Proxmox Installation

> *"From bare metal to hypervisor. Let's get the foundation running."*

With the BIOS updated and configured, we are ready to install Proxmox Virtual Environment (VE). This phase covers downloading the ISO, creating a bootable USB using Ventoy, and walking through the installation with a specific focus on setting up our primary ZFS RAID 1 array for the OS.

---

## 📥 Step 1: Download the Proxmox VE ISO

1. Navigate to the official [Proxmox VE Download Page](https://www.proxmox.com/en/downloads).
2. Click on **Proxmox VE** and download the latest stable ISO (e.g., `proxmox-ve_8.x-x.iso`).
3. *(Optional but recommended)* Verify the SHA256 checksum of the downloaded file to ensure it was not corrupted during download.

---

## 💾 Step 2: Create a Bootable USB with Ventoy

Ventoy is the best tool for this because it allows you to simply drag and drop ISO files onto the USB drive without reformatting every time.

1. Download the latest version of [Ventoy](https://www.ventoy.net/en/download.html) for your current OS (Windows or Linux).
2. Insert a USB flash drive (at least 8GB). **Warning: This will erase all data on the drive.**
3. Run the Ventoy installer and click **Install**. Confirm the warnings.
4. Once installed, the USB drive will appear as a normal storage drive named "Ventoy".
5. Simply **copy and paste** the downloaded Proxmox VE `.iso` file directly onto the Ventoy USB drive.

> ️ **Troubleshooting: "Ventoy fails to install or format the USB?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> This usually happens if the USB drive has a corrupted partition table or is write-protected.
> <br><br>
> **Fix:** Open Windows Disk Management (or `lsblk`/`fdisk` in Linux), delete all existing partitions on the USB drive until it shows as "Unallocated Space", and try the Ventoy installation again.
> </details>

---

## 💻 Step 3: Booting the Installer

1. Insert the Ventoy USB drive into the HP Z2 G9 Workstation.
2. Power on the machine and immediately press **F9** repeatedly to open the Boot Menu.
3. Select your USB drive from the list (it may be listed as "UEFI: [USB Brand Name]").
4. The Ventoy menu will appear. Use the arrow keys to select the **Proxmox VE ISO** and press Enter.
5. Select **Install Proxmox VE** and press Enter.

---

## ⚙️ Step 4: The Installation Walkthrough

Follow the on-screen prompts carefully. Pay special attention to **Step 4.3 (Target Harddisk)**.

### 4.1 EULA and Location
- Read and accept the End User License Agreement (EULA).
- Select your **Country**, **Time Zone**, and **Keyboard Layout**.

### 4.2 Password and Email
- **Password:** Create a strong password for the `root` user. *(Save this securely!)*
- **Email:** Enter your email address (used for system notifications and Let's Encrypt certificates later).

### 4.3 Target Harddisk (CRITICAL STEP) 🚨
This is where we set up the redundancy for the Proxmox OS using your two 1TB NVMe drives.

1. Click the **Options** button next to the Target Harddisk dropdown.
2. **Filesystem:** Select **zfs (RAID1)**.
3. **Hard Disks:** Hold `Ctrl` (or `Cmd` on Mac) and select **both** of your 1TB NVMe Gen 4 drives.
4. **Ashift:** Leave at `12` (default for modern NVMe/SSD drives).
5. Click **OK**.
6. Verify that the dropdown now shows something like `rpool` with both NVMe drives listed and `zfs (RAID1)` as the filesystem.

> ⚠️ **Troubleshooting: "No hard disks found or only one disk showing?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> If Proxmox cannot see your NVMe drives, the BIOS storage controller is likely set to "RAID" or "Intel RST" instead of "AHCI".
> <br><br>
> **Fix:** Reboot, press **F10** to enter BIOS, go to **Advanced > Storage Options**, change the mode to **AHCI**, save, and reboot the installer.
> </details>

### 4.4 Network Configuration
- **Hostname:** Give your server a Fully Qualified Domain Name (FQDN), e.g., `pve.labz254.local` or `storage.labz254.local`.
- **IP Address:** Assign a **Static IP** address (e.g., `192.168.1.100`). *Do not use DHCP for a hypervisor.*
- **CIDR:** Usually `24` (which equals a `255.255.255.0` subnet mask).
- **Gateway:** Your router's IP (e.g., `192.168.1.1` - your MikroTik RB5009).
- **DNS Server:** `1.1.1.1` or `8.8.8.8` (or your internal AdGuard Home IP if already configured).

### 4.5 Review and Install
- Review the summary screen carefully. Ensure the Target Harddisk shows **RAID1** and the correct IP.
- Click **Install**.
- Wait for the installation to reach 100%. This will format the drives, install the OS, and configure the bootloader.

---

## 🎉 Step 5: First Boot

1. Once the installation is complete, click **Reboot**.
2. **Remove the USB drive** when prompted.
3. The system will boot into the Proxmox VE console. You will see a login prompt and the URL to access the web interface (e.g., `https://192.168.1.100:8006`).

**Congratulations!** Proxmox VE is now installed with a redundant ZFS RAID 1 OS array. 

---

*Next Step: [Phase 3 - Creating a Secure Non-Root Admin User](/phases/03-admin-user-setup)*
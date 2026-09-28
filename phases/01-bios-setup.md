---
title: Phase 1 - BIOS Setup & Preparation
description: Updating the HP Z2 G9 BIOS and configuring hardware settings for Proxmox VE virtualization.
published: true
date: 2026-09-28T14:04:05.004Z
tags: phase-1, bios, hp-z2-g9, proxmox, hardware, setup
editor: markdown
dateCreated: 2026-09-28T13:56:17.414Z
---

# ️ Phase 1: BIOS Setup & Preparation

> *"A solid foundation prevents a collapsed house. We start at the bare metal."*

Before installing Proxmox VE, we must ensure the HP Z2 G9 Workstation is running the latest BIOS firmware and is configured specifically for Type-1 hypervisor operations. This phase covers updating the BIOS and applying the critical hardware settings required for virtualization, ZFS storage, and future PCIe passthrough.

---

## 📥 Step 1: Updating the BIOS to the Latest Version

HP frequently releases BIOS updates that improve thermal management, memory compatibility, and virtualization stability. 

### 1.1 Download the BIOS Update
1. Go to the [HP Customer Support - Software and Driver Downloads](https://support.hp.com/us-en/drivers) page.
2. Enter your serial number or select **Workstations** -> **HP Z2 G9 Tower Workstation PC**.
3. Select your Operating System (choose Windows 10/11 64-bit just to get the list, the BIOS file is OS-independent).
4. Expand the **BIOS** section and download the latest **HP Z2 G9 Workstation System BIOS Update**.

### 1.2 Prepare the USB Drive
1. Format a USB flash drive to **FAT32**.
2. Extract the downloaded `.exe` file. Inside, you will find a `.bin` file (e.g., `Q70_010500.bin`) and a `Flash64W.exe` (or similar HP flash utility).
3. Copy both the `.bin` file and the flash utility to the root of the FAT32 USB drive.

### 1.3 Flash the BIOS
1. Insert the USB drive into the HP Z2 G9.
2. Power on the workstation and immediately press **F10** repeatedly to enter the BIOS Setup.
3. Navigate to the **Main** tab -> **Flash System ROM**.
4. Select the USB drive and choose the `.bin` file.
5. Follow the on-screen prompts. **Do not turn off the power during this process.** The system will reboot automatically when finished.

> ⚠️ **Troubleshooting: "Flash System ROM option missing or grayed out?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> HP Z-series workstations sometimes restrict BIOS flashing if an Administrator Password is not set, or if Secure Boot is interfering.
> <br><br>
> **Fix 1:** Go to the **Security** tab -> Set an **Administrator Password**. Save and reboot into BIOS. The flash option should now be available.
> <br><br>
> **Fix 2:** If it still fails, download the **HP BIOS Configuration Utility (BCU)** or use the **HP Image Assistant** from within a Windows environment to flash the BIOS via the OS instead.
> </details>

---

##  Step 2: Navigating the HP Z2 G9 BIOS

The HP Z2 G9 uses a modern, mouse-enabled GUI BIOS. 
- **Enter BIOS:** Press **F10** at startup.
- **Boot Menu (One-time):** Press **F9** at startup.
- **Navigation:** Use the arrow keys or your mouse to navigate the top tabs: **Main**, **Security**, **Advanced**, **Power**, and **Boot**.

---

## ⚙️ Step 3: Critical Settings for Proxmox VE

Apply the following settings to prepare the hardware for Proxmox. 

### 3.1 Enable Virtualization (Crucial)
Proxmox relies entirely on hardware virtualization. Without this, no VMs will start.

1. Go to the **Advanced** tab -> **System Options**.
2. Set **Intel Virtualization Technology (VT-x)** to `Enabled`.
3. Set **Intel VT-d** (Virtualization Technology for Directed I/O) to `Enabled`.
   - *Note: VT-d is required if you ever plan to pass through hardware (like the NVIDIA GPU or network cards) to specific VMs.*

> ⚠️ **Troubleshooting: "VT-x or VT-d options are missing?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> On some HP BIOS versions, advanced CPU features are hidden by default.
> <br><br>
> **Fix:** Go to the **Security** tab -> **System Security**. Ensure **Virtualization Technology (VTx)** is checked. If it's still missing, you may need to set an Administrator Password in the Security tab first, which unlocks hidden advanced menus.
> </details>

### 3.2 Disable Secure Boot
While modern Debian/Proxmox supports Secure Boot, it often causes headaches with third-party kernel modules (like NVIDIA drivers or specific network card drivers) later on. It is highly recommended to disable it for a homelab.

1. Go to the **Security** tab -> **Secure Boot Configuration**.
2. Set **Secure Boot** to `Disabled`.
3. Set **Legacy Support** to `Disabled` (We want pure UEFI mode).

### 3.3 Configure Storage for ZFS (AHCI Mode)
Proxmox uses ZFS for its storage engine. ZFS requires direct access to the physical drives. If the BIOS is set to "RAID" or "Intel RST", Proxmox will not see the individual NVMe/SSD drives.

1. Go to the **Advanced** tab -> **Boot Options** (or **Storage Options** depending on BIOS version).
2. Locate **SATA Emulation** or **Storage Configuration**.
3. Set the mode to **AHCI**. 
   - *Do not use RAID or Intel RST Premium.*

### 3.4 Disable Fast Boot
Fast Boot skips certain hardware initializations to speed up startup. This can cause network interfaces to not initialize properly for Proxmox, or make it difficult to enter the BIOS.

1. Go to the **Boot** tab -> **Boot Configuration**.
2. Set **Fast Boot** to `Disabled`.

### 3.5 Enable Network Stack (Optional but Recommended)
If you plan to use Wake-on-LAN (WoL) to turn on your server remotely, or PXE boot in the future.

1. Go to the **Advanced** tab -> **Boot Options**.
2. Set **Network (PXE) Boot** to `Enabled`.
3. Go to **Power** tab -> **Hardware Power Management** and enable **Wake on LAN**.

---

## 💾 Step 4: Save and Exit

1. Go to the **Main** tab.
2. Click **Save Changes and Exit**.
3. The system will reboot. 

**Congratulations!** Your HP Z2 G9 is now running the latest firmware and is perfectly configured to host Proxmox VE. 

---

*Next Step: [Phase 2: Proxmox Installation](/phases/02-proxmox-installation)*
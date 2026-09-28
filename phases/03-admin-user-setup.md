---
title: Phase 3 - Creating a Secure Non-Root Admin User
description: Performing a full system update, creating the cannz user, and configuring sudo privileges.
published: true
date: 2026-09-28T14:27:21.253Z
tags: phase-3, admin-user, security, sudo, proxmox
editor: markdown
dateCreated: 2026-09-28T14:27:21.253Z
---

#  Phase 3 - Creating a Secure Non-Root Admin User

> *"Rule #1 of System Administration: Never live as root."*

Running daily tasks or hosting services as the `root` user is a massive security risk. A single typo or compromised service could destroy the entire host. In this phase, we perform a full system update, create a dedicated administrative user (`cannz`), and configure `sudo` privileges.

---

## 🔄 Step 1: Perform a Full System Update

Before creating users or installing new software, ensure your Proxmox host is fully up to date.

1. Log into your Proxmox server via SSH or the web console as `root`.
2. Update the package lists and upgrade all installed packages:
   ```bash
   apt update && apt full-upgrade -y
---
title: Phase 3 - Creating a Secure Non-Root Admin User
description: Performing a full system update, creating the cannz user, and configuring sudo privileges.
published: true
date: 2026-09-28T15:03:21.977Z
tags: phase-3, admin-user, security, sudo, proxmox
editor: markdown
dateCreated: 2026-09-28T14:27:21.253Z
---

# 🔐 Phase 3 - Creating New Admin User

> *"Root is powerful, but it's also dangerous to use daily. Let's create a proper administrative account."*

With Proxmox VE installed and running, we shouldn't operate the system as `root` for day-to-day tasks. This phase covers creating a new non-root user (`cannz`), granting it `sudo` privileges, and verifying that it works correctly both from the shell and the GNOME desktop.

---

## 🧹 Step 1: Clean Up Packages (Optional)

Before proceeding, clean up any unnecessary or orphaned packages:

```bash
apt autoremove
apt clean
```

> ⚠️ **Troubleshooting: "apt update fails with 'No repository defined' or 'pve-enterprise' errors?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> By default, Proxmox is configured to use the paid Enterprise repository. Since we are using the free version, we need to switch to the free "No-Subscription" repository.
> <br><br>
> **Fix:**
> 1. Disable the enterprise repo:
> ```bash
> sed -i 's/^deb/#deb/' /etc/apt/sources.list.d/pve-enterprise.list
> ```
> 2. Add the no-subscription repo:
> ```bash
> echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
> ```
> 3. Run `apt update` again.
> </details>

---

## 🔍 Step 2: Verify the Current User

Before proceeding, confirm you are administering the system as the `root` user.

Run:

```bash
whoami
```

**Expected Output:** `root`

This confirms you are administering the system as the root user.

---

## 👀 Step 3: Check Whether the User Already Exists

Before creating a new user, verify if the account already exists.

Run:

```bash
id cannz
```

**Possible Results:**

- **Result 1:** `id: 'cannz': no such user`
  This means the user does not exist. Continue to Step 4.
- **Result 2:** The command prints user information.
  This means the account already exists. Do not recreate it. Instead, inspect and adjust its configuration as needed.

---

## 👤 Step 4: Create the User

Create the user and their home directory:

```bash
adduser cannz
```

The system will create:

- The user account
- A new group named `cannz`
- A home directory at `/home/cannz`

It will also copy the default configuration files from `/etc/skel`.

---

## 🔑 Step 5: Set the Password

You will be prompted:

```text
New password:
```

Enter a strong password. Nothing will appear on the screen while typing — this is normal. Press Enter.

You will then see:

```text
Retype new password:
```

Enter the same password again. If both entries match, the password is saved.

---

## 📝 Step 6: Enter User Information

You may be prompted for:

- Full Name
- Room Number
- Work Phone
- Home Phone
- Other

These fields are optional. You can press Enter to leave each blank.

At the end, you'll be asked:

```text
Is the information correct? [Y/n]
```

Type `Y` and press Enter.

---

## ✅ Step 7: Verify the User Exists

Run:

```bash
id cannz
```

**Expected Output** (similar to):

```text
uid=1000(cannz) gid=1000(cannz) groups=1000(cannz)
```

*(The exact numeric IDs may differ.)*

---

## 📁 Step 8: Verify the Home Directory

Run:

```bash
ls -ld /home/cannz
```

**Expected Output** shows:

- The directory exists
- The owner is `cannz`
- The group is `cannz`

---

## 🔧 Step 9: Check Whether sudo Is Installed (CRITICAL STEP) 🚨

This is where we grant `cannz` the ability to run administrative commands.

Run:

```bash
sudo --version
```

- If a version number is displayed, sudo is installed — proceed to Step 10.
- If you receive `sudo: command not found`, install it:

```bash
apt update
apt install sudo
```

When installation completes, verify:

```bash
sudo --version
```

A version number should now be displayed.

> ⚠️ **Troubleshooting: "apt install sudo fails or hangs?"**
> <details>
> <summary>Click here to expand the fix</summary>
> <br>
> This usually happens if the package lists are stale or a repository is misconfigured.
> <br><br>
> **Fix:** Re-run `apt update` first (see the repository fix in Step 1), confirm you have network connectivity, then retry `apt install sudo`.
> </details>

---

## 🔐 Step 10: Add cannz to the sudo Group

Run:

```bash
usermod -aG sudo cannz
```

**Explanation:**

- `-a` = append (do not remove existing groups)
- `-G` = specify supplementary groups
- `sudo` = administrative group
- `cannz` = user to modify

---

## ✔️ Step 11: Verify Group Membership

Run:

```bash
groups cannz
```

**Expected Output** includes `sudo`, for example:

```text
cannz : cannz sudo
```

---

## 🔄 Step 12: Switch to the New User

Run:

```bash
su - cannz
```

Enter the password you created for `cannz`. The prompt changes to something similar to:

```text
cannz@pve:~$
```

---

## 👤 Step 13: Verify the Current User

Run:

```bash
whoami
```

**Expected Output:** `cannz`

---

## 🛡️ Step 14: Test sudo

Run:

```bash
sudo whoami
```

The first time you use sudo, you'll see a message similar to:

```text
[sudo] password for cannz:
```

Enter the password for `cannz`, **not** the root password.

**Expected Output:** `root`

This confirms that `cannz` can perform administrative tasks.

---

## Step 15: Return to the Root Shell

Type:

```bash
exit
```

You should return to:

```text
root@pve:~#
```

---

## 🔄 Step 16: Reboot

Run:

```bash
reboot
```

---

## 🖥️ Step 17: Log In to GNOME

When the GDM login screen appears:

1. Select the user: `cannz`
2. Enter the password you created for `cannz`.

GNOME should load successfully.

---

## ✅ Step 18: Verify the Desktop

Open GNOME Terminal and run:

```bash
whoami
```

**Expected Output:** `cannz`

Then run:

```bash
sudo whoami
```

Enter your password.

**Expected Output:** `root`

This confirms that the graphical session is running under `cannz` and that administrative privileges work correctly.

---

## 🎉 Phase Complete!

You now have a secure, non-root administrative user (`cannz`) ready for daily use. The user can perform administrative tasks via `sudo`, and the GNOME desktop environment is configured to run under this user account.

---

*Next Step: [Phase 4 - Safely Installing GNOME on the Proxmox Host](/phases/04-gnome-install)*
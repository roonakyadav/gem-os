# 🖥️ Virtual Machine Setup Log — Day 2 (Debian 13 on VirtualBox)

> **Project:** gem-os-dev
>
> **Host OS:** Ubuntu 24.04 (electron)
>
> **Guest OS:** Debian GNU/Linux 13 (Trixie)
>
> **Hypervisor:** Oracle VirtualBox
>
> **Disk Size:** 80 GB (Dynamic)
>
> **Goal:** Clean reinstallation, full storage usage, stable GUI + dev-ready setup

---

## 📌 1. Day 2 Objectives

* Remove old corrupted VM data
* Verify real disk allocation
* Reinstall Debian from scratch
* Configure partitions properly
* Fix cursor + keyboard capture issues
* Enable `sudo`
* Stabilize GUI login
* Prevent future boot/display freezes

---

## 📌 2. Cleaning Old VM & Storage Verification

### 2.1 Listing Existing VMs

```bash
ls ~/VirtualBox\ VMs
VBoxManage list hdds
```

### 2.2 Checking Host Storage

```bash
df -h ~
```

Result:

* Host had enough free space
* Old VM files confirmed
* Decision: Full reset

---

## 📌 3. Creating Fresh Virtual Disk

### 3.1 New VDI Creation

```bash
VBoxManage createmedium disk \
--filename "$HOME/VirtualBox VMs/gem-os-dev.vdi" \
--size 80000 \
--format VDI
```

### 3.2 Verifying Disk

```bash
VBoxManage showhdinfo "$HOME/VirtualBox VMs/gem-os-dev.vdi"
```

Result:

* Capacity: 80 GB
* Initial size: ~2 MB (dynamic)

---

## 📌 4. Debian Installation Process

### 4.1 ISO Used

* `debian-13.3.0-amd64-netinst.iso`

### 4.2 Network Setup

Hostname:

```
debian
```

Later renamed logically to:

```
gem-os
```

---

## 📌 5. Disk Partitioning

### 5.1 Partition Method Selected

```
Guided — use entire disk
```

Reason:

* VM only used for Debian
* No dual boot
* Avoid manual partition risk

### 5.2 Layout

* / (root)
* swap
* EFI (auto)

---

## 📌 6. GRUB Installation

Selection:

```
Install GRUB to primary drive → YES
```

Reason:

* Only OS
* Required for boot

---

## 📌 7. Post-Install Boot & First Login

### 7.1 GRUB Menu

Options:

* Debian GNU/Linux
* Advanced Options

Default used.

### 7.2 Initial Disk Check

```bash
df -h /
```

Result:

```
/dev/sda1  73G  1.1G  68G  2% /
```

Confirmed: Full disk available.

---

## 📌 8. Sudo Not Installed Problem

### 8.1 Error Encountered

```bash
sudo apt update
```

Output:

```
bash: sudo: command not found
```

Cause:

* sudo not selected during install

---

### 8.2 Installing sudo (Root Mode)

Switch to root:

```bash
su -
```

Install sudo:

```bash
apt update
apt install sudo
```

---

## 📌 9. Adding User to Sudoers

### 9.1 Add User to Group

```bash
usermod -aG sudo roonakyadav
```

### 9.2 Verify Groups

```bash
groups roonakyadav
```

Expected:

```
roonakyadav cdrom floppy sudo audio video plugdev users netdev bluetooth
```

---

### 9.3 Session Refresh

```bash
exit
logout
```

Then relogin.

---

## 📌 10. Cursor & Keyboard Capture Issues

### 10.1 Problem

* Mouse free
* Keyboard ignored
* Login screen frozen
* No tab/enter
* Ctrl+Alt+F keys affecting host

### 10.2 Cause

* Missing Guest Additions
* Wrong graphics controller
* GNOME + VirtualBox conflict
* No 3D driver

---

## 📌 11. Emergency Recovery Attempts

Tried:

* Reset VM
* Power off
* TTY switching
* Host key remap

Result:

* GUI remained frozen
* No input accepted

---

## 📌 12. Correct Fix Strategy (Host Side)

### 12.1 Hard Power Off

```
Machine → Close → Power Off
```

(No save state)

---

### 12.2 Display Settings Fix

VirtualBox → Settings → Display

Set:

```
Graphics Controller: VMSVGA
Video Memory: 128 MB
3D Acceleration: OFF
```

---

### 12.3 Remove ISO

Settings → Storage

Ensure:

```
Optical Drive: Empty
```

---

### 12.4 Normal Boot

```
Start → Normal Start
```

Expected:

* Keyboard restored
* Mouse synced
* Login works

---

## 📌 13. Successful GUI Recovery

After fixing display:

* Login screen responsive
* Keyboard works
* Mouse captured correctly
* GNOME session loads

System stabilized.

---

## 📌 14. Lessons Learned (Day 2)

### Technical

✔ Always install sudo during setup
✔ Always configure graphics early
✔ Remove ISO after install
✔ Don’t save broken VM state
✔ Use VMSVGA for Linux guests

### Process

✔ Verify disk before install
✔ Clean old data first
✔ Document every step
✔ Avoid rushed config

---

## 📌 15. Final System Status

| Component | Status         |
| --------- | -------------- |
| Disk      | ✅ 73 GB usable |
| Boot      | ✅ Stable       |
| sudo      | ✅ Working      |
| GUI       | ✅ Stable       |
| Input     | ✅ Working      |
| Network   | ✅ Active       |

---

## 📌 16. Pending Tasks (Day 3)

* Install VirtualBox Guest Additions
* Enable clipboard + drag-drop
* Setup dev environment
* Install Firefox/Chrome
* Configure VS Code
* Setup Git
* Harden backups

---

## 📌 17. Key Commands Reference

### System

```bash
df -h
lsblk
uname -a
```

### User & Privileges

```bash
su -
usermod -aG sudo USER
groups USER
```

### Updates

```bash
sudo apt update
sudo apt upgrade
```

### Power

```bash
sudo reboot
sudo poweroff
```

---

## ✅ End of Day 2 Summary

Day 2 focused on:

* Full VM reset
* Clean Debian install
* Disk verification
* Privilege setup
* Resolving major GUI freeze
* Stabilizing VirtualBox integration

System is now usable and ready for development setup.

---

**Status:** ✅ Day 2 Closed Successfully


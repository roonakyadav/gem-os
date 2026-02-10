# 🧩 DAY 6 — Minimal RootFS, Live ISO Build & Kernel Panic Debugging

Date: Day 6
Project: gem.os
System: Debian 13 (VirtualBox) → gem.os Live ISO

---

## 🎯 Objective

The goal of Day 6 was to:

* Build a minimal Debian root filesystem
* Configure a bootable live environment
* Package the system into a bootable ISO
* Debug kernel panic and init failures
* Achieve a fully working Live Linux distribution

---

## 🛠️ Tools Used (All Free)

* OS: Debian 13 (Trixie)
* Kernel: 6.12.x (Debian + Custom)
* Virtualization: VirtualBox
* Bootstrap Tool: debootstrap
* ISO Builder: grub-mkrescue, xorriso
* Compression: squashfs-tools
* Live System: live-boot, live-config
* File Transfer: VirtualBox Shared Folder

---

## 📌 Step 1: Workspace Preparation

Created clean directories for ISO build:

```bash
mkdir -p ~/gem-os/minimal/rootfs
mkdir -p ~/gem-os/iso
mkdir -p ~/gem-os/tools
```

Verified structure using:

```bash
tree -d -L 3
```

Purpose: Separate build, rootfs, and ISO assets.

---

## 📌 Step 2: Build Minimal Root Filesystem

Installed debootstrap:

```bash
sudo apt install debootstrap
```

Generated minimal Debian base:

```bash
sudo debootstrap --arch=amd64 trixie ~/gem-os/minimal/rootfs http://deb.debian.org/debian
```

Result: A clean minimal Linux system inside `rootfs`.

---

## 📌 Step 3: Prepare Chroot Environment

Mounted system directories:

```bash
sudo mount --bind /dev rootfs/dev
sudo mount --bind /proc rootfs/proc
sudo mount --bind /sys rootfs/sys
```

Entered chroot:

```bash
sudo chroot rootfs /bin/bash
```

Purpose: Configure minimal system as if it were a real OS.

---

## 📌 Step 4: Base System Configuration

Inside chroot:

* Set hostname
* Configure hosts
* Set root password
* Installed core packages

```bash
apt install systemd sudo nano vim network-manager locales
```

Generated locale:

```bash
locale-gen
update-locale
```

Result: Functional minimal OS.

---

## 📌 Step 5: Install Kernel & GRUB

Installed kernel and bootloader:

```bash
apt install linux-image-amd64 grub-pc
```

Configured fstab and networking.

Generated initramfs and GRUB config.

---

## 📌 Step 6: Cleanup & Exit

Reduced image size:

```bash
apt clean
rm -rf /var/lib/apt/lists/*
```

Unmounted system paths and exited chroot.

---

## 📌 Step 7: Install ISO Build Tools

Installed required tools:

```bash
sudo apt install xorriso grub-pc-bin squashfs-tools
```

Purpose: Enable ISO creation and compression.

---

## 📌 Step 8: Create SquashFS Image

Compressed root filesystem:

```bash
mksquashfs rootfs iso/live/filesystem.squashfs
```

Result: Read-only compressed OS image.

---

## 📌 Step 9: Initial Boot Setup

Copied kernel and initrd.

Created GRUB menu.

Generated ISO:

```bash
grub-mkrescue -o gem-os.iso iso
```

---

## ❗ Major Problem 1: Kernel Panic — No Init

### Error

```
Kernel panic - not syncing: Attempted to kill init
```

### Cause

* Missing live-boot system
* No handler for `boot=live`

### Fix

Installed live system inside chroot:

```bash
apt install live-boot live-config
```

Rebuilt initramfs.

---

## ❗ Major Problem 2: Incorrect Boot Parameters

### Issue

GRUB passed incomplete parameters:

```
boot=live
```

### Fix

Updated GRUB:

```
boot=live config components union=overlay
```

---

## ❗ Major Problem 3: Wrong ISO Layout

### Issue

Live system expected:

```
/live/vmlinuz
/live/initrd.img
/live/filesystem.squashfs
```

But files were in `/boot`.

### Result

live-boot could not locate rootfs.

### Fix

Restructured ISO:

```
iso/
 └── live/
```

Moved kernel + initrd to `/live`.

Updated GRUB paths.

---

## 📌 Step 10: Rebuild ISO (Final)

After fixes:

```bash
grub-mkrescue -o gem-os-1.0-nebula.iso iso
```

Copied ISO to host using shared folder.

---

## 📌 Step 11: Testing in VirtualBox

Created test VM using VBoxManage.

Attached ISO.

Booted system.

Result:

```
Debian GNU/Linux 13 gem-os-minimal tty1
login:
```

System successfully reached login shell.

---

## 📊 Verification

Inside Live System:

```bash
uname -r
hostname
cat /etc/os-release
```

Output:

* Kernel: 6.12.x
* Hostname: gem-os-minimal
* System: Debian 13 Live

---

## ✅ Final Status

| Component   | Status   |
| ----------- | -------- |
| RootFS      | Complete |
| Live-Boot   | Working  |
| ISO Build   | Success  |
| Kernel      | Loaded   |
| Init System | OK       |
| Login       | Enabled  |
| Debugging   | Mastered |

---

## 📚 Key Learnings

* How debootstrap builds minimal Linux
* How chroot enables OS configuration
* How live-boot works internally
* Why kernel panic happens
* ISO directory expectations
* GRUB parameter importance
* Debugging boot failures
* VirtualBox testing workflow

---

## 🚀 Outcome

Day 6 transformed gem.os from a modified Debian system into a real, portable Linux distribution.

The system now:

* Boots from ISO
* Runs in RAM
* Supports login
* Can be distributed
* Can be extended

This is equivalent to early-stage distro engineering.

---

## 🔮 Next Goals (Day 7 Preview)

* Integrate custom gemos kernel
* Replace Debian branding
* Add desktop environment
* Enable persistence
* Build installer
* Prepare public release

Status: ✅ Day 6 Completed Successfully

# 🧩 DAY 8 — Boot Optimization, Persistence Integration & Live System Stabilization

Date: Day 8
Project: gem.os
System: Debian 13 (VirtualBox) → gem.os Persistent XFCE Live ISO

---

## 🎯 Objective

The goal of Day 8 was to:

* Clean VirtualBox graphics warnings
* Fix fstab-related boot issues
* Optimize kernel boot parameters
* Implement real persistence
* Debug live-boot overlay behavior
* Stabilize user home and desktop environment
* Achieve fully persistent Live ISO

This day focused on converting gem.os from a temporary Live system into a **stateful, portable operating system**.

---

## 🛠️ Tools Used (All Free)

* OS: Debian 13 (Trixie)
* Kernel: 6.12.63-gemos
* Desktop: XFCE
* Virtualization: VirtualBox
* ISO Builder: grub-mkrescue, xorriso
* Filesystem Tools: e2fsprogs
* Live System: live-boot, live-config
* Editor: nano
* Disk Utilities: lsblk, mkfs.ext4, e2label

---

## 📌 PHASE 1 — Cleaning Graphics Warnings

### Problem

During boot, gem.os showed warnings related to VirtualBox graphics and DRM:

* umwgfx / hypervisor messages
* Kernel modesetting instability

### Solution

Modified GRUB kernel parameters.

Edited:

```bash
sudo nano /etc/default/grub
```

Changed:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

To:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nomodeset video=vesafb:mtrr:3,ywrap"
```

Applied:

```bash
sudo update-grub
sudo reboot
```

### Result

* No DRM crash
* No GPU panic
* Stable framebuffer rendering
* Clean boot logs

---

## 📌 PHASE 2 — Fixing fstab Errors

### Problem

Live system sometimes reported:

* Duplicate mount entries
* Non-fatal fstab warnings

### Host System Check

```bash
sudo nano /etc/fstab
```

Result: No duplicates found.

### RootFS Check

```bash
nano rootfs/etc/fstab
```

Standard Live configuration:

```
proc  /proc  proc  defaults  0  0
sysfs /sys   sysfs defaults  0  0
devpts /dev/pts devpts defaults 0 0
tmpfs /tmp tmpfs defaults 0 0
```

### Result

* No duplicate mount entries
* Boot warnings removed

---

## 📌 PHASE 3 — Boot Parameter Optimization

### Objective

Improve stability and prepare for persistence.

### Modified ISO GRUB

File:

```bash
nano ~/gem-os/minimal/iso/boot/grub/grub.cfg
```

Updated menu entry:

```
menuentry "Start gem.os (XFCE Live)" {
    linux /live/vmlinuz boot=live config components quiet splash nomodeset persistence
    initrd /live/initrd.img
}
```

### Purpose of Flags

| Flag        | Purpose            |
| ----------- | ------------------ |
| boot=live   | Enable live-boot   |
| config      | Enable live-config |
| components  | Load modules       |
| nomodeset   | GPU stability      |
| persistence | Enable save mode   |

---

## 📌 PHASE 4 — First Persistence Attempt (Embedded File)

### Initial Method

Created persistence image:

```bash
dd if=/dev/zero of=persistence.img bs=1M count=4096
```

Formatted:

```bash
/sbin/mkfs.ext4 persistence.img
```

Mounted and configured:

```bash
mkdir persist
sudo mount persistence.img persist
echo "/ union" | sudo tee persist/persistence.conf
sudo umount persist
```

Copied into ISO and rebuilt.

---

### ❗ Problem — ISO Size Limit

Error:

```
File exceeds size limit of 4294967295 bytes
```

### Cause

* ISO9660 filesystem limit: 4GB per file
* persistence.img was 4.3GB

### Fix

Reduced size:

```bash
dd if=/dev/zero of=persistence.img bs=1M count=1024
```

---

## 📌 PHASE 5 — Discovery: Embedded Persistence Not Used

### Observation

After boot:

```bash
mount | grep overlay
```

Showed:

```
upperdir=/run/live/overlay
```

### Meaning

* System was using RAM overlay
* persistence.img ignored

### Root Cause

Debian Live does not reliably support embedded persistence files.

Professional distros use external devices.

---

## 📌 PHASE 6 — Professional Persistence (External Disk)

### Method

Added second virtual disk in VirtualBox:

* Size: 2GB
* Type: VDI
* Dynamic

---

### Disk Preparation

Identified disk:

```bash
lsblk
```

Example:

```
sda 2G
```

Formatted:

```bash
sudo mkfs.ext4 /dev/sda
```

Labeled:

```bash
sudo e2label /dev/sda persistence
```

Verified:

```bash
lsblk -f
```

---

### Configure Persistence

Mounted disk:

```bash
sudo mount /dev/sda /mnt
```

Created config:

```bash
echo "/ union" | sudo tee /mnt/persistence.conf
```

Unmounted:

```bash
sudo umount /mnt
```

---

## 📌 PHASE 7 — Boot Parameter Bug

### Problem

Persistence still failed after reboot.

Overlay showed RAM usage.

### Investigation

GRUB entry contained:

```
persistence-label=GEMOS
```

But disk label was:

```
persistence
```

### Fix

Removed label flag.

Final boot line:

```
linux /live/vmlinuz boot=live config components quiet splash nomodeset persistence
```

ISO rebuilt.

---

## 📌 PHASE 8 — Verification

### Check Persistence Mount

```bash
mount | grep persistence
```

Output:

```
/dev/sda on /run/live/persistence/sda
upperdir=/run/live/persistence/sda/rw
```

### Result

* External disk mounted
* Overlay active
* Data stored on disk

---

## 📌 PHASE 9 — XFCE Desktop Reset Issue

### Problem

Even with persistence active:

* Desktop files disappeared
* Home recreated at login

### Diagnosis

```bash
df -h ~
```

Showed:

```
overlay /
```

XFCE + xdg-user-dirs was regenerating folders.

---

## 📌 PHASE 10 — Fixing Desktop Reset

### Disable Auto Recreation

```bash
xdg-user-dirs-update --set DESKTOP "$HOME/Desktop"
```

Edit:

```bash
nano ~/.config/user-dirs.dirs
```

```
XDG_DESKTOP_DIR="$HOME/Desktop"
```

Edit:

```bash
nano ~/.config/user-dirs.conf
```

```
enabled=False
```

Rebooted.

---

## 📌 PHASE 11 — Final Persistence Test

### Test

```bash
touch ~/Desktop/final_ok.txt
sudo reboot
ls ~/Desktop
```

### Result

```
final_ok.txt
```

Persistence confirmed.

---

## 📊 Final System Status

| Component     | Status     |
| ------------- | ---------- |
| Graphics      | Stable     |
| fstab         | Clean      |
| Boot Flags    | Optimized  |
| External Disk | Configured |
| Persistence   | Working    |
| Desktop       | Stable     |
| User Data     | Saved      |

---

## 📚 Key Learnings

* Live system overlay mechanics
* ISO9660 limitations
* Professional persistence design
* Virtual disk integration
* GRUB parameter importance
* live-boot detection rules
* XFCE home regeneration behavior
* Debugging layered systems

---

## 🚀 Outcome

Day 8 transformed gem.os from:

Temporary Live OS → Fully Persistent Portable Operating System

You now have:

* Stateful Live environment
* External disk persistence
* Stable desktop
* Saved applications
* Surviving reboots

This is production-grade live system engineering.

---

## 🔮 Next Goals (Day 9 Preview)

* Integrate graphical installer (Calamares)
* Add "Install gem.os" boot entry
* Configure disk partitioning
* Post-install scripts
* User creation wizard
* GRUB installation
* Branding installer UI
* Prepare hard-disk install mode

Goal: Convert Live OS → Fully Installable OS

Status: ⏳ Ready for Day 9

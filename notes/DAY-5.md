# 🧩 DAY 5 — System Branding, Boot Customization & Kernel Rebranding

Date: Day 5
Project: gem.os
System: Debian 13 (VirtualBox) → gem.os 1.0 (Nebula)

---

## 🎯 Objective

The goal of Day 5 was to:

* Rebrand Debian into gem.os at OS level
* Customize login, terminal, and hostname identity
* Brand GRUB bootloader
* Add custom Plymouth boot splash
* Fix VirtualBox graphics issues
* Remove Debian identity from kernel (uname)
* Achieve full system → kernel → userspace ownership

---

## 🛠️ Tools Used (All Free)

* OS: Debian 13 (Trixie)
* Kernel: 6.12.63 (Custom)
* Editor: nano
* Bootloader: GRUB
* Splash System: Plymouth
* VM: VirtualBox
* Renderer: Mesa llvmpipe

---

## 📌 Step 1: OS Identity (os-release)

### Backup Original File

```bash
sudo cp /etc/os-release /etc/os-release.backup
```

### Edit Identity

```bash
sudo nano /etc/os-release
```

Replaced with:

```
PRETTY_NAME="gem.os 1.0 (based on Debian 13)"
NAME="gem.os"
VERSION_ID="1.0"
VERSION="1.0 (Nebula)"
VERSION_CODENAME=nebula
ID=gemos
ID_LIKE=debian
HOME_URL="https://gemos.local"
SUPPORT_URL="https://gemos.local/support"
BUG_REPORT_URL="https://gemos.local/bugs"
```

### Verify

```bash
cat /etc/os-release
```

---

## 📌 Step 2: System-Wide Branding

### lsb-release

```bash
sudo nano /etc/lsb-release
```

```
DISTRIB_ID=gem.os
DISTRIB_RELEASE=1.0
DISTRIB_CODENAME=nebula
DISTRIB_DESCRIPTION="gem.os 1.0 (Nebula)"
```

### Login Banner

```bash
sudo nano /etc/issue
```

```
gem.os 1.0 (Nebula)
Kernel: \r on \m

Login:
```

### Remote Banner

```bash
sudo nano /etc/issue.net
```

```
Welcome to gem.os 1.0 (Nebula)
Powered by Debian Core
```

### MOTD

```bash
sudo nano /etc/motd
```

```
====================================
   Welcome to gem.os 1.0 (Nebula)
   Custom Linux Distribution
====================================
```

### Verify

```bash
lsb_release -a
cat /etc/issue
```

---

## 📌 Step 3: Hostname Branding

### Check

```bash
hostnamectl
```

### Set Hostname

```bash
sudo hostnamectl set-hostname gem-os-core
```

### Update Hosts

```bash
sudo nano /etc/hosts
```

Changed:

```
127.0.1.1 gem-os-core
```

### Reload

```bash
sudo systemctl restart systemd-hostnamed
exec bash
```

### Verify

```bash
hostname
hostnamectl
```

---

## 📌 Step 4: GRUB Bootloader Branding

### Backup

```bash
sudo cp /etc/default/grub /etc/default/grub.backup
```

### Edit

```bash
sudo nano /etc/default/grub
```

Final Config:

```
GRUB_DEFAULT=0
GRUB_TIMEOUT=3
GRUB_DISTRIBUTOR="gem.os"
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

### Apply

```bash
sudo update-grub
```

### Reboot Test

```bash
sudo reboot
```

---

## 📌 Step 5: Plymouth Boot Splash

### Install

```bash
sudo apt install plymouth plymouth-themes
```

### Create Theme Directory

```bash
sudo mkdir -p /usr/share/plymouth/themes/gemos
```

### Theme Config

```bash
sudo nano /usr/share/plymouth/themes/gemos/gemos.plymouth
```

```
[Plymouth Theme]
Name=gem.os
Description=gem.os Boot Splash
ModuleName=script

[script]
ImageDir=/usr/share/plymouth/themes/gemos
ScriptFile=/usr/share/plymouth/themes/gemos/gemos.script
```

### Script File

```bash
sudo nano /usr/share/plymouth/themes/gemos/gemos.script
```

### Create Logo

```bash
sudo apt install figlet imagemagick
convert -background '#0d1f2d' -fill white -font DejaVu-Sans-Bold -pointsize 80 label:"gem.os" /usr/share/plymouth/themes/gemos/logo.png
```

### Register Theme

```bash
sudo update-alternatives --install /usr/share/plymouth/themes/default.plymouth default.plymouth /usr/share/plymouth/themes/gemos/gemos.plymouth 100
sudo update-alternatives --config default.plymouth
```

### Rebuild Initramfs

```bash
sudo update-initramfs -u
```

### Test

```bash
sudo reboot
```

Result: gem.os splash screen active.

---

## 📌 Step 6: VirtualBox Graphics Fix

### Disable 3D Acceleration (Host)

* VirtualBox Settings → Display
* Disable 3D Acceleration
* Set VRAM = 128MB

### Force Software Renderer

```bash
echo "export LIBGL_ALWAYS_SOFTWARE=1" >> ~/.profile
source ~/.profile
```

### Install Tools

```bash
sudo apt install mesa-utils
```

### Verify

```bash
glxinfo | grep "OpenGL renderer"
```

Output:

```
llvmpipe (LLVM)
```

Result: Stable graphics.

---

## 📌 Step 7: Kernel Rebranding

### Enter Source Tree

```bash
cd ~/gem-os/kernel/linux-source/linux-source-6.12
```

### Edit Config

```bash
nano .config
```

Set:

```
CONFIG_LOCALVERSION="-gemos"
```

### Create localversion

```bash
nano localversion
```

```
-gemos
```

### Verify

```bash
grep LOCALVERSION .config
cat localversion
```

---

## 📌 Step 8: Rebuild Kernel Metadata

### Sync

```bash
make olddefconfig
```

### Rebuild

```bash
make -j$(nproc)
```

### Install

```bash
sudo make modules_install
sudo make install
```

### Update GRUB

```bash
sudo update-grub
```

### Reboot

```bash
sudo reboot
```

---

## 📊 Verification

```bash
uname -a
```

Output:

```
Linux gem-os-core 6.12.63-gemos-gemos
```

Debian branding removed.

---

## ✅ Final Status

| Component        | Status    |
| ---------------- | --------- |
| OS Branding      | Complete  |
| Login Branding   | Complete  |
| Hostname         | Complete  |
| GRUB             | Branded   |
| Splash Screen    | Active    |
| Graphics         | Stable    |
| Kernel Identity  | Rebranded |
| System Stability | OK        |

---

## 📚 Key Learnings

* Linux identity layers (os-release, lsb, kernel)
* GRUB customization
* Plymouth theming
* VirtualBox GPU limitations
* Kernel metadata control
* Professional distro branding workflow

---

## 🚀 Outcome

Day 5 transformed the system from a modified Debian into a fully branded custom distribution.

Control achieved from:

Bootloader → Kernel → Userspace → UI → Identity

System is now a true gem.os platform.

---

## 🔮 Next Goals (Day 6 Preview)

* Minimal root filesystem
* Package pruning
* ISO generation
* Custom installer
* Distribution packaging

Status: ✅ Day 5 Completed Successfully

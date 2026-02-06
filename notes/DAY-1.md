# 📘 DAY1.md — gem.os Development Log (Day 1)

**Date:** Day 1  
**Developer:** Ronak (roonakyadav_)  
**Project:** gem.os (Custom Linux-Based Operating System)

## 1. Objective of Day 1

The goal of Day 1 was:

- To set up a clean virtual machine environment
- To install Debian Linux as the base system
- To prepare a minimal system for building gem.os
- To understand the Linux terminal environment
- To begin setting up development tools

## 2. Tools Used (All Free)

- Host OS: Ubuntu Linux
- Virtualization: VirtualBox
- Guest OS: Debian 13 (netinst)
- Terminal: Default Linux terminal
- ISO Source: https://www.debian.org
- VM Manager: Oracle VM VirtualBox

## 3. Initial Setup

### 3.1 VirtualBox Installation

VirtualBox was already installed on Ubuntu.

Checked using:

```bash
virtualbox
```

### 3.2 Old VM Cleanup

Deleted previous VM (Android-x86) to start clean:

Right click → Remove

Select → Delete all files

Result: Clean VirtualBox environment.

## 4. Downloading Debian ISO

Downloaded Debian netinst ISO from:

https://www.debian.org/distrib/

Selected:

- 64-bit PC netinst.iso

File saved as:

debian-13.3.0-amd64-netinst.iso

Verified:

```bash
ls ~/Downloads | grep debian
```

## 5. Creating Virtual Machine

### 5.1 VM Creation

Clicked: New

Settings:

| Field | Value |
|---|---|
| Name | gem-os-dev |
| Type | Linux |
| Version | Debian (64-bit) |
| Folder | Default |

ISO Attached:

/home/roonakyadav_/Downloads/debian-13.3.0-amd64-netinst.iso

Checked:

☑ Skip Unattended Installation

### 5.2 Hardware Configuration

**RAM**  
4096 MB (4 GB)

**CPU**  
2 cores

**EFI**  
Disabled

### 5.3 Disk Configuration

Selected:

☑ Create Virtual Hard Disk

Disk Size:

40 GB

Pre-allocate: OFF

Disk Type: Dynamic

### 5.4 Final Summary

Verified:

- RAM: 4096 MB
- CPU: 2
- Disk: 40 GB
- OS: Debian
- ISO attached
- EFI OFF

Clicked: Finish

## 6. Virtualization Error & Fix

### 6.1 Error Encountered

When starting VM:

VERR_VMX_IN_VMX_ROOT_MODE
VirtualBox can't operate in VMX root mode

Cause:  
KVM conflict on Ubuntu.

### 6.2 CPU Virtualization Check

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

Output:

32

Meaning: CPU supports virtualization.

### 6.3 KVM Removal (Temporary)

Fixed conflict:

```bash
sudo modprobe -r kvm_intel kvm_amd kvm
```

After this, VM started successfully.

## 7. Debian Installation Process

### 7.1 Start Mode

Selected:

Normal Start

### 7.2 Installer Type

Chose:

Graphical Install

### 7.3 Language

English

### 7.4 Location

India

### 7.5 Keyboard

American English (US)

(Important to avoid password issues)

### 7.6 Network Configuration

Automatic configuration.

### 7.7 Hostname

Set:

gem-os

### 7.8 Domain Name

Left blank.

Pressed Enter.

### 7.9 Root Password

Left empty.

Pressed Enter twice.

Result:  
Root login disabled, sudo enabled.

### 7.10 User Setup

Full Name:

Ronak

Username:

roonakyadav_

Password (temporary):

abcd1234

### 7.11 Disk Partitioning

Selected:

Guided - use entire disk

Then:

All files in one partition

Then:

Finish partitioning

Confirmed:

Yes

### 7.12 Package Mirror

Selected:

mirror.nitc.ac.in

### 7.13 Proxy

Left blank.

### 7.14 Popularity Contest

Selected:

No

### 7.15 Software Selection (Minimal Install)

Unchecked everything except:

☑ Standard system utilities

Purpose:  
Minimal system for custom OS development.

### 7.16 GRUB Bootloader

Installed to:

Primary Virtual Disk (VBox disk)

Safe because VM is isolated.

### 7.17 Installation Completion

Clicked:

Continue

System rebooted.

## 8. First Boot

After reboot, system showed:

Debian GNU/Linux login

Logged in using:

Username: roonakyadav_
Password: abcd1234

System Message:

The programs included with Debian GNU/Linux system are free software...

Reached terminal.

## 9. Understanding Current Environment

Current environment:

- Minimal Debian
- No GUI
- Terminal only
- No browser
- No IDE

Equivalent to:

Ubuntu terminal without desktop.

## 10. First Commands Tried

### 10.1 Package Update (planned)

Not yet run, but planned:

```bash
sudo apt update
sudo apt upgrade
```

### 10.2 Build Tools (Initial Mistake)

Wrong:

```bash
sudo apt install build-essentials
```

Correct:

```bash
sudo apt install build-essential
```

### 10.3 Browser Attempt

Planned:

```bash
sudo apt install firefox-esr
```

But failed to launch:

Error: no display environment variable specified

Reason:  
No graphical environment installed.

## 11. Desktop Environment (Postponed)

Decision:  
Install GUI later.

Possible future choice:

```bash
sudo apt install xfce4 xorg
```

## 12. Disk Space Issue on Host System

### 12.1 Host Disk Status

From Ubuntu:

Root (/): 98 GB

150 GB Volume: Mounted

Windows-SSD: Unmounted

VM storage was limited.

### 12.2 Plan

Decision:  

Resize/merge partitions later

Possibly from Windows Disk Management



## 13. VM State Management

Paused VM instead of shutting down.

Purpose:
Resume later without losing state.

## 14. Development Philosophy (Day 1)

Established:

Minimal base system

Manual control

No bloat

Custom UI later

Open-source focus

Documentation-first mindset

## 15. Key Learnings (Day 1)
Technical

How to create Linux VM

How Debian installer works

How partitioning works

How GRUB works

Difference between GUI and CLI

KVM vs VirtualBox conflict

Practical

Keyboard layout matters

Always use minimal install

VM isolation is safe

Disk planning is important

## 16. Current System State

As of end of Day 1:

✅ Debian installed
✅ Minimal CLI system
✅ User configured
✅ VM running
⏸️ VM paused
❌ No GUI yet
❌ No IDE yet
❌ No build tools yet

## 17. Next Goals (Day 2 Preview)

Planned:

Fix host disk space

Resume VM

Install GUI (XFCE)

Install browser

Install VS Code

Install build tools

Setup GitHub repo

Start branding gem.os

## 18. Project Folder Structure (Planned)
gem-os/
├── DAY1.md
├── DAY2.md
├── builds/
├── scripts/
├── configs/
└── docs/

## 19. Notes

gem.os is based on Debian

All tools used are free

No paid software involved

System designed for long-term customization

Documentation maintained daily

### ✅ End of Day 1

Status: Successful Foundation Setup

```
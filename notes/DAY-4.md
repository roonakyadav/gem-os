```markdown
# 🧠 DAY 4 --- Custom Linux Kernel Build & Boot

Date: Day 4\
Project: gem.os\
System: Debian 13 (VirtualBox)

------------------------------------------------------------------------

## 🎯 Objective

The goal of Day 4 was to:

-   Install full Linux kernel source
-   Configure kernel build environment
-   Compile a custom Linux kernel
-   Install kernel modules
-   Install kernel image
-   Configure GRUB
-   Boot into custom kernel

------------------------------------------------------------------------

## 🛠️ Tools Used (All Free)

-   OS: Debian 13
-   Kernel Version: 6.12.63
-   Compiler: GCC 14
-   Build System: make
-   Virtualization: VirtualBox

------------------------------------------------------------------------

## 📌 Step 1: Install Kernel Source

Installed full kernel source:

sudo apt install linux-source

Source stored in:

/usr/src/linux-source-6.12.tar.xz

------------------------------------------------------------------------

## 📌 Step 2: Extract Kernel Source

cd /usr/src sudo tar -xf linux-source-6.12.tar.xz

Result:

linux-source-6.12/

------------------------------------------------------------------------

## 📌 Step 3: Copy Source to Workspace

mkdir -p \~/gem-os/kernel/linux-source sudo cp -r
/usr/src/linux-source-6.12 \~/gem-os/kernel/linux-source/ sudo chown -R
$USER:$USER \~/gem-os/kernel/linux-source

------------------------------------------------------------------------

## 📌 Step 4: Prepare Build Directory

cd \~/gem-os/kernel/linux-source/linux-source-6.12

Verified structure:

-   Makefile
-   arch/
-   drivers/
-   fs/
-   kernel/
-   net/

------------------------------------------------------------------------

## 📌 Step 5: Load Current Kernel Config

cp /boot/config-\$(uname -r) .config

Purpose:

Use existing stable configuration.

------------------------------------------------------------------------

## 📌 Step 6: Sync Config

make olddefconfig

Updated configuration for kernel 6.12.

------------------------------------------------------------------------

## 📌 Step 7: Install Build Dependencies

sudo apt install libncurses-dev flex bison libssl-dev libelf-dev dwarves

------------------------------------------------------------------------

## 📌 Step 8: Menu Configuration Test

make menuconfig

Verified configuration UI works.

------------------------------------------------------------------------

## 📌 Step 9: Full Kernel Compilation

make -j\$(nproc)

Duration: \~2--3 hours

Result:

All drivers, core, and modules compiled successfully.

------------------------------------------------------------------------

## 📌 Step 10: Install Kernel Modules

sudo make modules_install

Installed modules to:

/lib/modules/6.12.63/

------------------------------------------------------------------------

## 📌 Step 11: Install Kernel Image

sudo make install

Installed:

-   vmlinuz-6.12.63
-   initrd.img-6.12.63

To /boot

------------------------------------------------------------------------

## 📌 Step 12: Update GRUB

sudo update-grub

Detected kernels:

-   6.12.63 (custom)
-   6.12.63+deb13 (Debian)

------------------------------------------------------------------------

## 📌 Step 13: Set Custom Kernel Default

Edited:

/etc/default/grub

Configured GRUB_DEFAULT for custom kernel.

------------------------------------------------------------------------

## 📌 Step 14: Reboot & Verify

sudo reboot

Verified:

uname -r

Output:

6.12.63

System booted into custom kernel.

------------------------------------------------------------------------

## 📊 Disk Usage Check

df -h /

Result:

73G total\
32G used\
37G free

Sufficient space for kernel work.

------------------------------------------------------------------------

## ✅ Final Status

  Component       Status
  --------------- ---------------
  Kernel Source   Installed
  Compilation     Success
  Modules         Installed
  GRUB            Configured
  Boot            Custom Kernel
  Stability       OK

------------------------------------------------------------------------

## 📚 Key Learnings

-   How Linux kernel is structured
-   How to configure kernel builds
-   How GRUB selects kernels
-   How modules integrate
-   How to debug build errors
-   Importance of disk space

------------------------------------------------------------------------

## 🚀 Outcome

Day 4 successfully transformed the system from:

Debian Kernel → Custom gem.os Kernel

System is now under full kernel control.

------------------------------------------------------------------------

## 🔮 Next Goals (Day 5 Preview)

-   Customize boot splash
-   Remove Debian branding
-   Build minimal root filesystem
-   Generate gem.os ISO
-   Create installer

------------------------------------------------------------------------

Status: ✅ Day 4 Completed Successfully

```

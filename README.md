# LAB-02 — Embedded Linux Character Device Driver

## 📌 Overview

This project implements a Linux Kernel Character Device Driver for an ARMv7 embedded Linux environment running on **QEMU vexpress-a9**.

The project demonstrates the development and integration of a Linux kernel module with:

- Character device driver
- `/dev/lab2` device node
- `open()`, `read()`, `write()`, and `release()` operations
- `/proc/lab2_info` interface
- Sysfs attributes
- Kernel synchronization using `mutex`
- User-space ↔ kernel-space data transfer using `copy_to_user()` and `copy_from_user()`
- ARM cross-compilation
- BusyBox-based initramfs
- QEMU ARM emulation

The project is based on **Linux Kernel 6.6.21** and targets the **ARMv7 Cortex-A9 / QEMU vexpress-a9** platform.

---

## 🖥️ Development Environment

| Component | Configuration |
|---|---|
| Host OS | Ubuntu 26.04.1 LTS |
| Target Architecture | ARMv7 |
| CPU | Cortex-A9 |
| Machine | QEMU vexpress-a9 |
| Kernel | Linux 6.6.21 |
| Cross Compiler | arm-linux-gnueabihf-gcc |
| Bootloader | U-Boot 2022.04 |
| Root Filesystem | BusyBox + initramfs |
| Emulator | QEMU System ARM |
| RAM | 512 MB |

---

## 📂 Project Structure

```text
embedded_lab2/
├── driver/
│   ├── lab2_driver.c
│   └── Makefile
│
├── rootfs/
│   └── initramfs/
│       ├── etc/
│       │   └── init.d/
│       │       └── rcS
│       └── lib/
│           └── modules/
│               └── 6.6.21/
│                   └── lab2_driver.ko
│
├── output/
│   └── initramfs_lab2.cpio.gz
│
├── logs/
│   └── ...
│
└── README.md
```

---

# 🔧 Character Device Driver

The main kernel module is:

```text
lab2_driver.c
```

The driver registers a character device with:

```text
Device name : lab2
Major number: 240
```

After the system boots, the device is available as:

```text
/dev/lab2
```

The driver implements the following file operations:

```c
open()
read()
write()
release()
```

---

# 📡 Device Interfaces

## Character Device

```text
/dev/lab2
```

Applications can communicate with the kernel driver through normal file operations.

Example:

```bash
echo "Hello from userspace, LAB-02!" > /dev/lab2
cat /dev/lab2
```

---

## Procfs Interface

The driver provides:

```text
/proc/lab2_info
```

It exposes information about the driver and its current state.

Example:

```bash
cat /proc/lab2_info
```

Typical information includes:

```text
Driver name
Major number
Buffer size
Data length
Open count
Last data
```

The procfs interface is implemented using the kernel `seq_file` mechanism.

---

# 🖥️ Sysfs Interface

The driver creates a sysfs directory:

```text
/sys/class/lab2_class/lab2/
```

Available attributes include:

```text
buffer_len
open_count
last_data
upper_mode
```

Example:

```bash
ls -l /sys/class/lab2_class/lab2/
```

Read the current buffer length:

```bash
cat /sys/class/lab2_class/lab2/buffer_len
```

Read the current open count:

```bash
cat /sys/class/lab2_class/lab2/open_count
```

Read the last stored data:

```bash
cat /sys/class/lab2_class/lab2/last_data
```

---

# 🔠 Upper Mode

The `upper_mode` sysfs attribute controls whether lowercase characters are converted to uppercase before being stored in the driver's internal buffer.

Interface:

```text
/sys/class/lab2_class/lab2/upper_mode
```

### Mode 0 — Normal

```bash
echo 0 > /sys/class/lab2_class/lab2/upper_mode
```

Data is stored without uppercase conversion.

Example:

```bash
echo "Hello Lab 02" > /dev/lab2
```

Result:

```text
Hello Lab 02
```

### Mode 1 — Uppercase

```bash
echo 1 > /sys/class/lab2_class/lab2/upper_mode
```

Lowercase characters are converted to uppercase.

Example:

```bash
echo "Hello Lab 02" > /dev/lab2
```

Result:

```text
HELLO LAB 02
```

Only the values `0` and `1` are accepted.

Invalid values are rejected with:

```text
-EINVAL
```

---

# 🔒 Synchronization

Shared driver data is protected using a kernel mutex:

```c
static DEFINE_MUTEX(lab2_mutex);
```

The mutex protects shared resources such as:

```text
device_buffer
buffer_len
open_count
upper_mode
```

Critical sections use:

```c
mutex_lock(&lab2_mutex);
```

and:

```c
mutex_unlock(&lab2_mutex);
```

This prevents concurrent accesses from causing inconsistent driver state.

---

# 🔄 User Space ↔ Kernel Space

The driver uses:

```c
copy_from_user()
```

to transfer data from user space into the kernel.

It uses:

```c
copy_to_user()
```

to transfer data from the kernel back to user space.

These functions are used instead of directly accessing user-space pointers from kernel code.

---

# ⚙️ Building the Kernel Module

Enter the driver directory:

```bash
cd ~/embedded_lab2/driver
```

Build the module:

```bash
make clean
make
```

The resulting module is:

```text
lab2_driver.ko
```

Check the generated module:

```bash
file lab2_driver.ko
```

Check the kernel version information:

```bash
modinfo lab2_driver.ko
```

The module is built for:

```text
Linux 6.6.21
ARMv7
```

---

# 📦 Preparing Initramfs

Copy the kernel module into the initramfs:

```bash
cp lab2_driver.ko \
~/embedded_lab2/rootfs/initramfs/lib/modules/6.6.21/
```

The init script automatically loads the driver during system startup.

The initramfs is then packed into:

```text
~/embedded_lab2/output/initramfs_lab2.cpio.gz
```

---

# 🚀 Running on QEMU

Start QEMU using:

```bash
cd ~/embedded_lab1/output

qemu-system-arm \
    -M vexpress-a9 \
    -cpu cortex-a9 \
    -m 512M \
    -kernel zImage \
    -dtb vexpress-v2p-ca9.dtb \
    -initrd ~/embedded_lab2/output/initramfs_lab2.cpio.gz \
    -append "console=ttyAMA0,115200 rdinit=/init mem=512M" \
    -nographic
```

After booting, the system should provide a shell:

```text
/ #
```

The initialization script loads:

```text
lab2_driver.ko
```

and prepares:

```text
/dev/lab2
/proc/lab2_info
/sys/class/lab2_class/lab2/
```

---

# 🧪 Basic Driver Test

After QEMU boots:

### 1. Check the device

```bash
ls -l /dev/lab2
```

### 2. Check procfs

```bash
cat /proc/lab2_info
```

### 3. Check sysfs

```bash
ls -l /sys/class/lab2_class/lab2/
```

### 4. Write data

```bash
echo "Hello from userspace, LAB-02!" > /dev/lab2
```

### 5. Read data

```bash
cat /dev/lab2
```

### 6. Check stored data

```bash
cat /sys/class/lab2_class/lab2/last_data
```

### 7. Check buffer length

```bash
cat /sys/class/lab2_class/lab2/buffer_len
```

---

# 🔠 Upper Mode Test

Enable uppercase conversion:

```bash
echo 1 > /sys/class/lab2_class/lab2/upper_mode
```

Verify:

```bash
cat /sys/class/lab2_class/lab2/upper_mode
```

Write:

```bash
echo "Hello Embedded Linux" > /dev/lab2
```

Read:

```bash
cat /dev/lab2
```

Expected result:

```text
HELLO EMBEDDED LINUX
```

Disable uppercase conversion:

```bash
echo 0 > /sys/class/lab2_class/lab2/upper_mode
```

---

# 🧹 Module Management

The driver can also be loaded manually.

Load:

```bash
insmod /lib/modules/6.6.21/lab2_driver.ko
```

Check:

```bash
lsmod
```

Check kernel messages:

```bash
dmesg | tail
```

Remove:

```bash
rmmod lab2_driver
```

Check for errors:

```bash
dmesg | tail
```

---

# 🛠️ Main Technologies

This project demonstrates practical use of:

- Linux Kernel Modules
- Character Device Drivers
- `struct file_operations`
- `cdev`
- Device classes
- Sysfs
- Procfs
- `seq_file`
- Mutex synchronization
- `copy_to_user()`
- `copy_from_user()`
- BusyBox
- Initramfs
- ARM cross-compilation
- QEMU ARM emulation

---

# 🎯 Learning Objectives

The main objectives of this lab are:

1. Understand the structure of a Linux kernel module.
2. Implement a character device driver.
3. Implement `open`, `read`, `write`, and `release`.
4. Communicate between user space and kernel space.
5. Create `/dev` device nodes.
6. Expose driver information through procfs.
7. Expose driver attributes through sysfs.
8. Protect shared data using mutexes.
9. Build an ARM kernel module using cross-compilation.
10. Integrate the driver into an initramfs.
11. Boot and test the complete system using QEMU.

---

# 👨‍💻 Author

**Cao Huu An**

Embedded Linux / Semiconductor Engineering Student

---

# 📜 License

This project is developed for educational purposes as part of an Embedded Linux laboratory assignment.

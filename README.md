### LOS201 – Linux Platform

### Introduction

This repository contains my practical assignments and laboratory projects completed during the **LOS201 – Linux Platform** course.

The projects focus on Embedded Linux development, Linux kernel configuration, device driver implementation, kernel-space programming, and system initialization.

All projects were developed and tested in an ARM-based emulated environment using QEMU.

Projects Overview

### 1. LAB 01 – Embedded Linux System

**File:** `LAB01_Embedded_Linux.zip`

Implemented a basic Embedded Linux system from source, including:

- Linux Kernel compilation and configuration.
- U-Boot bootloader compilation.
- BusyBox compilation and installation.
- Root Filesystem (RootFS) construction.
- Initramfs generation.
- Embedded Linux booting and testing with QEMU.

**Technologies:**
- Linux Kernel 6.6.21
- U-Boot 2022.04
- BusyBox 1.35.0
- ARM Cross-Compilation Toolchain
- QEMU (ARM vexpress-a9)

### 2. LAB 02 – Linux Kernel Module & Device Driver

**File:** `LAB02_Kernel_Driver.zip`

Implemented and tested a Linux Character Device Driver with additional kernel interfaces.

Main features:

- Linux Kernel Module implementation.
- Character Device Driver registration.
- Device file creation and management.
- File operations: open, read, write and release.
- procfs interface for kernel information.
- sysfs interface for runtime parameter management.
- Mutex synchronization for shared data.
- Read-only mode implementation.
- Word counting functionality.

**Technologies:**
- Linux Kernel Module
- Character Device Driver
- procfs and sysfs
- Mutex Synchronization
- ARM Cross-Compilation
- QEMU

### 3. Assignment 01 – Linux Device Driver

**File:** `Assignment01_Device_Driver.zip`

Implemented Linux device driver exercises focusing on communication between user space and kernel space.

Main topics:

- IOCTL implementation.
- Kernel-space and user-space communication.
- Miscellaneous Device Driver.
- Device driver operations and management.
- User-space testing applications and scripts.

**Technologies:**
- Linux Kernel Module
- IOCTL
- Miscellaneous Device
- C Programming
- ARM Cross-Compilation

### 4. Assignment 02 – SMS Sensor Driver

**File:** `Assignment02_SMS_Sensor_Driver.zip`

Developed a Linux Kernel Module simulating a sensor device and providing sensor data to user-space applications.

Main features:

- SMS Sensor Character Device Driver.
- Temperature data simulation.
- Humidity data simulation.
- Configurable sampling interval using IOCTL.
- Mutex synchronization.
- procfs interface for monitoring driver information.
- Sensor data reading from user space.
- Error handling and input validation.

**Technologies:**
- Linux Kernel Module
- Character Device Driver
- IOCTL
- procfs
- Mutex Synchronization
- C Programming

## Development Environment

| Component | Configuration |
==============================
| Operating System | Ubuntu Linux |
| Architecture | ARMv7 |
| CPU | ARM Cortex-A9 |
| Emulated Machine | QEMU vexpress-a9 |
| Linux Kernel | 6.6.21 |
| Cross Compiler | arm-linux-gnueabihf-gcc |
| Programming Language | C |
| Development Tools | GCC, Make, GDB |

## Repository Structure

LOS201-Linux-Platform/
│
├── README.md
│
├── LAB01_Embedded_Linux.zip
├── LAB02_Kernel_Driver.zip
├── Assignment01_Device_Driver.zip
└── Assignment02_SMS_Sensor_Driver.zip
```

## Learning Outcomes

Through these laboratory exercises and assignments, I gained practical experience in:

- Understanding Embedded Linux architecture.
- Building and configuring Linux kernel components.
- Working with bootloaders and root filesystems.
- Developing Linux kernel modules.
- Implementing character device drivers.
- Understanding kernel-space and user-space communication.
- Working with procfs, sysfs and IOCTL.
- Applying synchronization mechanisms in kernel programming.
- Cross-compiling and testing ARM-based Linux systems using QEMU.

## Author

CAO HUU AN

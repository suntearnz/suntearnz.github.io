---
title: BootLoader Brief Review
date: 2020-07-09 09:21:43
tags: bootloader
categories: [bootloader]
---

## Small Talk about BootLoader

**author ：[Fred](suntearinheart@gmail.com)**

[TOC]

### Introduction

> Note：This article aims to prodive an empirical introductory reference solution

### Prerequisites

- Good at C。
- Certain programming experience.
- Certain Linker&Loader knowledge

### Main Topic

The bootloader is the codes that are initially loaded and run after the system is powered on. It provides the initialization codes that need to be executed initially after the processor is powered on and reset. Loading and starting of the whole system is completely done by the bootloader. Its main function is to load and boot the kernel image

An embedded storage device usually includes four partitions:

 1. Bootloader
 2. Stores the parameters that u-boot will pass to the system kernel
 3. the system kernel (kernel)
 4. the root file system

### Bootloader

Bootloader is a concept that is bound to be touched for embedded development. This article mainly explains the basic concepts and internal principles of Bootloader. The mastery of this part will be very helpful for the study of embedded Linux system development!

Definition of Bootloader: Bootloader is a small program that is executed before the operating system runs. Through this small program, we can initialize hardware devices and establish a mapping table of memory space, so as to establish an appropriate system software and hardware environment, and finally call the operating system. The kernel is ready. This means that if we want an operating system to run on our board, we must first perform some basic configuration and initialization on our board before we can boot the operating system into it. We will analyze the specific operations completed in the Bootloader later. In embedded systems, the loading and startup tasks of the entire system are completely completed by Boot Loader. For example, in an embedded system based on ARM7TDMI core, the system usually starts from address 0x00000000 when it is powered on or reset, and the Boot Loader program of the system is usually arranged at this address. 

The bootloader is implemented based on a specific hardware platform, so it is almost impossible to build a universal bootloader for all embedded systems. Different processor architectures have different bootloaders. The bootloader not only depends on the CPU architecture, but also depends on embedding. The configuration of the board-level equipment of the distributed system. For two different boards, even if they are using the same processor, if you want the Bootloader program running on one board to run on another board, you usually need to modify the source program of the Bootloader.

### startupmethod

Bootloader's startup methods mainly include network startup mode, disk startup mode and Flash startup mode.

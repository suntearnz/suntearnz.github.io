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

### Detail of boot loader in ATxmega256A3BU

There are two section in this MCU.  one is application secton and the other is boot loader.  The size of boot section is 8k.  we can use the ICE device to config the value of the FUSES  to choose the starting address when doing the poweron.  Normally we like to jump to bootloader firstly to do some basic initialization.
Most of the MCU will disable the interrupt when doing the the power-on action. but I like to do one clear disable action

```C
cli()
```

Then init the clock. System clock is a very important thing that need to be done firstly. It's like when we play one group dance, we need play the music and every dancer need dance with the music by themself.  The system clock seems as the music meter. Based on the meter, every parts can know when and how they can co-work together and when they should do and when not. For the system clock, we need to select the source of the clock, becausing the default internal source is a little low.  After we have done the selection of the clock. we can do the prescaler of the source.  because there are lots of peripheral with different clock requirement.  So there are three Prescalers that we can config to get the different clock that we want.
and the precedure to enable the PLL as below

1. Enable reference clock source
2. Set the multiplication factor and select the clock refercence for the PLL
3. Wait until the clock reference source is stable
4. Enable the PLL

Also when we practice the group dance, if someone make mistake or fault. and the director want to  rehearse again. Normally we need to replay the music. For the MCU or CPU, it is the same.
we need stop the PLL before a new configuration can be written.

After the config of the system clock is done, the next step is configing the interrupt vector table to booloader section. Because in our design, the bootloader application can work by itself.
So there is no need to let the application and bootloader to share the same vector. and also when the application is crashed, the bootloader can work by itself. we only need to config the IVSEL to move the interrupt vector table to bootloader section.

In the following steps,  we will init the peripherals that the board has one by one. I don't think there need special order.  because the interrupt is still in disable status. From my view, the peripherals are in the same priority, they are all slave for CPU.

### Init the peripherals

In our board, the peripherals are Bluetooth, flash, Uart. We will use the Bluetooth to let the bootloader has the communication ability. and the flash will  be used to store the application image. and the Uart is the protocol that need be used for bluetooth.  Even there is no special requirement for init order. But I perfer to do them as:  flash, Uart, Bluetooth. This just like when you hold a wedding event,  you will try  to list out the seats for your family and friends. Depending on the relationship.

### Main process

For the MCU, this is only a main thread to do the loop that is triggered by event(interrupt, including the timer). In the main loop,  it will check the flag in EEPROM, if updating flag has been set, it will load the new firmware from the external flash to application section. else it will try to start from application section. Before they do this, they will do the CRC checking. CRC checking is important thing to do. Just like when we try to drive car out, we like to walk around to check the tyres are ok or not. After the checking is pass,  let's jump to application codes.
if not,  we need fix every faults like changing the tyres, repairing the engine. 

### Give the right to application

Cool! After we find the application codes is the real one that we want,  we need release the right to application.  Reset the interrupt vector to application section, and update the PC value to 0

### Congratulation

All Done!

---
title: "Linux Kernel - Part 1"
date: 2026-04-06
tags: ["linux", "mmap", "syscall"]
---

Recently start to learn some basic stuffs for linux kernel. I'm nearly new to this since I used to study Ic desing and accelrators and has no experience in OS. The learning materials will be stored in [https://linkingmon.github.io/Algorithm/Linux](this repo).

Overall I think this is interesting and I'm starting to bridge the gap for my IC design knowldege and C++ programming knowldege. 

Linux kernel is mostly written in C, and the interaction can be classified in 4 class:
1. syscall
2. filesystem interface
3. device file
4. ioctl/mmap

## Syscall
The syscall is called in a process to do some interaction with the OS system, such as `read`, `write`, `mmap`, `execve`, `brk`, etc.

we can build a simple program and use `strace` to check the syscall for the process.

For C-lib, it may include some `sys` comamnds such as `mmap` in the `malloc` when the memory allocator finds that it needs more memory.

## Filesystem & Device file
Usaully for debuggin, we can do `cat /proc/cpuinfo` or `ls /sys/devices` to get some information. These files are not actual files that is stored on the disk, and the value is return only when user tries to read it. This is another method to interact with the OS system.

The design strategy for this is that it is better to concat with otehr commands or tools like `echo` or `python`.

## ioctrl, mmap
These are for memory controlls.

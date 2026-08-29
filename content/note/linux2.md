---
title: "Linux Kernel - Part 2"
date: 2026-04-30
tags: ["linux", "mmap"]
---

Recently learn more linux (materials will be put [here](https://github.com/linkingmon/tech-lab/tree/master/Linux)).

## mmap/brk and memory allocation in C
We try to build some run and use `strace` to analyze all the syscalls, such as `execve` for execution, `brk` and `mmap` for requesting the memory, etc.

I notice that for the C `malloc` and C++ `new`, we actually call `brk` or `mmap` for requesting memory and then the allocator would do memory control, and it will continue requesting memory from OS if the allocator found that the current memory is not enough.

## Process & Thread Id
Another intersting thing is using syscall to get hte proces Id and trhead Id, and this is what we usually see in `ps -ef`

## Detail for mmap
I notice that the mmap requires mlutiple inut such as the memory size (usualyl 4096 (as 1 page), and the memory usage (protection read/write), if it actully map to files (map private, annonymius or shared), etc.

I then find that there is a Youtube that is really interesting and has describe the funciton of `mmap` well. See [Why Linux Has This Syscall?! - Tsoding](https://www.youtube.com/watch?v=sFYFuBzu9Ow), I will also write this in the next post.

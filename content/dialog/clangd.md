---
title: "Clangd & LLVM"
date: 2026-01-03
tags: ["llvm", "clangd"]
---

Recently I saw YT streaming for C++ compiling, and find that many people are using alngd for building C++, and I'm curious about what is the difference for clangd and gcc compiler, since during my work with CAD tools, we always use gcc for compiling.

After discussion with AI, they are a old style (gcc) and new style (clangd). Old style does not mean that they are worse. Here is a table for the comparison:

| Aspect | GCC | Clang / LLVM |
|---|---|---|
| Architecture | Monolithic | Modular |
| License | GPL | Apache 2.0 |
| Commercial Friendliness | Limited | Very High |
| Community Contributions | Difficult | Easy |
| Adoption by Large Companies | Limited | Google / Apple |
| IR Flexibility | Low | Very High |
| Extensibility | Low | High |

The GCC is a all-in-one architecture, while Clangd is more modularized. Clangd is just a part for the while Clangd/LLVM system, and I will introduce that later.

The license GPL is much more strict, and it seems that it restricts that if you try to modify them and share it, it must not be commercialize. While Apache 2.0 does not care if you use or make any changes. Therefore, a bunch of bigtechs such as apple, google, meta are using them and also promoting them.

For the performance, GCC is average better for compiling (perhaps 5%), but this is not really important since the most performance bottleneck are usually the cahce miss, memory access pattern, data structure, etc.

For commiting codes, they are both welcome, but gcc is more restricted and have more careful review.

#### Clangd / LLVM
Actually clangd is the frontend to converting the C++ to IR (stands for intermediate representation). For other language, we might have rustc, or coverting from swift code (apple).

During the converting, the techniques include parsing, semetic check and AST (acstract syntax treee) generations.

After LLVM IR, we will have optimizer to optimize to differnet hardware archiecture. In this stag,e techiqnue include DCE (dead code eliminate), loop Opt (ex. loop unrolling), amd Instruction Combine. Finally the backend is x86, ARM, RISC-V, etc.
 
In this structure, you can have same IR, that can optimize to different hardware arhc. 

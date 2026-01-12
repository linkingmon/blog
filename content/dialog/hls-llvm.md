---
title: "HLS & LLVM"
date: 2026-01-05
tags: ["llvm", "hls", "hardware"]
---

Last few days I learned that the C++ translation to IR requires AST (abstract syntax tree), and it seems to me that this is much the same as the HLS.

From HLS (high leve synthesis) tool, you input a C++ code and output a RTL, usualy verilog code, which is a hardware descriptoin language.

I learned HLS and used tools before, for FPGA mapping we use Vivado HLS, or called Vitis now (original owned by Xilinx, which is aquired by AMD). I think if we can leverage the same technique, perhaps we can do the same techqiue and map arbitary C++ code to RTL.

However, AI state that this is not really useful, since for the software performance, when you need to split some functions out and make it more hardware frieldy, this reuiqers a lot of hardware knoledge, and are not quite easily. And even if you know where is good for hardware, people did not choose to use HLS since this is still too high level, and they use somthing like chisel instead. I think this is quite true, since in EDA tools, most of the algorithm are heuristics with lots of branching, early termination, which is more suitable for running on a CPU. Also, this helps us more easily to debug and adding new features and re-write our structure. Also, a lot of functions are more data structure related, and converting it to a function or mapping on FPGA did not always help. I think for GPU this is the same. Many people aim to find paralell algorithm on EDA tools and try to run on GPU, such as maze seraching (there are some paper), but this is actually really difficult and not quite useful in a highly branched code. 

But I still think that perhaps in the future, we can have automatic HLS conversion algorithm that is really quick and powerful, then the AST related method may be useful (current repo relatede to hls-llbm include vitis-llvm, hwt-hls, ..) 

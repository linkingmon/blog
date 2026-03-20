---
title: "Gdb skills - part 1"
date: 2026-03-13
tags: ["gdb"]
---

Today I like to share some gdb skills that I've learned in the pass few years. 

## Break
I think the most widely use is the breakpoint. 
We can do break in some function (as `b funcA`), or break at some line (as `b classA.cpp:99`).
Some times adding hte condition for breaking is also useful (as `b funcA if cnt == 10`). Also this can ne done by `cond`, say this breakpoint is the third, we can do `cond 3 cnt ==10`.

`info b` shows all the breakpoint and its status.
`dis 3` to disable the third breakpoint, and `en 3` to enable it again. `del 3` to delete it.

## Continue, Next, Step, Finish
`c` would approach to next breakpoint, nad `s` would step in next line (will step into function), and `n` would step over next line, and `f` would finish the current function.
Usually this would combine with `+N`, `c 3` would approahc to the third breakpoint, `s 3` and `n 3` would step in/over next 3 lines.

##bt, up, down
see hte call stack for the current thread, and `up` and `down` to move up and down the call stack.

## Watch
I think watch is the most powerful debugging command. Usually we may encounter a situation htat some variable is originally correct, but after some operations it becomdes wrong, but we did not know where it actually becomes wrong.

For instance, `classA a`'s member `is_valid` is originally true, but after some operations it becomes false, and we do not know where it becomes false. In this case, we can use `watch a.is_valid` to set a watch point on this variable, and when it becomes false, gdb will break and we can check the call stack to find out where it becomes false.

## Set var
sometimes we might need to chagne value or to store some value for convience.
For instance, if we know the pointer of some class from the log (say 0x12345678), we can do `set $a = (classA*)0x12345678` to set a pointer variable $a to this address, and then we can do `p *$a` to print the content of this class.

## x, p
`x addr` would print the value inside the address, `p` prints the variable. We can combine with `/x`, `/u`, `/d`, `/t` to print the format in hxe, unsigned, signed, binary, and `/4wx` to print 4 words (4 byte per word), `/gx` to print 8 bytes.

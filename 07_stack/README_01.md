# README for 01.asm

This program is the simplest demonstration of the stack as a LIFO structure.

## Execution flow

```asm
_start:
    mov rax, 100
    mov rbx, 200

    push rax
    push rbx
    nop

    pop rcx
    pop rdx
    nop
```

At the start, `RAX = 100` and `RBX = 200`. The stack is empty, so the top of the stack is just the current value of `RSP`.

## Stack behavior

### First push

```asm
push rax
```

`push` stores the 64-bit value of `RAX` at the new top of the stack and then subtracts 8 from `RSP`.

Conceptually:

```text
Before:  RSP = S
After:   [S - 8] = 100, RSP = S - 8
```

The value 100 is now sitting at the top of the stack.

### Second push

```asm
push rbx
```

`RBX` is 200, so `RSP` is decremented again by 8 bytes. The new stack layout is:

```text
[S - 16] = 200
[S - 8]  = 100
RSP      = S - 16
```

This is the key LIFO behavior: the most recent value pushed is placed closest to the stack pointer.

### First pop

```asm
pop rcx
```

`pop` reads the value at the current top of the stack and loads it into `RCX`, then adds 8 to `RSP`.

```text
RCX = [S - 16] = 200
RSP = S - 8
```

### Second pop

```asm
pop rdx
```

The next value is now at `[S - 8]`, so:

```text
RDX = [S - 8] = 100
RSP = S
```

At that point, the stack pointer is restored to its original value, and the stack is effectively back to empty.

## Relevant runtime observation

When I ran `./01`, it exited successfully with status 0. There was no console output because this example does not print anything; it only shows that the stack pointer moves in a controlled way and the values come back out in reverse order.

## Summary

This file demonstrates the essential stack rule: `push` moves `RSP` downward and stores a value; `pop` reads that value and moves `RSP` back upward. The values leave the stack in reverse order of insertion, which is exactly the LIFO behavior of a stack.

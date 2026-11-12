# README for 06.asm

This is the 32-bit equivalent of the previous 64-bit push/pop example. The same stack principles hold, but with 32-bit registers and a 4-byte `ESP` step per push/pop.

## Execution flow

```asm
BITS 32
section .text
    global _start

_start:
    mov eax, 0x11223344
    mov ebx, 0x1234

    push eax
    push ebx
    nop

    pop ebx
    pop eax

    mov eax, 1
    xor ebx, ebx
    int 0x80
```

## Stack pointer behavior

Because this is 32-bit code, each `push` reduces `ESP` by 4 bytes and each `pop` increases it by 4 bytes.

### First push

```asm
push eax
```

`EAX` contains `0x11223344`, so the program writes that 32-bit value to the top of the stack and then does:

```text
ESP = ESP - 4
```

### Second push

```asm
push ebx
```

`EBX` contains `0x1234`, so it is stored just below the previous value:

```text
[S - 8] = 0x1234
[S - 4] = 0x11223344
ESP     = S - 8
```

This is the same LIFO ordering as in the 64-bit example.

### Pop sequence

```asm
pop ebx
pop eax
```

`pop ebx` reads the current top-of-stack value (`0x1234`), places it in `EBX`, and increments `ESP` by 4. Then `pop eax` reads the remaining value (`0x11223344`) and restores it to `EAX`.

At the end, `ESP` is back to the original value, and both registers contain the values that were pushed earlier.

## Relevant runtime observation

I ran `./06` and it exited successfully with code 0. There was no output because this program focuses on stack ordering rather than printing to the terminal. The significant runtime fact is that `ESP` moves down on each push and then back up on each pop, restoring the stack to its original state.

## Summary

This file confirms that the stack is architecture-dependent in size but the principle is the same in every case: pushing stores a value at a lower address and reduces the stack pointer; popping reads it back and raises the pointer again. The reverse order of retrieval is the defining feature of a stack.

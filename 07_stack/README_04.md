# README for 04.asm

This is the 32-bit version of the frame-based stack example. The same ideas apply as in 02.asm, but here the registers are 32-bit and the stack elements are 4-byte values.

## Execution flow

```asm
BITS 32
section .text
    global _start

_start:
    call work

    ; Linux i386 exit syscall
    mov eax, 1
    xor ebx, ebx
    int 0x80

work:
    push ebp
    mov ebp, esp
    sub esp, 8

    mov dword [ebp - 4], 100
    mov dword [ebp - 8], 200

    mov eax, [ebp - 4]
    mov ebx, [ebp - 8]
    nop

    mov esp, ebp
    pop ebp
    ret
```

## Stack pointer behavior

The key difference from the 64-bit version is 32-bit stack alignment: `push` and `pop` move `ESP` by 4 bytes instead of 8.

### Prologue

```asm
push ebp
mov ebp, esp
sub esp, 8
```

`push ebp` saves the caller's frame pointer and reduces `ESP` by 4. Then `mov ebp, esp` makes `EBP` point to the top of the stack after the saved frame pointer. After `sub esp, 8`, there are 8 bytes reserved for local storage.

Conceptually:

```text
[ebp - 8] = 200
[ebp - 4] = 100
```

The values are stored in memory relative to the frame pointer, exactly as in the 64-bit case.

### Cleanup

```asm
mov esp, ebp
pop ebp
ret
```

`mov esp, ebp` removes the 8 bytes of local storage and returns `ESP` to the point right after the saved `EBP`. Then `pop ebp` restores the caller's original base pointer and increases `ESP` by 4. Finally, `ret` pops the return address and resumes after the call site.

## Relevant runtime observation

I ran `./04` and it exited successfully with code 0. There was no visible output because the example intentionally only demonstrates stack discipline and register setup. The meaningful result is that the stack frame is created, used, and then restored without leaving stale values in the caller's frame.

## Summary

This 32-bit example shows the same stack-frame pattern as the 64-bit version, but with 4-byte elements and a 32-bit stack pointer. It demonstrates that `push`/`pop`, `EBP`-relative locals, and the prologue/epilogue pattern are architecture-level conventions for safe function execution.

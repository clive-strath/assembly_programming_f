# README for 05.asm

This program is a 64-bit version of a simple push/pop sequence, but it uses a larger value in `RAX` and a 16-bit/32-bit-sized value in `RBX` to show that the stack stores the exact register content without changing the logic of the operation.

## Execution flow

```asm
_start:
    mov rax, 0x1122334455667788
    mov rbx, 0x1234

    push rax
    push rbx
    nop

    pop rbx
    pop rax

    mov rax, 60
    xor rdi, rdi
    syscall
```

## Stack behavior

The first value placed on the stack is the 64-bit contents of `RAX`:

```asm
push rax
```

This decrements `RSP` by 8 and stores the entire 64-bit value at the new top of the stack.

Then:

```asm
push rbx
```

`RBX` holds `0x1234`, and `push` stores that value to the next lower address, lowering `RSP` again by 8 bytes.

The stack now effectively looks like this:

```text
[S - 16] = 0x1234
[S - 8]  = 0x1122334455667788
RSP      = S - 16
```

Because the stack is LIFO, the first value popped is the most recently pushed value.

## Pop sequence

```asm
pop rbx
pop rax
```

`pop rbx` reads the word at the top of the stack and places it into `RBX`, then increments `RSP` by 8. Since the top was `0x1234`, `RBX` receives that value.

The next pop loads the remaining value into `RAX`, and `RSP` returns to its original value.

This demonstrates that the stack can hold values of different sizes but preserves the actual register content while moving them in a strict reverse order.

## Relevant runtime observation

I ran `./05` and it exited successfully with status 0. There was no printed output, because the program is only demonstrating stack movement, not user-visible output. The important runtime fact is that the values are restored in reverse order: the second push is popped first, then the first push is popped.

## Summary

This file shows that the stack is a last-in, first-out data structure even when values are large or of differing size. `RSP` moves downward on each push and upward on each pop, and the register values are restored in reverse order of insertion.

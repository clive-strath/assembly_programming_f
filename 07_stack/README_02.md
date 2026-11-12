# README for 02.asm

This example shows a more realistic stack frame: it creates a function, saves the caller's base pointer, allocates local space, stores values in memory relative to `RBP`, and then cleans the stack before returning.

## Execution flow

```asm
_start:
    call work
    mov rax, 60
    xor rdi, rdi
    syscall

work:
    push rbp
    mov rbp, rsp
    sub rsp, 16

    mov qword [rbp - 8], 100
    mov qword [rbp - 16], 200

    mov rax, [rbp - 8]
    mov rbx, [rbp - 16]
    nop

    mov rsp, rbp
    pop rbp
    ret
```

## How the call affects the stack

When `_start` executes `call work`, the CPU automatically pushes the return address onto the stack. On 64-bit Linux, this is an 8-byte return address, so `RSP` decreases by 8 before execution enters `work`.

At the start of `work`:

```asm
push rbp
mov rbp, rsp
```

`push rbp` saves the previous base pointer on the stack. That is another 8-byte push, so `RSP` goes down by 8 again. Then `mov rbp, rsp` makes `RBP` point to the new top of stack.

## Local frame allocation

```asm
sub rsp, 16
```

This reserves 16 bytes of local storage for the function. `RSP` drops again, creating a stack frame for the two 8-byte values 100 and 200.

The frame now looks like this conceptually:

```text
[rbp - 16] = 200
[rbp - 8]  = 100
```

The values are stored relative to `RBP`, not directly on the top of the stack. That is a common stack-frame pattern: `RBP` acts as a stable reference while `RSP` is free to move during the function.

## Cleanup before return

```asm
mov rsp, rbp
pop rbp
ret
```

`mov rsp, rbp` removes the local area and returns `RSP` to the value it had after the saved `RBP` was pushed. Then `pop rbp` restores the caller's base pointer and increases `RSP` by 8. The `ret` instruction then pops the saved return address and jumps back to the instruction after the original `call`.

## Relevant runtime observation

I ran `./02` and it exited with status 0. There is no console output; the program does not print values, but its stack activity is clear: `call` stores a return address, `push rbp` saves the old frame pointer, `sub rsp,16` allocates locals, and the cleanup sequence restores everything before returning.

## Summary

This file shows the standard function stack frame: a call pushes a return address, the function pushes `RBP`, allocates local storage with `sub rsp, N`, uses `RBP` offsets to access locals, and then restores `RSP` and `RBP` before returning. It is a good example of how a stack supports function-local temporary storage without corrupting the caller's state.

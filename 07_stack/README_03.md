# README for 03.asm

This file is a minimal example of a function call with a return address on the stack. It shows that the stack is not just for data: it also stores the place to resume execution after a function returns.

## Execution flow

```asm
_start:
    mov rax, 10
    call calculate

after_call:
    ; RAX should now be 15
    nop

    mov rax, 60
    xor rdi, rdi
    syscall

calculate:
    add rax, 5
    ret
```

## What happens at `call`

`mov rax, 10` sets the input value. Then `call calculate` pushes the return address onto the stack and jumps to `calculate`.

On x86-64 Linux, `call` pushes an 8-byte return address. So conceptually:

```text
Before call:  RSP = S
During call:  [S - 8] = address of after_call, RSP = S - 8
Jump to:      calculate
```

The CPU saves the place where execution should resume after `calculate` finishes.

## What happens in `calculate`

```asm
calculate:
    add rax, 5
    ret
```

`RAX` was 10 before the call, so it becomes 15 after `add rax, 5`. Then `ret` pops the saved return address from the stack and resumes execution at `after_call`.

In other words, the `ret` instruction does the opposite of `call`:

```text
Before ret:  RSP = S - 8, top of stack = address of after_call
After ret:   RSP = S, RIP = address of after_call
```

This is why execution continues at the instruction right after the `call`.

## Relevant runtime observation

I ran `./03`; it exited with status 0 and produced no visible result because the program does not print `RAX` or any other value. The important observation is the logical one: the return address was stored on the stack, the arithmetic occurred in `calculate`, and execution resumed at `after_call` after `ret`.

## Summary

This example demonstrates that the stack is used for control flow as well as data. `call` pushes a return address, `ret` removes it, and the function resumes where it left off. In effect, the stack preserves the caller's execution context while the callee runs.

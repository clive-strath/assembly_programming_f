# SUB examples

This folder includes subtraction examples that were assembled and linked locally:

- `sub1.asm` -> `sub1.o` -> `sub1`
- `sub2.asm` -> `sub2.o` -> `sub2`


## Example 1: sub1.asm

```asm
mov al, [num1]       ; AL = 50 = 0x32
sub al, [num2]       ; AL = 50 - 80 = -30 = 0xE2
```

### Why the flags are as they are

- `CF = 1`
  - In unsigned arithmetic, `50 - 80` requires a borrow because `50` is smaller than `80`. A borrow means the carry flag is set.
- `ZF = 0`
  - The result `0xE2` is not zero.
- `SF = 1`
  - The result has bit 7 set, so the sign bit is set. A value of `0xE2` is negative in signed 8-bit arithmetic.
- `OF = 0`
  - This is a valid signed result. `50 - 80 = -30`, and `-30` is within the signed 8-bit range `[-128, 127]`. No signed overflow occurred.
- `PF = 0`
  - `0xE2` has 5 set bits, which is odd; therefore the parity flag is cleared.

This example shows that a subtraction can produce a negative result without overflow, but it still sets `CF` because the operation required an unsigned borrow.

## Example 2: sub2.asm

```asm
mov ax, [num1]       ; AX = 1000 = 0x03E8
sub ax, [num2]       ; AX = 1000 - 2000 = -1000 = 0xFC18
```

### Why the flags are as they are

- `CF = 1`
  - Because `1000 < 2000`, subtraction requires a borrow. In x86, `sub` sets `CF` when a borrow happens.
- `ZF = 0`
  - The remainder is not zero.
- `SF = 1`
  - `0xFC18` has the sign bit set, so the result is negative in signed 16-bit form.
- `OF = 0`
  - `1000 - 2000 = -1000` is still a valid result inside the signed 16-bit range. No signed overflow occurred.
- `PF = 0`
  - `0xFC18` has an odd number of one bits, so the parity flag is cleared.

The key idea is that `CF` is about unsigned borrow, while `OF` is about signed overflow. In both subtraction examples, the borrow happened, but the signed result stayed inside the valid range.

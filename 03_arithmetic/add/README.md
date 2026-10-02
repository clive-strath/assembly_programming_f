# ADD examples

This folder includes several addition examples. The following programs were assembled and linked locally:

- `add1.asm` -> `add1.o` -> `add1`
- `add3.asm` -> `add3.o` -> `add3`


```

## Example 1: add1.asm

```asm
mov al, [num1]       ; AL = 120 = 0x78
add al, [num2]       ; AL = 120 + 10 = 130 = 0x82
```

### Why the flags are as they are

- `CF = 0`
  - There is no carry out of the highest bit. The addition `0x78 + 0x0A` gives `0x82`, and the carry bit is not set.
- `ZF = 0`
  - The result is `0x82`, not zero.
- `SF = 1`
  - The result bit 7 is 1, so the sign bit is set. In signed 8-bit form, `130` is interpreted as `-126`.
- `OF = 1`
  - This is the important signed-overflow case. `120` and `10` are both positive numbers, but the sum `130` is larger than the maximum signed 8-bit value `127`. The sign bit flips from positive to negative, so overflow is detected.
- `AF = 1`
  - Lower nibble addition: `0x8 + 0xA = 0x12`. A carry occurred from bit 3 to bit 4, so the auxiliary carry flag is set.
- `PF = 1`
  - `0x82` has two set bits, which is an even number, so the parity flag is set.

The important point is not only the raw result value, but the fact that the arithmetic crossed a signed boundary while still fitting in unsigned 8 bits.

## Example 2: add3.asm

```asm
mov ax, [num1]       ; AX = 0xFFFF = 65535
add ax, [num2]       ; AX = 65535 + 1 = 0x0000
adc ax, 0            ; carry is added into the result
```

### Why the flags are as they are

- `CF = 1`
  - `0xFFFF + 0x0001 = 0x10000`. The carry out of the 16-bit result is 1, so the carry flag is set.
- `ZF = 1`
  - The final result is zero, so the zero flag is set.
- `SF = 0`
  - The result has bit 15 cleared, so the sign flag is not set.
- `OF = 0`
  - In signed arithmetic, `-1 + 1 = 0`, which does not overflow. The result is valid and the overflow flag remains clear.
- `AF = 1`
  - `0xF + 1 = 0x10` creates a carry from bit 3 to bit 4, so the auxiliary carry flag is set.
- `PF = 1`
  - The final value is zero, and zero has an even number of one bits, so parity is even and the parity flag is set.

This example demonstrates how a carry out can happen even when the final result is zero, and why `CF` and `ZF` can be set at the same time for different reasons.

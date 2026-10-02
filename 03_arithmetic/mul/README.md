# MUL examples

This folder contains multiplication examples that were assembled and linked locally:

- `mul1.asm` -> `mul1.o` -> `mul1`
- `mul2.asm` -> `mul2.o` -> `mul2`


## Example 1: mul1.asm

```asm
mov al, [num1]      ; AL = 25
mul byte [num2]     ; AX = AL * 10 = 250 = 0x00FA
```

### Why the flags are as they are

- `CF = 0`
- `OF = 0`
  - For `mul r/m8`, the product is stored in `AX`. If the upper byte of the product is zero, there was no overflow beyond the 16-bit result. Here `250` is `0x00FA`, so `AH = 0x00` and no overflow bits are needed.
- `ZF = 0`
  - The final `AX` value is not zero.
- `SF = 0`
  - Bit 15 is clear, so the result is not negative.

The important part is that `mul` sets `CF` and `OF` to 1 only when the upper half of the product is non-zero. In this case, the product fits entirely in the lower 8 bits of the 16-bit result, so both flags are cleared.

## Example 2: mul2.asm

```asm
mov ax, [num1]      ; AX = 3000
mul word [num2]     ; DX:AX = AX * 200 = 600000
```

### Why the flags are as they are

- `CF = 1`
- `OF = 1`
  - For `mul r/m16`, the product is stored in `DX:AX`. If the upper half `DX` is non-zero, then the product could not fit in 16 bits, so both `CF` and `OF` are set.
  - `3000 * 200 = 600000 = 0x0927C0`. Since the result requires more than 16 bits, `DX` is non-zero and the overflow carry flags are set.
- `ZF = 0`
  - The result is not zero.
- `SF = 0`
  - The highest bit in the final `DX:AX` result is not set in the sign bit of the 16-bit low half, so the sign flag remains clear.

This example shows the core behavior of `mul`: the flags indicate whether the multiplication produced bits that spilled into the upper half of the result.

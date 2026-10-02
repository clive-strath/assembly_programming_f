# DIV examples

This folder includes division examples that were assembled and linked locally:

- `div1.asm` -> `div1.o` -> `div1`
- `div2.asm` -> `div2.o` -> `div2`



## Important note about flags and `div`

The `div` instruction does not define the normal arithmetic flags in the same way as `add`, `sub`, or `mul`.

For x86 unsigned division:

```asm
div r/m
```

- `AX` is divided by the operand.
- The quotient goes into `AL` or `AX` depending on operand size.
- The remainder goes into `AH` or `DX`.
- The `CF`, `OF`, `ZF`, `SF`, and `PF` flags are not used for the arithmetic result in a meaningful, reliable way. They are left undefined by the instruction.

The key idea is that `div` is about producing quotient and remainder, not about setting standard arithmetic condition codes.

## Example 1: div1.asm

```asm
mov ax, [dividend]  ; AX = 100
mov bl, [divisor]   ; BL = 7
div bl              ; AL = 14, AH = 2
```

### Why the flags are as they are

- `CF`, `OF`, `ZF`, `SF`, `PF` are undefined after `div bl`
  - The instruction performs integer division, not a flag-setting arithmetic calculation.
  - The result is stored as quotient and remainder: `100 / 7 = 14` remainder `2`.

This example shows why we should not interpret the flags after a division instruction as if it were a normal arithmetic operation.

## Example 2: div2.asm

```asm
mov ax, [dividend]  ; AX = 50000
mov dx, [highpart] ; DX = 0
mov bx, [divisor]  ; BX = 300
div bx             ; AX = 166, DX = 200
```

### Why the flags are as they are

- `CF`, `OF`, `ZF`, `SF`, `PF` are undefined after `div bx`
  - `50000 / 300 = 166` with remainder `200`.
  - The instruction only produces quotient and remainder, so it does not set the usual arithmetic flags in a way that is used by subsequent conditional jumps.

This is a good example of a division instruction whose output is meaningful even though the CPU does not provide a standard carry/zero/sign model for the operation.

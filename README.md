# sCPU: A Simple 8-Bit Processor for Series Summation

This project is a simple 8-bit processor built in **Logisim-evolution v4.0.0**. I designed it to perform iterative calculations, specifically for summing arithmetic sequences, and displaying the results on a hexadecimal screen.

##  Hardware Overview

- **Data Width**: 8-bit (supports values from 0 to 255).
- **Registers**: 4 general-purpose 8-bit registers (R1,R2,R3,R4).
- **Program Counter (PC)**: 4-bit (up to 16 instructions).
- **Output Stage**: A 16-bit hexadecimal display (two 7-segment digits) driven by a custom decoder.

##  Instruction Set Architecture (sISA)

The processor uses an 8-bit instruction format. Each instruction starts with a 2-bit Opcode.

```Plain
7  6 5  4 3   2 1   0
+----+----+-----+-----+
| 00 | rd | rs1 | rs2 | R[rd]=R[rs1]+R[rs2]       (Add registers)
+----+----+-----+-----+
| 01 | rs |   0000    | OUT rs                    (Show register on Hex display)
+----+----+-----+-----+
| 10 | rd |    imm    | R[rd]=imm                 (Load 4-bit constant, high bits 0)
+----+----+-----+-----+
| 11 |   addr   | rs2 | if (R[0]!=R[rs2]) PC=addr (Jump if not equal)
+----+----------+-----+
```

## Solving the Oscillation Problem

While building the `ADD` logic, I ran into a "Circuit Oscillation" error. This happened because the output of a register (like R2) was going through the adder and back into itself in the same clock cycle, creating a never-ending loop that crashed the simulation.

**How I fixed it:**

I added **Clock Gating** to the registers. Instead of the write-signal being "always on" during the instruction, I used an **AND gate** to combine the write-enable signal with the **Clock**. Now, the register only saves data at the exact moment the clock clicks (Rising Edge), which stops the feedback loop and makes the calculation stable.

## Example: Summing a Sequence ($1+3+5+...$)

This program adds numbers together and stops once the counter hits the limit.

0:   li: r0,11                                    10001011

1:   li: r1,1                                      10010001

2:   li: r2,0                                      10100000

3:   li : r3,2                                     10110010

4:   add r2,r2,r1                            00101001  

5:   add r1,r1,r3                            00010111

6:   bner0 r1,4                              11010001

7：out rs2                                    01100000

8:   bner0 r3,7                              11011111

1. ## How to Use

   1. Open main0.circ in **Logisim-evolution v4.0.0**.
   2. **Important**: Before starting, toggle the **Reset** signal (Set to 1 then back to 0) to ensure the PC is at `0` and registers are cleared.
   3. Enable the clock: Go to simulate-> Auto-Tick Enabled.
   4. The Hex display will update until the final sum is calculated and displayed.




# DNA-v32  (Definitely Not ARM Vector 32 bit) ISA Simulator
This is a custom 32-bit instruction set architecture (ISA) simulator inspired by 32-bit ARM and supports a vector processor for vector arithmetics. The project includes a fully functional 5-stage pipelined CPU simulator, a direct-mapped cache, a Python assembler, and an interactive Qt GUI for visualizing program execution. 

Several benchmarks were ran such as exchange sort and matrix multiply, with different pipeline and cache settings. Notably, vectorized matrix multiplication benchmarks demonstrate up to ~2.5x cycle reduction compared to scalar implementations when vector instructions are enabled.

This was developed collaboratively with [Neha Aryasomayajula](https://github.com/nehaka02)

## Demo / Screenshots 
_Put some videos here_


<details>
<summary>Below are some demo screenshots</summary>
Below is a screenshot of running a simple program (4x4 Matrix Multiply) to completion
<img width="831" height="540" alt="Screenshot 2026-09-10 at 9 41 53 PM" src="https://github.com/user-attachments/assets/57fe2055-49f5-48f3-ac2b-748a61d78a47" />

Below is a screenshot of running the same program until a breakpoint (PC=4)
<img width="829" height="538" alt="Screenshot 2026-09-10 at 9 42 43 PM" src="https://github.com/user-attachments/assets/2bc21af4-6b3b-45cd-aeb4-b810be67eeba" />

Besides matrix multiply, some exchange sort benchmarks were also ran, below are screenshots for the content of the cache after running exchange sort with pipeline+cache enabled and pipeline only modes. 
<img width="823" height="533" alt="Screenshot 2026-09-10 at 10 13 28 PM" src="https://github.com/user-attachments/assets/827ec40f-255e-441d-969a-e3d13213dfa4" />
<img width="820" height="532" alt="Screenshot 2026-09-10 at 10 14 12 PM" src="https://github.com/user-attachments/assets/d6e93b8b-14a6-4299-aed7-09b7d51cac12" />

Below are the contents of the vector registers after running our vector matrix multiplication and scalar matrix multiplication benchmarks respectively

<img width="821" height="533" alt="Screenshot 2026-09-10 at 10 16 32 PM" src="https://github.com/user-attachments/assets/dcea4c9e-e0b6-4636-b649-1d88018da62f" />
<img width="820" height="531" alt="Screenshot 2026-09-10 at 10 16 55 PM" src="https://github.com/user-attachments/assets/c4aaef43-8936-4430-a792-a7070a4ab177" />

</details>

## Build & Run Instructions 
### Requirements
- CMake 3.16+
- Qt 5 or Qt 6 (Core and Widgets modules)
- C++17 compatible compiler
- GTest (for unit tests)
- Python 3.x (for the assembler)

### How to run
Make sure Qt creator is installed locally. Qt can be downloaded via [the official site](https://www.qt.io/development/download)

Download this repo from github 
```bash
git clone https://github.com/liyu-hz168/DNA-v32.git
```
Open up the project in Qt creator, and build the project via <img height="20" alt="build button" src="https://github.com/user-attachments/assets/66786695-1dd2-430f-8a07-62ba6e578580" />

In the IDE, click on Projects on the left side bar, then go to run settings, make sure the working directory of the project is set to the root directory. If the project directory is ever changed, before running, make sure to delete the old build folder and rebuild. 

Then run via <img height="20" alt="run button" src="https://github.com/user-attachments/assets/aa9dd324-bd7f-4681-bf5f-9dd6082d36bf" />

To use this project, first load an assembly file via the Load File button in the assembler window, then press the Assemble button. In the tool bar at the top of the main window (shown in the image below), the user can chose to run the assembly file to completion via run, or set a breakpoint. Cache and Pipeline can enabled or disabled separately to test different modes. 

<img width="1063" height="79" alt="Screenshot 2026-09-11 at 1 59 52 AM" src="https://github.com/user-attachments/assets/98277058-fc17-45da-a0c5-eb9f9d190955" />


## ISA Specification (Be warned... Excessive tables ahead. Browse at your own risk)
Some basic info before more info dump. The word size is 32 bits, and instructions are 32 bits; hence, the instruction fetch paradigm is single instruction per word. The architecture supports 32-bit signed integers and 4-word vectors only. We employed Princeton-style memory organization. Memory is word addressable. The address range is from 0x00000000 to 0xFFFFFFFF, providing 2³² addressable words. In addition, we have a unified, direct-mapped, write-through, no-write allocate cache. Our cache is 64 words; that is 16 lines, each containing 4 words of data.
<details>
<summary>Addressing Modes</summary>
  
1. PC-relative addressing is supported for some branch instructions only.
2. Immediate addressing is supported for ALU instructions, except NOT. It is also supported for integer loads.
3. Register-direct addressing is supported for ALU instructions, except NOT. Register-indirect addressing is supported for the BX instruction. It is also supported for loads/stores.
4. Register base+index addressing is supported for loads/stores only.
</details>

<details>
<summary>Registers</summary>
Our ISA has a total of 32 registers. 16 of those registers are general-purpose vector registers. 12
are general-purpose integer registers. The remaining 4 registers are special-purpose integer
registers. The r12 register is the link register, which stores the return address of a subroutine
when it is called. The r13 register contains the program counter, which points to the current
instruction to fetch. It is updated automatically and cannot be modified by the program. The CR
register is the integer condition code register. The VCR register is the vector condition code
register. The latter two registers are inspired by ARM’s CPSR register.

| Register | Alias | Purpose |
|----------|-------|---------|
| r0  | —   | General Purpose |
| r1  | —   | General Purpose |
| r2  | —   | General Purpose |
| r3  | —   | General Purpose |
| r4  | —   | General Purpose |
| r5  | —   | General Purpose |
| r6  | —   | General Purpose |
| r7  | —   | General Purpose |
| r8  | —   | General Purpose |
| r9  | —   | General Purpose |
| r10 | —   | General Purpose |
| r11 | —   | General Purpose |
| r12 | LR  | Link Register |
| r13 | PC  | Program Counter |
| r14 | CR  | Condition Register |
| r15 | VCR | Vector Condition Register |

Note, in addition we have sixteen general-purpose 4-word vector registers. The registers are named Q0-Q15.
</details>

<details>
<summary>Instruction Details & Structure</summary>
All instructions are 32 bits wide. The first 2 bits indicate the type code, dividing instructions into three categories.

### Data Processing (type code: `00`)

| Instruction | Assembly | Opcode | Function |
|-------------|----------|--------|----------|
| Integer Addition | `ADD dest, src1, src2` | 00000 (0) | dest = src1 + src2 |
| Integer Subtraction | `SUB dest, src1, src2` | 00001 (1) | dest = src1 - src2 |
| Integer Division | `DIV dest, src1, src2` | 00010 (2) | dest = src1 / src2. If src2 = 0, dest = 0xFFFFFFFF (RISC-V convention) |
| Integer Multiplication | `MUL dest, src1, src2` | 00011 (3) | dest = src1 * src2. Keeps lower 32 bits on overflow, sets V flag |
| Integer Modulus | `MOD dest, src1, src2` | 00100 (4) | dest = src1 % src2. If src2 = 0, dest = src1 (RISC-V convention) |
| Arithmetic Right Shift | `ASR dest, src1, src2` | 00101 (5) | dest = src1 >> src2 |
| Arithmetic Left Shift | `ASL dest, src1, src2` | 00110 (6) | dest = src1 << src2 |
| Logical Right Shift | `LSR dest, src1, src2` | 00111 (7) | dest = src1 >> src2 |
| Logical Left Shift | `LSL dest, src1, src2` | 01000 (8) | dest = src1 << src2 |
| Bitwise AND | `AND dest, src1, src2` | 01001 (9) | dest = src1 & src2 |
| Bitwise OR | `OR dest, src1, src2` | 01010 (10) | dest = src1 \| src2 |
| Bitwise XOR | `XOR dest, src1, src2` | 01011 (11) | dest = src1 ^ src2 |
| Vector Addition | `VADD length(#), dest, src1, src2` | 01100 (12) | dest = src1 + src2 (element-wise). Sets V on overflow |
| Vector Subtraction | `VSUB length(#), dest, src1, src2` | 01101 (13) | dest = src1 - src2 (element-wise). Sets V on underflow |
| Vector Multiplication | `VMUL length(#), dest, src1, src2` | 01110 (14) | dest = src1 * src2 (element-wise). Keeps lower 32 bits, sets V on overflow |
| Immediate Addition | `ADDI dest, src1, #` | 01111 (15) | dest = src1 + # |
| Immediate Subtraction | `SUBI dest, src1, #` | 10000 (16) | dest = src1 - # |
| Immediate Multiplication | `MULI dest, src1, #` | 10001 (17) | dest = src1 * #. Keeps lower 32 bits, sets V on overflow |
| Immediate Arith. Right Shift | `ASRI dest, src1, #` | 10010 (18) | dest = src1 >> # |
| Immediate Arith. Left Shift | `ASLI dest, src1, #` | 10011 (19) | dest = src1 << # |
| Immediate Bitwise AND | `ANDI dest, src1, #` | 10100 (20) | dest = src1 & # |
| Immediate Bitwise OR | `ORI dest, src1, #` | 10101 (21) | dest = src1 \| # |
| Immediate Bitwise XOR | `XORI dest, src1, #` | 10110 (22) | dest = src1 ^ # |
| Immediate Division | `DIVI dest, src1, #` | 10111 (23) | dest = src1 / #. If # = 0, dest = 0xFFFFFFFF |
| Immediate Modulus | `MODI dest, src1, #` | 11000 (24) | dest = src1 % #. If # = 0, dest = src1 |
| Immediate Logical Right Shift | `LSRI dest, src1, #` | 11001 (25) | dest = src1 >> # |
| Immediate Logical Left Shift | `LSLI dest, src1, #` | 11010 (26) | dest = src1 << # |
| Compare | `CMP src1, src2` | 11011 (27) | src1 - src2 (background). Sets N, Z, V flags |
| Vector Equivalence | `VEQ length(#), src1, src2` | 11100 (28) | src1 - src2 (background). Sets Z if result is zero vector |
| Vector Sum | `VSUM length(#), dest, src1` | 11101 (29) | dest = sum of all elements in src1 |
| Compare Immediate | `CMPI src1, #` | 11110 (30) | src1 - # (background). Sets N, Z, V flags |

**Instruction Structure**

**Scalar (except CMP and immediate shifts)**
| Type code | Opcode | dest | src1 | src2 | Leftover |
|-----------|--------|------|------|------|----------|
| 2 bits | 5 bits | 4 bits | 4 bits | 4 bits | 13 bits |

**CMP**
| Type code | Opcode | src1 | src2 | Leftover |
|-----------|--------|------|------|----------|
| 2 bits | 5 bits | 4 bits | 4 bits | 17 bits |

**Immediate (except CMPI)**
| Type code | Opcode | dest | src1 | Immediate |
|-----------|--------|------|------|-----------|
| 2 bits | 5 bits | 4 bits | 4 bits | 17 bits |

**Vector (except VEQ)**
| Type code | Opcode | Vector length | dest | src1 | src2 | Leftover |
|-----------|--------|---------------|------|------|------|----------|
| 2 bits | 5 bits | 2 bits | 4 bits | 4 bits | 4 bits | 11 bits |

**VEQ**
| Type code | Opcode | Vector length | src1 | src2 | Leftover |
|-----------|--------|---------------|------|------|----------|
| 2 bits | 5 bits | 2 bits | 4 bits | 4 bits | 15 bits |

**VSUM**
| Type code | Opcode | Vector length | src1 | dest | Leftover |
|-----------|--------|---------------|------|------|----------|
| 2 bits | 5 bits | 2 bits | 4 bits | 4 bits | 15 bits |

**CMPI**
| Type code | Opcode | src1 | Immediate |
|-----------|--------|------|-----------|
| 2 bits | 5 bits | 4 bits | 21 bits |

> **Note:** The vector length field specifies the number of active elements (1–4) in vector operations.
---

### Branch Operations (type code: `01`)

| Instruction | Assembly | Opcode | Function |
|-------------|----------|--------|----------|
| Unconditional Branch | `B offset` | 0000 | Always branch to PC-relative offset |
| Branch if Equal | `BEQ offset` | 0001 | Branch if Z = 1 |
| Branch if Not Equal | `BNE offset` | 0010 | Branch if Z = 0 |
| Branch if Less Than | `BLT offset` | 0011 | Branch if N = 1 |
| Branch if Less or Equal | `BLE offset` | 0100 | Branch if N = 1 or Z = 1 |
| Branch if Greater Than | `BGT offset` | 0101 | Branch if N = 0 |
| Branch if Greater or Equal | `BGE offset` | 0110 | Branch if N = 0 or Z = 1 |
| Branch with Link | `BL offset` | 0111 | Store return address in LR, branch to subroutine |
| Branch and Exchange | `BX src` | 1000 | Branch to address in src (register-indirect) |

---

### Miscellaneous (type code: `10`)

| Instruction | Assembly | Opcode | Function |
|-------------|----------|--------|----------|
| Bitwise NOT | `NOT dest, src` | 0000 | dest = !src |
| Load | `LD dest, src` | 0001 | dest = MEM[src] |
| Store | `STR dest, src` | 0010 | MEM[dest] = src |
| Vector Load | `VLD dest, src` | 0011 | dest = MEM[src] (4-word aligned) |
| Vector Store | `VSTR dest, src` | 0100 | MEM[dest] = src (4-word aligned) |
| Halt | `HALT` | 0101 | Pause program execution |
| NOP | — | — | Any invalid instruction is treated as NOP |
| Load Base+Offset | `LDB dest, base, offset` | 0110 | dest = MEM[base + offset] |
| Store Base+Offset | `STRB src, base, offset` | 0111 | MEM[base + offset] = src |
| Load Immediate | `LDI dest, #` | 1000 | dest = MEM[#] |

> **Note:** VLD and VSTR enforce 4-word alignment by zeroing the lowest 2 bits of the address. Be careful with unaligned addresses as this may cause unintended memory overwrites.

---
</details>






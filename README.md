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





# DNA-v32  (Definitely Not ARM Vector 32 bit) ISA Simulator
This is a custom 32-bit instruction set architecture (ISA) simulator inspired by 32-bit ARM and supports a vector processor for vector arithmetics. The project includes a fully functional 5-stage pipelined CPU simulator, a direct-mapped cache, a Python assembler, and an interactive Qt GUI for visualizing program execution. 

Several benchmarks were ran such as exchange sort and matrix multiply, with different pipeline and cache settings. Notably, vectorized matrix multiplication benchmarks demonstrate up to ~2.5x cycle reduction compared to scalar implementations when vector instructions are enabled.

This was developed collaboratively with _(REMEMBER TO CREDIT NEHA)_

## Demo / Screenshots 
_Put some videos here_

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

## Build & Run Instructions 





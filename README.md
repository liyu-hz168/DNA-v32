# DNA-v32  (Definitely Not ARM Vector 32 bit) ISA Simulator
This is a custom 32-bit instruction set architecture (ISA) simulator inspired by 32-bit ARM and supports a vector processor for vector arithmetics. The project includes a fully functional 5-stage pipelined CPU simulator, a direct-mapped cache, a Python assembler, and an interactive Qt GUI for visualizing program execution. 

Several benchmarks were ran such as exchange sort and matrix multiply, with different pipeline and cache settings. Notably, vectorized matrix multiplication benchmarks demonstrate up to ~2.5x cycle reduction compared to scalar implementations when vector instructions are enabled.

This was developed collaboratively with _(REMEMBER TO CREDIT NEHA)_

## Demo 
_Put some videos here_

Below is a screenshot of running a simple program (4x4 Matrix Multiply) to completion
<img width="831" height="540" alt="Screenshot 2026-09-10 at 9 41 53 PM" src="https://github.com/user-attachments/assets/57fe2055-49f5-48f3-ac2b-748a61d78a47" />

Below is an example of running the same program until a breakpoint (PC=4)
<img width="829" height="538" alt="Screenshot 2026-09-10 at 9 42 43 PM" src="https://github.com/user-attachments/assets/2bc21af4-6b3b-45cd-aeb4-b810be67eeba" />



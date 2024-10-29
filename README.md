# VLSI Physical Design Project - JBI
## Open SPARC T1 Processor
### System level Block diagram:

![Screenshot from 2024-10-29 13-28-43](https://github.com/user-attachments/assets/e60c62cf-a42c-40de-88cd-29988704fbb5)

### SPARC Core

Each SPARC core has hardware support for four threads. This support consists of a full register file (with eight register windows) per thread, with most of the address space identifiers (ASI), ancillary state registers (ASR), and privileged registers replicated per thread. The four threads share the instruction, the data caches, and the TLBs. Each instruction cache is 16 Kbytes with a 32-byte line size. The data caches are write through, 8 Kbytes, and have a 16-byte line size. The TLBs include an autodemap feature which enables the multiple threads to update the TLB without locking

Each SPARC core has single issue, six stage pipeline. These six stages are:

1.Fetch

2.Thread Selection

3.Decode

4.Execute

5.Memory

6.Write Back

Each SPARC core has the following units:

1.Instruction fetch unit (IFU) includes the following pipeline stages - fetch, thread selection, and decode. The IFU also includes an instruction cache complex.

2. Execution unit (EXU) includes the execute stage of the pipeline.

3. Load/store unit (LSU) includes memory and writeback stages, and a data cache complex.

4. Trap logic unit (TLU) includes trap logic and trap program counters. 5. Stream processing unit (SPU) is used for modular arithmetic functions for crypto. 6. Memory management unit (MMU).

7. Floating-point frontend unit (FFU) interfaces to the FPU.

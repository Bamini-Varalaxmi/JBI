# OpenSparc T1 (Detailed)
Sun Microsystems began shipping the UltraSPARC T1 chip multithreaded (CMT) processor in December 2005. Sun surprised the
industry by announcing that it would not only ship the processor
but also open-source the RTL design of this processor. By March
2006, UltraSPARC T1 had been open-sourced in a distribution called
OpenSPARC T1.

## OpenSPARC T1 architecture
The OpenSPARC T1 processor is a highly integrated processor that
implements the 64-bit SPARC V9 architecture. The OpenSPARC
T1 processor contains eight SPARC processor cores, which each have
full hardware support for four threads. Each SPARC core has an
instruction cache, a data cache, and a fully associative instruction
and data translation lookaside buffers (TLB). The eight SPARC cores
are connected through a crossbar to an on-chip unified level 2 cache
(L2-cache).
The four on-chip dynamic random access memory (DRAM) controllers directly interface to the double data rate-synchronous DRAM
(DDR2 SDRAM). Additionally, there is an on-chip J-Bus controller
that provides an interconnect between the OpenSPARC T1 processor
and the I/O subsystem.
The features of the OpenSPARC T1 processor include [4]:
• 8 SPARC V9 CPU cores, with 4 threads per core, for a total of
32 threads

• 132 Gbytes/sec crossbar interconnect for on-chip communication

• 16 Kbytes of primary (Level 1) instruction cache per CPU core

• 8 Kbytes of primary (Level 1) data cache per CPU core

• 3 Mbytes of secondary (Level 2) cache - 4 way banked, 12 way
associative shared by all CPU cores

• 4 DDR-II DRAM controllers - 144-bit interface per channel, 25
GBytes/sec peak total bandwidth

• IEEE 754 compliant floating-point unit (FPU), shared by all
CPU cores
External interfaces:
• J-Bus interface (JBI) for I/O - 2.56 Gbytes/sec peak bandwidth,
128-bit multiplexed address/data bus

• Serial system interface (SSI) for boot PROM

The Figure below shows a block diagram of the OpenSPARC T1 processor
illustrating the various interfaces and integrated components of the
chip.

![Procesador-OpenSPARC-T1](https://github.com/user-attachments/assets/55a3c3f5-c5e1-4bcd-97d6-420bd1d6b7e3)

                   OpenSPARC T1 Processor Block Diagram
OpenSPARC T1 includes the following components:

1. 8 SPARC Cores
2. Floating-Point Unit
3. CPU-Cache Crossbar
4. L2-Cache
5. DRAM Controller
6. I/O Bridge
7. J-Bus Interface
8. Serial System Interface
9. Electronic Fuse
   
In the following sections briefly describe each component.

### SPARC Core
Each SPARC core has hardware support for four threads. This support
consists of a full register file (with eight register windows) per thread,
with most of the address space identifiers (ASI), ancillary state registers (ASR), and privileged registers replicated per thread. The four
threads share the instruction, the data caches, and the TLBs. Each instruction cache is 16 Kbytes with a 32-byte line size. The data caches
are write through, 8 Kbytes, and have a 16-byte line size. The TLBs include an autodemap feature which enables the multiple threads to
update the TLB without locking.
Each SPARC core has single issue, six stage pipeline. These six
stages are:

1. Fetch
2. Thread Selection
3. Decode
4. Execute
5. Memory
6. Write Back

![Screenshot from 2024-10-30 21-04-25](https://github.com/user-attachments/assets/e42942a3-23b8-47c1-b6aa-22bbb29b16fa)

                      SPARC Core Pipeline

Each SPARC core has the following units:
1. Instruction fetch unit (IFU) includes the following pipeline stages
- fetch, thread selection, and decode. The IFU also includes an
instruction cache complex.
2. Execution unit (EXU) includes the execute stage of the pipeline.

# VLSI Physical Design Project - JBI
## Open SPARC T1 Processor
### System level Block diagram:

![opskt1](https://github.com/user-attachments/assets/24ef0ab8-d456-4590-9946-b42238d3dddc)


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

### Instruction Fetch Unit

The thread selection policy is as follows - a switch between the available threads every cycle giving priority to the least recently executed thread. The threads become unavailable due to the long latency operations like loads, branch, MUL, and DIV, as well as to the pipeline stalls like cache misses, traps, and resource conflicts. The loads are speculated as cache hits, and the thread is switched-in with lower priority.

Instruction cache complex has a 16-Kbyte data, 4-way, 32-byte line size with a single ported

instruction tag. It also has dual ported (1R/1W) valid bit array to hold cache line state of valid/invalid. Invalidates access the V-bit array, not the instruction tag. A pseudo-random replacement algorithm is used to replace the cache line.

There is a fully associative instruction TLB with 64 entries. The buffer supports the following page sizes: 8 Kbytes, 64 Kbytes, 4 Mbytes, and 256 Mbytes. The TLB uses a pseudo least recently used (LRU) algorithm for replacement. Multiple hits in the TLB are prevented by doing an autodemap on a fill.

Two instructions are fetched each cycle, though only one instruction is issued per clock, which reduces the instruction cache activity and allows for an opportunistic line fill. There is only one outstanding miss per thread, and only four per core. Duplicate misses do not issue requests to the L2-cache.

The integer register file (IRF) of the SPARC core has 5 Kbytes with 3 read/2 write/1 transport ports. There are 640 64-bit registers with error correction code (ECC). Only 32 registers from the current window are visible to the thread. Window changing in background occurs under the thread switch. Other threads continue to access the IRF (the IRF provides a single-cycle read/write access).

### Execution Unit

The execution unit (EXU) has a single arithmetic logic unit (ALU) and shifter. The ALU is reused for branch address and virtual address calculation. The integer multiplier has a 5 clock latency, and a throughput of half-per-cycle for area saving. One integer multiplication is allowed outstanding per core. The integer multiplier is shared between the core pipe (EXU) and the modular arithmetic (SPU) unit on a round-robin basis. There is a simple non-restoring divider, which allows for one divide outstanding per SPARC core. Thread issuing a MUL/DIV will be rolled back and switched out if another thread is occupying the MUL/DIV units.

### Load/Store Unit

The data cache complex has an 8-Kbyte data, 4-way, 16-byte line size. It also has single ported data tag. There is a dual-ported (1R/1W) valid bit array to hold cache line state of valid or invalid. Invalidates access the V-bit array but not the data tag. A pseudo-random replacement algorithm is used to replace the data cache line. The loads are allocating, and the stores are non-allocating. The data TLB operates similarly to the instruction TLB.

The load/store unit (LSU) has an 8 entry store buffer per thread, which is unified into a single 32 entry array, with RAW bypassing. Only a single load per thread outstanding is allowed. Duplicate requests for the same line are not sent to the L2-cache. The LSU has interface logic to interface to the CPU-cache crossbar (CCX). This interface performs the following operations:

■Prioritizes the requests to the crossbar for floating-point operation (Fpops), streaming operations. 15 and DS misses, stores and interrupts, and so on.

■Request priority: imiss>Idmiss>stores, fpu,stream.interrupt).

■Assembles packets for the processor-cache crossbar (PCX).

The LSU handles returns from the CPX crossbar and maintains the order for cache updates and invalidates.

### Floating-Point Frontend Unit

The floating-point frontend unit (FFU) decodes floating-point instructions and it also includes the floating-point register file (FRF). Some of the floating-point instructions like move, absolute value, and negate are implemented in the FFU, while the others are implemented in the FPU. The following steps are taken when the FFU detects a floating-point operation

(Fpop):

The thread switches out.

■The Fpop is further decoded and the FRF is read.

■Fpops with operands are packetized and shipped over the crossbar to the FPU. The

computation is done in the FPU and the results are returned by way of the crossbar.

Writeback completed to the FRF and the thread restarts.

### Trap Logic Unit

The trap logic unit (TLU) has support for six trap levels. Traps cause pipeline flush and thread switch until trap program counter (PC) becomes available. The TLU also has support for up to 64 pending interrupts per thread.

### Stream Processing Unit

The stream processing unit (SPU) includes a modular arithmetic unit (MAU) for crypto (one per core), and it supports asymmetric crypto (public key RSA) for up to a 2048-byte size key. It shares an integer multiplier for modular arithmetic operations. MAU can be used by one thread at a time. The MAU operation is set up by the store to control register, and the thread returns to normal processing. The MAU unit initiates streaming load/store operations to the L2-cache through the crossbar, and compute operations to the multiplier. Completion of the MAU can be checked by polling or issuing an interrupt.

### CPU-Cache Crossbar

The eight SPARC cores, the four L2-cache banks, the I/O Bridge, and the FPU all interface with the crossbar. FIGURE displays the crossbar block diagram. The CPU-cache crossbar (CCX) features include:

■Each requester queues up to two packets per destination.

■Three stage pipeline - request, arbitrate, and transmit.

■Centralized arbitration with oldest requester. getting priority.

■Core-to-cache bus optimized for address plus doubleword store.

■Cache-to-core bus optimized for 16-byte line fill. 32-byte IS line fill delivered in two back to-back clocks.

![Screenshot from 2024-10-29 15-13-43](https://github.com/user-attachments/assets/33827162-8718-4638-9aea-2b06c344afa7)

### Floating-Point Unit

A single floating-point unit (FPU) is shared by all eight SPARC cores. The shared floating point unit is sufficient for most commercial applications in which typically less than one percent of the instructions are floating-point operations.

### L2-Cache

The L2-cache is banked four ways, with the bank selection based on the physical address bits 7:6. The cache is 3-Mbyte, 12-way set-associative with pseudo-least recently used (LRU) replacement (the replacement is based on a used bit scheme). The line size is 64 bytes. Unloaded access time is 23 cycles for an L1 data cache miss and 22 cycles for an L1 instruction cache miss.

L2-cache has a 64-byte line size, with 64 bytes interleaved between banks. Pipeline latency in the L2-cache is 8 clocks for a load, 9 clocks for an I-miss, with the critical chunk returned first. 16 outstanding misses per bank are supported for a 64 total misses. Coherence is maintained by shadowing the Ll tags in an L2-cache directory structure (the L2-cache is a point of global visibility). DMA from the I/O is serialized with respect to the traffic from the cores in the L2-cache.

The L2-cache directory shadows the Ll tags. The Ll set index and the L2-cache bank interleaving is such that one forth of the LI entries come from an L2-cache bank. On an L.1 miss, the L1 replacement way and set index identifies the physical location of the tag which will be updated by the miss address. On a store, the directory will be camned. The directory entries are collated by set, so only 64 entries need to be cammed. This scheme is quite power efficient. Invalidates are a pointer to the physical location in the L1-cache, eliminating the need for a tag lookup in the LI-cache.

Coherency and ordering in the 1.2-cache are described as:

■Loads update directory and fill the L1-cache on return

■Stores are non-allocating in the 1:1-cache

■There are two flavors of stores: total store order (TSO) and read memory order (RMO).

Only one outstanding TSO store to the 1.2-cache per thread is permitted in order to preserve the store ordering. There is no such limitation on RMO stores,

■No tag check is done at a store buffer insert

■Stores check directory and determines an L1-cache hit

■Directory sends store acknowledgements or invalidates to the SPARC core

■Store updates happens to DS on a store acknowledge

■Crossbar orders the responses across cache banks.

### DRAM Controller

The OpenSPARC TI processor DRAM controller is banked four ways, with each L2 bank interacting with exactly one DRAM controller bank (a two-bank option is available for cost constrained minimal memory configurations). The DRAM controller is interleaved based on physical address bits 7:6, so each DRAM controller bank must have identical dual in-line memory modules (DIMM) installed and enabled.

The OpenSPARC TL processor uses DDR2 DIMMs and can support one or two ranks of stacked or unstacked DIMMs. Each DRAM bank/port is two-DIMMs wide (128-bit +16-bit ECC). All installed DIMMs must be identical, and the same number of DIMMs (that is, ranks) must be installed on each DRAM controller port. The DRAM controller frequency is an exact ratio of the core frequency, where the core frequency must be at least three times the DRAM controller frequency. The double data rate (DDR) data buses transfer data at twice the frequency of the DRAM controller frequency.

The OpenSPARC TI processor can support memory sizes of up to 128 Gbytes with a 25 Gbytes/sec peak bandwidth limit. Memory access is scheduled across 8 reads plus 8 writes, and the processor can be programmed into a two-channel mode for a reduced configuration. Each DRAM channel has 128 bits of data and 16 bytes of ECC interface, with chipkill support, nibble error correction, and byte error detection.

### I/O Bridge

The I/O bridge (IOB) performs an address decode on I/O-addressable transactions and directs them to the appropriate internal block or to the appropriate external interface (J-Bus or the

serial system interface). Additionally, the IOB maintains the register status for external interrupts

## J-Bus Interface (JBI)

The J-Bus interface (JBI) is the interconnect between OpenSPARC TI processor and the 10 subsystem. The J-Bus is a 200 MHz, 128-bit wide, multiplexed address or data bus, used predominantly for direct memory access (DMA) traffic, plus the programmable input output (PIO) traffic used to control it.

The J-Bus interface is the functional block that interfaces to the J-Bus, receiving and responding to DMA requests, routing them to the appropriate L2 banks, and also issuing PIO transactions on behalf of the processor threads and forwarding responses back.

Functional Description

For a detailed description on the external J-Bus interface, refer to OpenSPARC TI Processor External Interface Specification. The OpenSPARC TI J-Bus interface (JBI) block generates J-Bus transactions and responds to external J-Bus transactions.

The JBI block:

* Interfaces with following blocks in an OpenSPARC TI processor:

L2-cache (scbuf and sctag) to read and write data to L2-cache

* I/O Bridge (IOB) - for programmed input/output (PIO), interrupts, and debug port.

J-Bus I/O pads

Most of the JBI sub-blocks use the J-Bus clock, and remaining part runs at the CPU core clock or emp clk. The data transfer between the two clock domains is by way of queues within the two clock domains, these are the Request header queues and the Return data queues. The interface to the L2-cache is through the direct memory access (DMA) reads and DMA writes.

* The IOB debug port data is stored in the debug FIFOs and then it is sent out to the external J-Bus.

* IOB PIO requests are stored in the PIO queue and the return data is stored in the PIO return queue. Similarly, there is an interrupt queue and an interrupt ACK/NACK queues in the JBI in order to interface to the IOB.

* There are only two sub-blocks in the JBI (J-Bus parser and J-Bus transaction issue) specific to J-Bus. All of the other blocks are J-Bus independent. J-Bus independent blocks can be used for any other external bus interface implementation.

JBI Functional Block Diagram

![Screenshot from 2024-10-29 15-52-27](https://github.com/user-attachments/assets/7a1fdc9f-d94f-44cd-b487-8e74606c6d94)


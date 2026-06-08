# Asynchronous FIFO Design in Verilog

A parameterizable, N-byte Asynchronous First-In, First-Out (FIFO) memory buffer designed in Verilog. This project implements industry-standard Clock Domain Crossing (CDC) strategies to transfer data safely and reliably between two completely independent, unsynchronized clock domains (Write Clock and Read Clock).

##  Key Features

*   **Parameterizable Architecture:** Fully parameterizable data width (`DATA_WIDTH`) and memory depth (`FIFO_DEPTH`) to adapt to different system requirements.
*   **Safe Clock Domain Crossing (CDC):** Utilizes **Gray Code encoding** for write and read pointers, ensuring only a single bit changes per increment cycle to mitigate bus-skew sampling errors.
*   **Metastability Mitigation:** Implemented standard **Dual Flip-Flop (2-FF) Synchronizers** to safely pass control pointers across the asynchronous clock boundary.
*   **Accurate Flag Generation:** Advanced Full and Empty flag generation using Most Significant Bit (MSB) comparisons to prevent catastrophic data overflow or underflow.

---

##  Architecture & Block Diagram

The system architecture decouples the write and read paths, using dedicated control logic blocks and synchronizers to bridge the two distinct clock domains.

```text
                  +-----------------------------------+
   wdata -------->|                                   |--------> rdata
   winc  -------->|        Dual-Port RAM Memory       |--------> rinc
   wclk  -------->|                                   |--------> rclk
                  +-----------------------------------+
                     ^                             ^
                     |                             |
            +-----------------+           +-----------------+
            |  Write Control  |           |   Read Control  |
            |   & Flag Gen    |           |    & Flag Gen   |
            +-----------------+           +-----------------+
               |           ^                 |           ^
        Binary |           | Synchronized    | Binary    | Synchronized
        to Gray|           | Gray Pointer    | to Gray   | Gray Pointer
               v           |                 v           |
         +-----------+  +-----------+  +-----------+  +-----------+
         | Gray Pointer|  |  2-FF     |  | Gray Pointer|  |  2-FF     |
         |  Output   |  |Synchronizer|  |  Output   |  |Synchronizer|
         +-----------+  +-----------+  +-----------+  +-----------+
               |              ^              |              ^
               +--------------|--------------+              |
                              |                             |
                              +-----------------------------+
                                   Cross-Domain Pointers

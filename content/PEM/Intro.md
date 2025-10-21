---
date:
  - 2025-10-21
tags:
  - PEM
  - Introduzione
---
There are two types of parallel systems:
- **Shared-memory**: The cores have access to the computer's memory. The cores coordinate by analyzing and updating shared memory regions.
- **Distributed-memory**: Each core has its own memory and they communicate via message passing over the network (not necessarily the Internet).

Parallelism can occur, in this context, in two ways:
- **Multiple-Instruction Multiple-Data (MIMD)**: Each core operates independently from the others.
- **Single-Instruction Multiple-Data (SIMD)**: Each core executes a portion of the code or remains idle.

![[Pasted image 20251021113524.png]]
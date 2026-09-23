---
date:
  - 2025-10-07
tags:
  - PEM
---
## Concurrent vs. Parallel vs. Distributed

There isn’t a complete agreement on the definition, but: 
- **Concurrent**: Multiple tasks can be in progress at any time 
- **Parallel**: Multiple tasks cooperate closely to solve a problem 
- **Distributed**: A program might need to cooperate with other programs to solve a problem 

> [!info] In detail:
> 1. Parallel and distributed are concurrent 
> 2. Concurrent programs can be serial (e.g., multitasking operating system running on a single core(interleaving))
> 3. Parallel: tightly coupled (cores share the memory or are connected through a fast network)
> 4. Distributed: more loosely coupled (e.g., services connected through the Internet)

## Know your hardware
 
We want to write efficient code, in order to achieve this goal we need to know our hw and optimize for that configuration.

### Von Neumann's Architecture

![[Pasted image 20251007174152.png|400]]

• **Main memory**: Collection of locations. Each has an address (used to access that location) and some content (data or instruction). 
• **CPU/Processor/Core**: Control unit (decides which instructions execute) and datapath (executes the instructions) – The state of an executing program is stored in registers (very fast storage) – An important register in the control unit is the program counter (PC), storing the address of the next instruction to execute 
• **Interconnect**: used to transfer data between CPU and memory. Traditionally a bus, but can be much more complex (we will get back to it)

> [!bug] The interconnect determines the rate at which data is transferred (Von Neumann Bottleneck)

![[Pasted image 20251007174415.png]]

## MPI

Referred as a Single-Program Multiple-Data (SPMD) (compiled program executed by all processes)

![[Pasted image 20251007175022.png]]

If there are conditionals, I split the work into multiple processes (for example, if I am process X, I will do Y; if I am process Z, I will do K).

> [!hint] Note:
> Remember that there is no shared memory, so coordination occurs through message passing.

```C
#include <stdio.h> 
#include <mpi.h>
int main(void) { 
MPI_Init(NULL, NULL); #Sempre prima chiamata (per chiamate mpi)
printf(“hello, world\n”); 
MPI_Finalize(); #Sempre ultima chiamata (per chiamate mpi)
return 0; 
}
```

- MPI_Init: Tells to MPI to start and setup everything.

![[Pasted image 20251007175959.png]]

- MPI_Finalize: tells to clean everything and terminate (the MPI operations)
![[Pasted image 20251007180052.png]]

> [!done] Return di entrambi sono int che se diversi da 0 significano errori con codici corrispondenti

![[Pasted image 20251007181150.png|500]]

## Compilazione

![[Pasted image 20251007181229.png|600]]

## Execution

![[Pasted image 20251007181345.png]]
![[Pasted image 20251007181356.png]]

## Debugging

E' molto piu difficile debuggare con piu processi rispetto ad un programma sequenziale
Usando MPI con ddd (o gdb) su un processo:

```bash
mpiexec –n 4 ./test : -n 1 ddd ./test : -n 1 ./test
```

Lancia il quinto processo sotto "ddd" e tutti gli altri processi normalmente
### Identificare processi MPI

Common practice to identify processes by nonnegative integer ranks.
p processes are numbered 0, 1, 2, .. p-1
## Communicators

Una collezione di processi che si scambiano messaggi tra di loro. MPI_Init definisce un comminicatore che consiste di tutti i processi creati quando il programma viene fatto partire. 
Chiamato **MPI_COMM_WORLD** (actually a macro)

![[Pasted image 20251007181918.png]]

### Hello World (v.1)

```C
#include <stdio.h>
#include <mpi.h>
int main(void) { 
int comm_sz, my_rank; 
MPI_Init(NULL, NULL); 
MPI_Comm_size(MPI_COMM_WORLD, &comm_sz); 
MPI_Comm_rank(MPI_COMM_WORLD, &my_rank); 
printf(“hello, world from process %d out of %d\n”, my_rank, comm_sz); MPI_Finalize(); 
return 0; 
}
```

![[Pasted image 20251007182545.png|600]]

Questo può capitare "grazie" allo scheduler.
## Communication

![[Pasted image 20251007182802.png]]

> [!warning] In msg_size sono il numero di elementi non il numero di bytes

![[Pasted image 20251007182914.png]]

> [!warning] In buf_size sono il numero di elementi non il numero di bytes

### Hello World (v.2)

![[Pasted image 20251007183032.png]]


> [!warning]  Nonovertaking messages
> MPI requires that messages be nonovertaking. This means that if process q sends two messages to process r, then the first message sent by q must be available to r before the second message. (However, there is no restriction on the arrival of messages sent from different processes)

## DataType

| MPI datatype       | C datatype           |
| ------------------ | -------------------- |
| MPI_CHAR           | signed char          |
| MPI_SHORT          | signed short int     |
| MPI_LONG           | signed long int      |
| MPI_LONG_LONG      | signed long long int |
| MPI_UNSIGNED_CHAR  | unsigned char        |
| MPI_UNSIGNED_SHORT | unsigned short int   |
| MPI_UNSIGNED       | unsigned int         |
| MPI_UNSIGNED_LONG  | unsigned long        |
| MPI_FLOAT          | float                |
| MPI_DOUBLE         | double               |
| MPI_LONG_DOUBLE    | long double          |
| MPI_BYTE           |                      |
| MPI_PACKED         |                      |

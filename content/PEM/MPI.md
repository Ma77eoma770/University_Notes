---
date:
  - 2025-10-07
tags:
  - PEM
---
...type of parallelel systems

Concurrent vs. Parallel vs. Distributed • There isn’t a complete agreement on the definition, but: • Concurrent: Multiple tasks can be in progress at any time 
Parallel: Multiple tasks cooperate closely to solve a problem 
Distributed: A program might need to cooperate with other programs to solve a problem 

Parallel and distributed are concurrent • Concurrent programs can be serial (e.g., multitasking operating system running on a single core(interleaving))

Parallel: tightly coupled (cores share the memory or are connected through a fast network)
Distributed: more loosely coupled (e.g., servicesconnected through the Internet)

## Know your hardware

Vogliamo scrivere codice efficente e quinid vogliamo conoscere l'hw dove stiamo per ppoi ottimizzare per quello. 

### Architettura di Von Neumann

![[Pasted image 20251007174152.png|400]]
classica architettura...
• Main memory: Collection of locations. Each has an address (used to access that location) and some content (data or instruction). 
• CPU/Processor/Core: Control unit (decides which instructions execute) and datapath (executes the instructions) – The state of an executing program is stored in registers (very fast storage) – An important register in the control unit is the program counter (PC), storing the address of the next instruction to execute 
• Interconnect: used to transfer data between CPU and memory. Traditionally a bus, but can be much more complex (we will get back to it)

> [!bug] The interconnect determines the rate at which data is transferred (Von Neumann Bottleneck)

![[Pasted image 20251007174415.png]]

## MPI

Riferito spesso come Single-Program Multiple-Data (SPMD) (Un programma compialto che viene eseguito da tutti i processi)

![[Pasted image 20251007175022.png]]

Se ho dei condizionali, splitto in più processi (se sono il processo x faro y se sono z faro k)
Ricordiamo che non c'è memoria condivisa quindi la coordinazione avviene per message passing

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

- MPI_Init: dice a mpi di setuppare il tutto

![[Pasted image 20251007175959.png]]

- MPI_Finalize: dice che abbiamo finito ce di ripulire il tutto
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


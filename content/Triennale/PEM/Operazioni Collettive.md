---
date:
  - 2026-01-15
tags:
  - Operazioni_collettive
  - Reduce
---

> [!hint] Si può pensare di suddividere il carico di lavoro ad albero

### Reduce

A tal proposito, MPI fornisce una funzionalità che permette di eseguire operazioni di aggregazione di risultati senza preoccuparci della logica di comunicazione per il trasferimento dei dati parziali. 

La funzione in questione è `int MPI_Reduce` , con i seguenti parametri
- `void* input_data_o` è il puntatore alla variabile in ingresso (somma parziale)
- `void* output_data_o` è il puntatore al valore che sarà riempito con il valore totale aggregato
- `int count` è il numero di elementi da aggregare
- `MPI_Datatype datatype` è il tipo dei valori in questione
- `MPI_Op operator` è l’operazione di aggregazione (somma, moltiplicazione, XOR, etc...)
- `int dest_process` è il rank del processo che riceverà il risultato
- `MPI_Comm comm` il comunicatore in questione

Le operazioni di aggregazione supportate da MPI sono le seguenti:
1. Massimo / Minimo
2. Somma / Prodotto
3. AND logico / bit a bit
4. OR logico / bit a bit
5. XOR logico / bit a bit
6. Massimo / Minimo insieme al suo indice (?)

---

### Bcast

È chiaro che sul processo di rank 0 (poiché e l'unico che può interagire con stdin) ci sia riportato un carico di lavoro maggiore, è quindi possibile suddividere il carico facendo si che il processo 0 condivida i dati con due altri processi, e questi due li condividano a loro volta con altri due processi ciascuno, creando un albero di condivisione.

MPI fornisce una funzione, `MPI_Bcast` che si occupa di eseguire il broadcast da un processo verso tutti gli altri. I parametri sono i seguenti :
- `void* data_p` è il puntatore alla variabile da condividere
- `int count` è il numero di elementi da condividere
- `MPI_Datatype datatype` è il tipo dei valori in questione
- `int source_process` è il rank del processo che condivide il valore
- `MPI_Comm comm` il comunicatore in questione

Il parametro `void* data_p` fungerà sia da input, che da output, nel caso il processo chiamante sia colui che condivide il valore, in `data_p` sarà presente il valore condiviso, altrimenti, in `data_p` sarà presente il valore ricevuto.

---

### Allreduce

A questo punto, si supponga di voler fare un operazione di aggregato, per poi avere il risultato condiviso fra tutti i processi, concettualmente, ciò equivale ad eseguire una `MPI_Reduce` seguita da una `MPI_Bcast` .

MPI fornisce una funzione a tal proposito, ottimizzata a dovere, ossia `MPI_Allreduce`. I parametri sono identici alla `MPI_Reduce` , eccetto per l’assenza del processo di destinazione, dato che in questo caso, ogni processo avrà il risultato.
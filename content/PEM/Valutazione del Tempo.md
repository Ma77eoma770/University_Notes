---
date:
  - 2026-01-15
tags:
  - Valutazione_temporale
---
Valutare il tempo di esecuzione di un programma multicore non è banale. MPI fornisce una funzione double `MPI_Wtime` , ritorna un valore che rappresenta il tempo passato da un certo riferimento fisso. Basta valutare questo tempo in due punti diversi del codice e farne la differenza.

```C
double start, finish;
start=MPI_Wtime();
/*codice*/
finish=MPI_Wtime();
printf("%lf",finish-start)
```

Ogni processo, seguirà un evoluzione dello stesso codice differente, e non contemporanea fra gli altri. Se verrà calcolato il tempo trascorso per l’esecuzione di una sezione di codice, ogni processo restituirà un tempo diverso. Il tempo totale del programma, sarà dato dal massimo dei tempi forniti da ogni processo. Si può usare l’operazione collettiva:

```C
double local_start, local_finish, local_elapsed, elapsed;
local_start=MPI_Wtime();
/*codice*/
local_finish=MPI_Wtime();
local_elapsed=local_finish-local_start;
MPI_Reduce(&local_elapsed,&elapsed,1,MPI_DOUBLE,MPI_MAX,0,comm);
printf("%d",elapsed);
```

Ovviamente, tale metodo è valido con l’assunzione che tutti i processi inizino nello stesso momento.

Quando si esegue un’operazione di reduce, tutti i processi devono arrivare ad uno punto comune nel codice, "sincronizzandosi", esiste un’operazione, `MPI_Barrier` , il cui unico scopo è attendere che tutti i processi la eseguano prima di continuare nell’esecuzione. Tale operazione comunque, non garantisce che i processi si sincronizzino una volta eseguita, quindi approssima il comportamento sincronizzato. Nella pratica, è molto complesso garantire tale proprietà.

---

Ovviamente, con l’aumento delle dimensioni dell’input, aumenta anche il tempo di esecuzione, e quest’ultimo decresce con l’aumentare del numero di processi. Questo fattore di proporzionalità inversa non è però valido per un arbitrario numero di processi, de facto, ci sarà un limite per cui vale, ad un certo punto, l’aumentare dei processi non migliorerà il tempo di esecuzione. Per la stima dei tempi si definiscono le seguenti variabili:
- $T_s(n)$ è il tempo di **esecuzione** di un programma se eseguito in maniera sequenziale, con un input di dimensione n.
- $T_p(n,p)$ è il tempo di **esecuzione** di un programma se eseguito in maniera **parallela** con p processi, e con un input di dimensione n.
- $S(n,p) = \frac{T_s(n)}{T_p(n,p)}$ è detto **speed up** dell’applicazione e misura il margine di miglioramento di un programma quando si esegue in parallelo piuttosto che in sequenziale. All'aumentare dei processi diminuisce lo speed up mentre all'aumentare dell'input aumenta lo speed up.
- $Sc(n,p) = \frac{T_p(n,1)}{T_p(n,p)}$ è la **scalabilità**.
- $E(n,p)= \frac{S(n,p)}{p} \in (0,1]$ è l'**efficienza**.

---

Un programma si dice **strongly scalable** (fortemente scalabile) se, data una dimensione fissa dell’input n, lo speed up ha una buona crescita all’aumentare dei processi. È invece **weakly scalable** (debolmente scalabile) se, l’aumentare dell’input, e l’aumentare del numero dei processi, il tempo di esecuzione varia di poco.

la **legge di Amdahl** stabilisce che lo scaling di un’applicazione è limitato dalla frazione seriale. Per un n dimensione in input fissata, si ha:
$$T_p(p)=(1-\alpha)T_s + \alpha\frac{T_s}{p}$$

Dove $\alpha \in [0,1]$ rappresenta la frazione parallelizzabile, e $1-\alpha$ la frazione seriale. Lo speed up sarà quindi:
$$S(p)=T_s\frac{1}{(1-\alpha)T_s + \alpha \frac{T_s}{p}}$$

che mandando i processi all'infinito si riduce in $\frac{1}{1-\alpha}$ .
Questa legge non tiene conto del weak scaling e decreta un comportamento ideale/approssimato. La **legge di Gustafson** tiene conto del weak scaling:
$$S(n,p)=(1-\alpha)+\alpha p$$


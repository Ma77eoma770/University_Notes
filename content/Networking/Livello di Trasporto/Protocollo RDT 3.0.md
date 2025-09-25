---
date:
  - 2025-04-13
tags:
  - RDT
  - Protocollo
  - Livello_Trasporto
---
Oltre all’assunzione di possibili bit invertiti, il protocollo RDT 3.0 assume la possibilità di una perdita di pacchetti, sia dati che ACK. Per risolvere tale problematica, il mittente attende un lasso di tempo:
- Il destinatario deve specificare il numero di sequenza del pacchetto per il quale sta inviando un ACK
- Se non viene ricevuto alcun ACK allo scadere del lasso di tempo, il pacchetto dati (che indicheremo con pkt) viene ritrasmesso
- Se pkt o ACK arrivano successivamente allo scadere del tempo, il pacchetto verrà
  ritrasmesso, implicando che la trasmissione verrà duplicata (problema già gestito
  dai numeri di sequenza)
La FSM associata al mittente corrisponde a:

![[RDT_3.0_FSM.png]]

> [!warning] Attenzione:
> Tuttavia, in cambio dei notevoli benefici del meccanismo stop and wait, le prestazioni del protocollo RDT 3.0 risultano essere **infime**, limitando le prestazioni dell’infrastruttura sottostante, ossia il canale.

In particolare, la percentuale di utilizzo Umit della comunicazione da parte del mittente,
ossia la frazione di tempo in cui il mittente è impegnato nell’invio corrisponde a:

$U_{mit} = \frac{D_t}{RTT + D_t}$

dove $D_t$ è il delay di trasmissione (dunque $D_t = \frac{L}{R}$ con L la lunghezza del pacchetto e R il transmission rate del link).

![[Trasmission_delay_RDT_3.0.png]]

> [!example] Esempio
> Considerando un link avente un rate pari a R = 1 Gb/s, una lunghezza di pacchetto pari a L = 8000 b e un ritardo di propagazione sia pari a 15 ms, la percentuale di utilizzo del mittente corrisponde a:
> $D_t = \frac{8 \cdot 10^3 b}{10^9 b/s} = 8 \mu s$
> $U_{mit} = \frac{8 \mu s}{30 ms + 8 \mu s} = 27 \cdot 10^{-5} = 0.027 \%$

^ac5bbc


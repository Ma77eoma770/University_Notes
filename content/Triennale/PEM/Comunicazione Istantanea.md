---
date:
  - 2026-01-15
tags:
  - Comunicazione
---
MPI utilizza , se non specificato diversamente, una metodologia di comunicazione standard, se il messaggio da trasferire è piccolo, è probabile che venga immediatamente trasferito venendo salvato su un buffer del destinatario. Diversamente, nel caso di un messaggio grande, la chiamata sarà bloccante in quanto MPI deve assicurarsi che il destinatario abbia allocato la memoria sufficiente per riceverlo.

> [!hint] NB
> In entrambi i casi, MPI si assicura che il messaggio da inviare non vada perso, il programma riottiene il controllo solo quando il buffer utilizzato per contenere il messaggio è di nuovo disponibile, si dice che la `MPI_Send` è **locally blocking**. 

Oltre la comunicazione standard, vi sono altri modi di inviare messaggi:
- **Buffered** : Tramite la chiamata `MPI_Bsend` , l’operazione è sempre locally blocking, ma l’utente deve fornire manualmente un buffer in cui salvare il messaggio da inviare.
- **Sincrona** : Tramite la chiamata `MPI_Ssend` , l’operazione è globalmente bloccante, il controllo viene restituito esclusivamente quando il destinatario ha ricevuto il messaggio chiamando `MPI_Recv` . Risulta utile per far si che un processo attenda che un altro arrivi ad un certo punto della computazione.
- **Ready** : Tramite la chiamata `MPI_Rsend` , se il destinatario non ha già effettuato una `MPI_Recv` , tale chiamata fallisce, è quindi necessario che esso sia già in attesa di ricevere.

Le chiamate `MPI_Recv` e `MPI_Send` sono considerate **poco performanti** in quanto il processo chiamante potrebbe bloccare la sua esecuzione, in alcuni casi può essere utile una chiamata non bloccante per la trasmissione dei dati, soprattutto quando il mancato ricevimento di essi non causa errori nell’esecuzione del programma. Le funzioni non bloccanti messe a disposizione da MPI sono dette **funzioni immediate**, e permettono l’overlap fra computazione e comunicazione. 

- La chiamata `MPI_Isend` ha gli stessi parametri della funzione non immediata, eccetto un parametro aggiuntivo, `MPI_Request *req` , necessario per avere informazioni sullo status della chiamata.
- La chiamata `MPI_Irecv` ha gli stessi parametri della funzione non immediata, eccetto per l’assenza del parametro sullo status originario, e l’aggiunta del parametro `MPI_Request *req`, necessario per avere informazioni sullo status della chiamata.

- La funzione `int MPI_Wait(MPI_Request *request, MPI_Status *status)` fa si che il processo si blocchi finché un invio o una ricezione non è andato a buon termine. È una chiamata bloccante.
- La funzione `int MPI_Test(MPI_Request *request, int *flag, MPI_Status *status)` controlla se una chiamata di invio o ricezione è andata o no a buon fine, salvando l’esito del risultato nel campo flag .
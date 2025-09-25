---
date:
  - 2025-04-13
tags:
  - Protocollo
  - TCP
  - Livello_Trasporto
---

## Definizione
![[Principi delle applicazioni di rete#^5a238a]]

Il protocollo TCP è un protocollo end-to-end, ossia con un solo mittente ed un solo destinatario, offerente un byte stream **affidabile e in ordine**, dove i messaggi del livello di applicazione vengono concatenati in un unico stream, a differenza dell’UDP, dove ogni messaggio è un segmento diverso.
Come già discusso, il protocollo TCP è **orientato alla connessione**, dove l’handshaking
inizializza lo stato del mittente e del destinatario prima dello scambio dei dati.
Il flusso dati, inoltre è full duplex, ossia bidirezionale all’interno della stessa connessione
(dati full duplex), limitati tuttavia da un **Maximum Segment Size** (**MSS**). Tuttavia,
è necessario sottolineare che si tratta di un paradigma diverso dalla [[Pacchetti, Forwarding e Routing#^b98bc4|commutazione di circuito]], poiché la rete non è a conoscenza dello stabilimento della connessione.

![[TCP_header.png]]

Per gestire il trasporto affidabile, il protocollo TCP utilizza:
1. **ACK cumulativi e pipelining**: dove il window size dipende dal **flow control**, ossia una garanzia sul non sovraccarico del destinatario da parte del mittente, e dal **congestion control**, ossia una garanzia sul non sovraccarico della rete da parte del mittente
2. Il **numero di sequenza** dei segmenti del protocollo TCP corrisponde al numero di sequenza del **primo byte del settore data** del segmento stesso. Per quanto riguarda l’**ACK**, viene utilizzato il numero di sequenza del **byte successivo aspettato**.
3. Nel momento in cui il mittente riceve dati dal livello di applicazione, viene creato il segmento e il suo numero di sequenza, avviando il timer a meno che esso non sia già in esecuzione. Inoltre, il **timer** utilizzato è singolo e collegato al **segmento non confermato pià vecchio**. Allo scadere del *TimeoutInterval*, viene ritrasmesso il segmento che ha causato il timeout, riavviando il timer.
4. Alla **ricezione di un ACK,** invece, se quest’ultimo copre segmenti precedentemente non confermati, vengono aggiornate le informazioni di tali pacchetti, avviando il timer se vi sono ancora segmenti non confermati, il quale sarà collegato al nuovo segmento più vecchio ([[Go-back-N e Selective repeat|ibrido tra Go-back-N e Selective Repeat]]).
   
![[TCP_window.png]]

Per quanto riguarda il destinatario, invece, si hanno quattro scenari:
1. All’arrivo di un **segmento in ordine**, con **numero di sequenza atteso** e tutti i segmenti **precedenti già confermati**, viene inviato un **delayed ACK**: dopo aver atteso 500 ms per il prossimo segmento, se quest’ultimo non è stato ricevuto viene inviato l’ACK
2. All’arrivo di un **segmento in ordine**, con numero di sequenza atteso ma con un segmento precedente **non ancora confermato**, viene immediatamente inviato un **ACK cumulativo confermante entrambe i segmenti**.
3. All’arrivo di un segmento **fuori ordine e con numero di sequenza maggiore di quello atteso (dunque vi è un gap)**, viene inviato immediatamente un **ACK duplicato**.
4. All’arrivo di un segmento **che in parte o totalmente riempie in ordine un gap**, viene immediatamente **inviato l’ACK**.
   
![[TCP_visualized_ACK.png]]
---
date:
  - 2025-04-22
tags:
  - Congestione
  - FlowControll
  - Livello_Trasporto
---
Per tentare di gestire la congestione, vengono principalmente utilizzati due approcci:
- Controllo della congestione end-to-end, dove non viene ricevuto alcun feedback esplicito dalla rete e la congestione viene dedotta dalle perdite e ritardi osservati dal mittente e il destinatario. 
  Tale approccio è adottato dal protocollo TCP
- Controllo della congestione assistito dalla rete, dove i router forniscono un feedback diretto agli host di invio/ricezione con flussi che passano attraverso router congestionati, indicando, in alcuni casi, direttamente il livello di congestione o la velocità di invio impostata


> [!info] Definizione: Algoritmo AIMD
> L’algoritmo AIMD (Additive Increase, Multiplicative Decrease) è un algoritmo utilizzato da alcune versioni del protocollo TCP per prevenire la congestione, dove i mittenti possono aumentare la velocità di invio fino a quando si verifica una perdita di pacchetti, per poi diminuirla:
> - **Additive Increase**: il rate viene aumentato di 1 MSS (Maximum Segment Size) ad ogni RTT fino a quando una perdita non viene osservata.
> - **Multiplicative Decrease**: ad ogni perdita osservata, il rate di invio viene dimezzato

![[Algoritmo_AIMD.png|700]]


> [!info] Definizione: Congestion avoidance e Congestion window
> Approssimativamente, il protocollo TCP utilizza la seguente procedura di prevenzione della congestione (congestion avoidance):
> - Viene utilizzato un valore cwnd (congestion window), corrispondente alla quantità di byte inviata ad ogni RTT, da cui ne segue che:
>   $\text{Rate di invio} \approx \frac{\text{cwnd}}{RTT} B / s$
> - Il mittente limita la trasmissione a $LastByteSent − LastByteAcked ≤ cwnd$
> - Il valore $cwnd$ varia dinamicamente reagendo alla congestione osservata. In particolare, utilizzando l’ **Additive Increase** ad ogni ACK ricevuto si ha che: 
>   $\text{cwnd} \approx \text{prev\_cwnd} + ( MSS \cdot \frac{MSS}{\text{prev\_cwnd}})$

![[Congestion_avoidance.png|700]]


> [!info] Definizione: Fast Recovery
> Il fast recovery è una procedura utilizzata da alcune versioni del protocollo TCP per prevenire la congestione, dove ad ogni perdita rilevata a seguito di un triplo ACK duplicato viene dimezzato il rate di invio (ottenuto circa dimezzando il valore cwnd)

> [!info] Definizione: Slow Start
> Lo slow start è una procedura utilizzata da alcune versioni del protocollo TCP prevenire la congestione, dove:
> - Il valore cwnd viene impostato ad 1 MSS all’inizio della connessione o a seguito di un timeout
> - Successivamente, il valore di cwnd viene raddoppiato ad ogni RTT, fino al rilevamento della prima perdita di pacchetto (incremento esponenziale)

![[Slow_Start.png|400]]

> [!warning] Nota:
> L’immagine superiore è un’approssimazione del vero comportamento, poichéraddoppiare cwnd non implica che venga raddoppiata la quantità di segmenti inviati, bensì che raddoppi la quantità di segmenti di dimensione massima inviati

**Versioni del protocollo TCP:**
1. **TCP Tahoe**: composto da un’algoritmo di congestion avoidance (tramite l’Ad- ditive Increase), l’algoritmo slow start e il fast retransmit.
2. **TCP Reno**: analogo al TCP Tahoe, ma con l’utilizzo aggiuntivo del fast recovery (FSM riportata in seguito)

![[TCP_Reno.png]]

In particolare, per effettuare il passaggio tra slow start e congestion avoidance, viene utilizzato un valore ssthresh (slow start threshold).
1. A seguito del rilevamento di una perdita di pacchetto, il valore ssthresh viene settato (con il valore di cwnd precedente alla perdita). Se al prossimo RTT impostato a $cwnd / 2$ con il valore di $cwnd \geq ssthresh$, viene posto $cwnd = ssthresh$ e si passa dallo slow start al congestion avoidance
2. Nel caso particolare del TCP Reno, se si verifica un triplo ACK duplicato, il valore $cwnd$ viene dimezzato dal fast recovery, dunque si ha direttamente $cwnd = ssthresh$
   
![[Grafico_TCP_Reno.png|700]]


> [!info] Definione: TCP CUBIC
> Il TCP CUBIC è una versione di TCP utilizzante l’algoritmo CUBIC per prevenire la congestione, il quale è definito dalla seguente logica:
> - Viene utilizzato un valore W , corrispondente alla velocità di invio (o alla quantità di dati inviati dal mittente, ossia cwnd). Ad ogni perdita rilevata, viene dimezzato tale valore.
> - Il limite superiore Wmax corrisponde alla velocità di invio nel momento in cui è stata rilevata una perdita di pacchetto (viene assunto che a seguito della perdita lo stato di congestione della rete non sia variato di molto).
> - Viene utilizzato un valore K corrispondente al momento di tempo stimato in cui il valore W raggiungerà il limite superiore Wmax (la modalità di stima viene definita dallo standard RFC 8312).
> - Il valore W viene incrementato in funzione del cubo della distanza tra il tempo corrente e K. Di conseguenza, il valore W crescerà molto rapidamente quando il tempo corrente è lontano dal valore K, mentre crescerà lentamente al suo avvicinarsi.

Supponendo, ad esempio, che il valore di Wmax rimanga sempre lo stesso nel tempo, il throughput del TCP CUBIC rispetto a quello standard risulta più elevato:

![[TCP_CUBIC.png]]

In particolare, essendo predefinito sul sistema operativo Linux, il TCP CUBIC è la
versione del protocollo TCP più diffusa nei web server.
Una volta aver visto algoritmi e tecniche per prevenire la congestione, possiamo concentrarci sul link di uscita del stesso in cui si verifica la perdita (bottleneck link)

![[Focus1.png]]

In presenza di un bottleneck link, aumentando il rate di invio non aumenterà il throughput per via del bottleneck link ma aumenterà il RTT misurato.

![[Focus2.png]]

Una soluzione ottimale, dunque, è quella di mantenere il percorso end-to-end quasi pieno,
ma senza superare la soglia stabilita, utilizzando un approccio delay-based:
- Il valore $RTT_{min}$ è il RTT minimo osservato dal mittente, corrispondente quindi al RTT osservato quando il percorso non è congestionato
- Approssimativamente, il throughput misurato ad ogni RTT corrisponde a:
  $Throughput_{misurato} = \frac{Byte_{RTT}}{RTT_{misurato}}$
  dove {Byte_RTT} è il numero di byte inviati nell’ultimo RTT, mentre il throughput non congestionato è pari a:
  $Throughput_{non cong} = \frac{cwnd}{RTT_{min}}$
- Se il valore del throughput misurato è molto vicino a quello non congestionato, il valore di cwnd viene incrementato in modo lineare (dunque +1 MSS ad ogni RTT), mentre se è molto inferiore viene decrementato in modo lineare

Tramite tale approccio, alcune versioni di TCP (es: protocollo BBR) riescono ad indurre
un controllo della congestione senza forzare delle perdite di pacchetto, massimizzando il
throughput ma mantenendo basso il ritardo.
Altre versioni di TCP (es: TCP ECN), invece, implementano anche la seconda modalità
di controllo della congestione, ossia il controllo della congestione assistito dalla rete,
dove:
- Due bit dell’header del livello di rete vengono contrassegnati dal router per indicare lo stato della congestione.
- Una volta raggiunto il destinatario, quest’ultimo imposterà il bit ECE sul segmento ACK per notificare al mittente lo stato della congestione.

![[TCP_ECN.png]]


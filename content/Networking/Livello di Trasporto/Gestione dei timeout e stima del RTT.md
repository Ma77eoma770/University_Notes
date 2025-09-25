---
date:
  - 2025-04-13
tags:
  - RTT
  - Delay
  - Livello_Trasporto
---
Il valore di **timeout** impostato deve essere p**iù lungo di un RTT**. Tuttavia, poiché il
RTT è variabile, è necessario **stimarlo**.
Se il timeout scelto è troppo corto, si verificheranno troppi timeout prematuri, creando
una serie di ritrasmissioni non necessarie. Se invece è troppo lungo, vi è una reazione
troppo lenta a seguito della perdita di un pacchetto.

Per stimare il RTT, viene campionato un valore *SampleRTT*, ossia il tempo misurato
dalla trasmissione del segmento fino alla ricezione dell’ACK (ignorando le ritrasmissioni).
Poiché *SampleRTT* varia, viene utilizzata una media delle misurazioni recenti e non
solo dell’ultimo *SampleRTT* (EWMA - Exponential Weighted Moving Average):

> [!tip] .
> $EstimatedRTT = (1 − \alpha) \cdot PreviousEstimatedRTT + \alpha · SampleRTT$
> 

dove tipicamente si ha $\alpha$ = 0.125 e dove l’influenza del campione passato diminuisce in modo esponenziale

![[RTT_ext_graph.png]]

Il valore del *TimeoutInterval*, dunque, corrisponderà al valore attuale dell’*EstimatedRTT*
sommato ad un **margine di sicurezza**:

$TimeoutInterval = EstimatedRTT + 4 · DevRTT$

dove *DevRTT* è l’EWMA della deviazione di *SampleRTT* da *EstimatedRTT*

$DevRTT = (1 − \beta) · PreviousDevRTT + \beta |SampleRTT − EstimatedRTT|$

con un valore tipico β = 0.25

Un’ottimizzazione ulteriore del protocollo TCP prevede l’implementazione del **fast 
retransmit**: se il mittente riceve 3 ACK aggiuntivi per gli stessi dati (dunque tre ACK duplicati), viene nuovamente inviato il segmento non confermato con numero di sequenza più piccolo poiché probabilmente tale segmento è andato perso, dunque non è necessario
aspettare il timeout

![[TCP_fast_retr.png]]
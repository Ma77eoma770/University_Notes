---
date:
  - 2025-04-06
tags:
  - UDP
  - Protocollo
  - Livello_Trasporto
---
Come già accennato, il [[Principi delle applicazioni di rete#^ebfa99|protocollo UDP]] è un protocollo di trasporto "senza fronzoli" (bare bone) e senza connessione. Pertanto, non avviene alcun handshake tra mittente e destinatario, implicando che ogni segmento UDP venga gestito indipendentemente dagli altri. Inoltre, il protocollo UDP svolge un servizio best-effort, dunque i segmenti UDP possono essere persi o consegnati in modo non ordinato. Tuttavia, tali caratteristiche rendono UDP vantaggioso in alcune casistiche: 
- Poiché non vi è alcuna connessione, il protocollo risulta **semplice**, oltre all’assenza del ritardo RTT necessario per l’handshake richiesto 
- La **dimensione dell’header è minima**, rendendo il pacchetto più leggero 
- L’assenza di controllo della congestione permette al protocollo UDP di tentare la trasmissione senza alcun limite di velocità e il funzionamento anche in casi di congestione dovuti ad un carico elevato sui nodi della rete 
- Se si vuole rendere il trasferimento affidabile anche utilizzando UDP, basta implementare l’affidabilità necessaria al livello di applicazione (es: HTTP/3 tramite il protocollo QUIC), piuttosto che al livello di trasporto 

In particolare, l’header utilizzato dal protocollo UDP, oltre a contenere le porte di origine e di destinazione, contiene solamente due campi aggiuntivi: 
- Un campo **length** (16 bit), indicante la lunghezza del contenuto
- Un campo **checksum** (16 bit), utilizzato per rilevare errori nel segmento trasmesso

![[Header_UDP.PNG|400]]

Il valore di checksum viene calcolato tramite una **somma in complemento ad uno con wrap-around**: 
1. Il mittente considera il contenuto del segmento (compresi gli altri campi dell’header e gli indirizzi IP) come una sequenza di numeri interi a 16 bit 
2. Il mittente calcola il checksum sommando in complemento ad 1 (ossia sommando e poi invertendo tutti i bit) i numeri interi della sequenza. In particolare, se è presente un riporto finale generato dalla somma del bit più significativo, viene sommato anche tale riporto (wrap-around) 
3. Il valore del checksum viene inserito nel campo dell’header e il pacchetto continua il processo di trasmissione 
4. Una volta giunto a destinazione, l’host ricevente calcola nuovamente il checksum, verificando che sia uguale a quello inserito nell’header del segmento. 
   Se il checksum è differente, viene rilevato un errore nella trasmissione 

Tuttavia, è necessario notare che se il checksum è uguale non è detto che la trasmissione non abbia generato alcun errore: 
- Consideriamo il calcolo del checksum tra i seguenti numeri interi a 16 bit:
  
  ![[Checksum_ex_1.PNG]]
  
- Poiché è presente un riporto, viene effettuato il wrap-around sommando tale riporto ai restanti 16 bit:
  
  ![[Checksum_ex_2.PNG]]
  
- Infine, vengono invertiti i bit del risultato per ottenere la somma in complemento ad 1:
  
  ![[Checksum_ex_4.PNG]]

- Tuttavia, nel caso in cui i due bit meno significativi di entrambi i numeri fossero stati invertiti (per qualche motivo sconosciuto) durante la trasmissione, il **checksum calcolato sarebbe identico**:
  
  ![[Checksum_ex_3.PNG]]
  
  
  
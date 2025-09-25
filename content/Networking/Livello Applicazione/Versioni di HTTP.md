---
date:
  - 2025-03-30
tags:
  - HTTP
  - Livello_applicazione
  - Versioni
  - Protocollo
---
## Introduzione

Come già discusso, il **protocollo HTTP/1.1** ha introdotto la possibilità di poter effettuare più richieste GET in successione tramite una singola connessione TCP. Tuttavia, tale modifica ha introdotto ulteriori problematiche:
- Il server risponde alle richieste GET nell’ordine in cui vengono effettuate (**First Come First Served (FCFS)**). 
- Un oggetto di piccole dimensioni potrebbe dover attendere la trasmissione di oggetti di grandi dimensioni richiesti prima di esso (blocco [[Head of line (HOL)|head-of-line (HOL)]]).
- La perdita di un segmento TCP causa lo stallo del trasferimento di un oggetto

![[Http_stallo_HOL.PNG]]

Per risolvere tali problematiche, **il protocollo HTTP/2** introduce una maggiore flessibilità al server nell’invio di oggetti al client: 
- L’ordine di trasmissione degli oggetti richiesti viene stabilito in base alla priorità dell’oggetto specificata dal client.
- Gli oggetti vengono divisi in frame, schedulati in modo da mitigare il blocco HOL.
- Possono essere inviati più oggetti contemporaneamente (multiplexing).

![[Http2_mux.PNG]]

Il protocollo **HTTP/3**, invece, risolve le ultime problematiche rimanenti all’interno del protocollo HTTP/2, tramite l’aggiunta di controlli sulla sicurezza, sugli errori e sulla congestione per oggetto, utilizzando il [[Protocollo QUIC|protocollo QUIC]] (basato su UDP) al posto del protocollo TCP.
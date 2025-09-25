---
date:
  - 2025-04-21
tags:
  - TCP
  - Livello_Trasporto
  - Handshake
---
Prima di effettuare lo scambio di dati, il mittente e il destinatario effettuano un handshake, dove viene determinata la disponibilità dell’uno e dell’altro ad accettare di stabilire una connessione, concordando i parametri di quest’ultima (es: l’inizio del numero di sequenza).

![[Gestione_connessione_handshaking.png]]
Una prima implementazione **dell’handshake è l’handshake a 2 vie** (two-way handshake), dove il mittente invia la richiesta di connessione al destinatario, il quale invia successivamente l’accettazione di tale richiesta, assumendo lo stato di connessione ESTAB (established).
Una volta ricevuta l’accettazione, anche il mittente assumerà lo stato ESTAB, per poi
procedere con l’invio effettivo dei dati.

![[Estab_TCP.png]]

Tuttavia, in tale implementazione il destinatario non è a conoscenza della ricezione da parte del mittente del pacchetto di accettazione della connessione, presentando quindi d**ue problematiche fondamentali**:
1. Nel caso in cui il mittente rinvii la richiesta di connessione allo scadere del timer TCP e l’accettazione del destinatario inerente alla prima richiesta giunge comunque dopo lo scadere del timer, il mittente suppone che la connessione sia andata a buon fine (nonostante il RTT sia estremamente basso), stabilendo quindi una prima connessione.
2. Se tale connessione viene terminata prima che la seconda richiesta del mittente sia giunta al destinatario, quest’ultimo interpreterà la richiesta come una richiesta appartenente ad una seconda connessione.
3. Tuttavia, poiché tale richiesta era solo un rinvio della prima richiesta connessione, il client ignorerà la seconda richiesta di accettazione del server, creando quindi una connessione fantasma senza client
   
![[Probelam_two_handshake.png]]

4. Inoltre, nel caso in cui venga stabilita una connessione fantasma, si potrebbe andare incontro ad accettazioni di pacchetti dati duplicati.
   
![[2problema_two_handshake.png]]

Di conseguenza, l’handshake TCP viene implementato attraverso uno scambio di 3 mes-
saggi (handshake a 3 vie):
1. Il mittente sceglie un numero di sequenza iniziale x e invia un pacchetto di tipo SYN (synchronize) al destinatario, richiedendo di stabilire una connessione.
   Per inviare un pacchetto di tipo SYN, è sufficiente impostare il campo SYN = 1 all’interno dell’header
2. Una volta ricevuto il pacchetto SYN, il destinatario sceglie un numero di sequenza iniziale y e invia un pacchetto di tipo SYN/ACK (synchronize and ACK) al mittente, impostando i campi SYN = 1 e ACK = 1 nell’header
3. Una volta ricevuto il pacchetto SYN/ACK, il mittente invia un pacchetto di tipo ACK (dunque con solo ACK = 1), passando in stato ESTAB. Infine, una volta ricevuto il pacchetto ACK, il destinatario passerà in stato ESTAB
   
   ![[3_way_handshake.png]]

In questo modo, il destinatario sarà a conoscenza dello stato finale del mittente, risolvendo
le due problematiche.
Per effettuare la chiusura di una connessione, il primo dispositivo (mittente o destinatario che sia) invia al secondo dispositivo un pacchetto di tipo FIN (finished). Una volta ricevuto il pacchetto FIN, il secondo dispositivo risponderà con un pacchetto FIN/ACK, per poi inviare, dopo un breve lasso di tempo, un secondo pacchetto FIN. Analogamente, anche il primo dispositivo una volta ricevuto il pacchetto FIN invierà un pacchetto FIN/ACK.
Utilizzando tale handshake a 4 vie, entrambi i dispositivi riescono accertarsi il corretto
termine della connessione. Inoltre, in tal modo entrambi i dispositivi possono terminare
la connessione simultaneamente (creando una sorta di doppio handshake a 2 vie)
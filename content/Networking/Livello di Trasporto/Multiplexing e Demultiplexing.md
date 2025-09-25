---
date:
  - 2025-04-06
tags:
  - Multiplexing_and_Demultiplexing
  - Livello_Trasporto
  - Socket
---

Per implementare le funzionalità di multiplexing e demultiplexing al livello di trasporto, ogni host utilizza indirizzi IP e numeri di porta per indirizzare correttamente un segmento al socket appropriato del destinatario:
1. L’header di ogni segmento possiede un numero di porta per l’origine e la destinazione 
2. Ogni datagramma del livello di rete trasporta un segmento del livello di trasporto 
3. L’header di ogni datagramma possiede l’IP dell’origine e l’IP della destinazione
   
Nel caso di un demultiplexing senza connessione (es: protocollo UDP), durante la creazione di un socket all’interno di un processo è necessario specificare la porta locale dell’host con cui identificare tale socket.

(es: DatagramSocket d_soc = new DatagramSocket(12534);)

Successivamente, qualsiasi dispositivo che voglia comunicare con tale host invierà un datagramma al cui interno sia specificata la coppia indirizzo IP di destinazione e la porta di destinazione. Una volta giunto alla destinazione, verrà letto il numero di porta di destinazione presente nell’header del segmento contenuto all’interno del datagramma ricevuto, indirizzando il segmento al socket con tale numero di porta. In particolare, è necessario sottolineare che, in tal modo, il socket su tale host per la comunicazione con più mittenti sia unico. Di conseguenza, qualsiasi datagramma inviato su tale porta apparterrà allo stesso stream dati. Tuttavia, essi saranno comunque distinti univocamente dal numero di porta e l’indirizzo IP del mittente presenti nel datagramma.

![[Livello_trasporto_ex.PNG|700]]

Nel caso del demultiplexing con connessione (es: protocollo TCP), invece, ogni socket viene identificato univocamente come una quadrupla 

(IP_Orig., Porta_Orig., IP_Dest., Porta_Dest.) 

Una volta ricevuto il datagramma, vengono utilizzati tutti e quattro i valori per indirizzare il segmento al socket appropriato. In tal modo, ogni connessione è identificata in modo univoco da una coppia di socket (una sul primo host ed una sul secondo host), permettendo di implementare le garanzie previste dai protocolli. 

Inoltre, per via dell’identificazione univoca dei socket, un host può avere più socket legati alla stessa porta con una comunicazione diversa: se due host A e C avviano una connessione avente come destinazione la porta 80 dell’host B, su quest’ultimo verranno creati due socket diversi.

![[tcp_transport.PNG]]


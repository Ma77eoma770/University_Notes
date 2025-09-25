---
date:
  - 2025-03-30
tags:
  - UDP
  - TCP
  - Paradigmi_comunicazione
  - Internet
  - Livello_applicazione
---

## Definizioni iniziali

> [!info] Definizione: Paradigma di comunicazione
> Un **paradigma di comunicazione** è una metodologia di scambio informazioni e gestione delle connessioni all’interno di una rete, principalmente all’interno di Internet.

I due principali paradigmi di comunicazione sono:
1. **Paradigma Client-Server**:  I sistemi terminali vengono divisi in due categorie (i protocolli HTTP, FTP e IMAP sono basati su questo paradigma): ^79bb29
   
	- **Client**: il quale comunica solo ed esclusivamente con un server, richiedendo dei servizi a quest’ultimo, e può rimanere anche inattivo se non necessario, implicando che esso possa avere indirizzi IP dinamici nel tempo. In particolare, per tali caratteristiche, non vi è una comunicazione client-client, ma solo una comunicazione client-server-client.
	  
	- **Server**: il quale possiede un indirizzo IP permanente, rimanendo sempre attivo in attesa di **fornire servizi** ai vari client richiedenti.

2. **Paradigma Peer-to-Peer (P2P)**: dove i sistemi terminali vengono detti peer (tradotto: pari, di egual importanza) ed ognuno di essi è in grado di comunicare direttamente con ogni altro peer (assumendo quindi entrambi i ruoli di client e server). 
   
   In particolare, ogni peer richiede e fornisce servizi ad altri peer, rendendo il sistema estremamente scalabile: ogni nuovo peer incrementa le capacità di servizio e le richieste di servizio. 
   
   Inoltre, come nel caso dei client, ogni peer può connettersi alla rete in modo intermittente utilizzando IP dinamici, diminuendo temporaneamente la quantità di servizi fornibili nella rete. Di conseguenza, la loro gestione risulta estremamente più complessa, ma anche più performante nel caso di un numero elevato di peer. 
   
   Un classico esempio di utilizzo del paradigma P2P risultano essere i vari protocolli legati al torrenting e alla condivisione di file di grandi dimensioni.


> [!info] Definizione: Processo
> Un **processo** è un programma in esecuzione all’interno di un sistema terminale. 
> In particolare, un **processo client** è un processo che avvia una comunicazione, mentre un **processo server** è un processo che attende di essere contattato da un processo client.

All’interno dello stesso sistema, due processi comunicano tra di loro utilizzando una comunicazione **inter-process**, definita dal sistema operativo. I processi situati su sistemi diversi, invece, comunicano tra di loro tramite **scambio di messaggi**.


> [!info] Definizone: Socket
> Un **socket** è un’**astrazione software** tramite cui un processo può inviare e ricevere messaggi tramite il socket di un altro processo. Per poter comunicare, dunque, due processi devono connettersi tramite due socket (uno ciascuno), identificati da una coppia 
> *<Indirizzo_IP , Numero_Porta>*

Ogni protocollo a livello di applicazione definisce: 

- Le **tipologie** di messaggi scambiati (es: richiesta e risposta).
- La **sintassi** del messaggio.
- La **semantica** del messaggio. 
- Le **regole** per come e quando i processi inviano e rispondono ai messaggi.

In particolare, i protocolli a tale livello si differenziano in **protocolli apert**i, ossia definiti secondo uno standard pubblico ed adottato comunemente da ogni applicazione (es: HTTP, FTP, ...), e **protocolli proprietari**, ossia non pubblici e fini all’applicazione stessa (es: Skype, ...). 

Per poter funzionare correttamente, ogni applicazione di rete necessita di alcuni servizi di trasporto. In particolare, esse possono necessitare di: 
- **Integrità dei dati**, ossia un trasferimento dei dati affidabile al 100%, senza alcuna perdita di pacchetto o corruzione dei dati.
- **Garanzie temporali**, ossia un basso ritardo per la ricezione dei dati.
- **Garanzie di throughput**, ossia una quantità minima di throughput dati.
- **Sicurezza**, ad esempio crittografia o integrità dei dati a seguito di manomissioni.


> [!info] Definizione: Transmission Control Protocol (TCP)
> **Il Transmission Control Protocol (TCP)** è un protocollo risiedente sul **layer di trasporto** in grado di fornire **trasporto affidabile**, ossia senza perdita di alcun pacchetto, e controllo del flusso e della congestione, in cambio di un’assenza di garanzie temporali, di throughput e di sicurezza. Inoltre, il protocollo TCP è **orientato alla connessione**, ossia richiedente una configurazione (**handshaking**) tra il processo client e il processo server.

^5a238a


> [!info] Definizione: User Datagram Protocol (UDP)
> L’**User Datagram Protocol (UDP)** è un protocollo risiedente sul **layer di trasporto** in grado di fornire **trasporto veloce** poiché **non orientato alla connessione** ed estremamente scarno, ossia sprovvisto di: trasporto affidabile, controllo del flusso e della congestione e garanzie temporali, di throughput e di sicurezza

> [!warning]- Perche UDP allora invece di TCP?
> **TCP** pur essendo affidabile ha bisogno dell'handshake (**tempo perso** per la comunicazione vera e propria) mentre UDP no. E anche vero che UDP non ha tutte le feature di TCP ma da UDP possiamo avere un "livello base" che andremo ad arricchire (con quello che ci serve creando un **UDP "custom"**) non nel livello di trasporto ma nel livello applicazione (la gestione del flusso la implementiamo noi a livello applicazione)

Poiché per loro natura i protocolli TCP ed UDP sono privi di garanzie di sicurezza, i messaggi scambiati tra socket TCP e UDP risultano sprovvisti di crittografia, attraversando il percorso instradato completamente in chiaro ed essendo quindi leggibili e manipolabili da qualsiasi dispositivo intermedio. 

Per ovviare tale problema, viene implementato a livello di applicazione il protocollo **Transport Layer Security (TLS)** tramite socket realizzati con librerie software specifiche, fornendo connessioni crittografate, integrità dei dati ed autenticazione dell’endpoint.
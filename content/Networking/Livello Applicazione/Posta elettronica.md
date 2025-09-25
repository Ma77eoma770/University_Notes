---
date:
  - 2025-03-30
tags:
  - Posta_elettronica
  - Internet
  - Livello_applicazione
---

Il servizio di posta elettronica è costituito da tre entità fondamentali: 
- **Uno User agent (UA)**: detto anche **mail reader**, è un processo attivo sul client utente attivato dall’utente stesso o da un timer. Si occupa di informare l’utente nel caso in cui sia disponibile una nuova email da leggere nella sua casella di posta. Inoltre, lo user agent permette la composizione, l’editing, l’invio e la lettura di messaggi di posta elettronica. Ogni messaggio di posta inviato da un UA viene passato ad un MTA.
- **Mail Transfer Agent (MTA)**: è un processo attivo su un mail server utilizzato per il trasferimento tramite Internet di un messaggio ricevuto da un UA o da un altro MTA.
- **Mail Access Agent (MAA)**: è un processo attivo su un mail server utilizzato per leggere i messaggi di posta in arrivo.

Ogni mail server è dotato di una casella di posta (mailbox), contenente i messaggi in arrivo per l’utente, ed una coda di messaggi, contenente i messaggi dell’utente ancora da inviare.

![[Schema Mail server|1000]]

![[Schema_Protocolli|1000]]


> [!tip] Protoccoli
> [[Protocolli SMTP e MIME]] vs. [[Protocolli POP3 e IMAP]]

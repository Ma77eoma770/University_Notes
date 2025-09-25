---
date:
  - 2025-03-30
tags:
  - Posta_elettronica
  - POP3IMAP
  - Protocollo
  - Livello_applicazione
---

## POP3


> [!info] Definizione: Protocollo POP3
> Il **protocollo POP3 (Post Office Protocol vers. 3)** è un protocollo stateless a livello di applicazione utilizzato per il download di messaggi di posta elettronica ricevuti. La sua porta di riferimento comune all’interno dei socket è la **porta 110**. Per stabilire una connessione, il protocollo POP3 fa uso del protocollo **TCP**, effettuando quindi l’handshake TCP, per poi procedere nelle seguenti tre fasi: 
> 1. Autorizzazione, dove lo UA invia nome utente e password per essere identificato dal mail server.
> 2. Transazione, dove lo UA recupera i messaggi nella casella di posta dell’utente.
> 3. Aggiornamento, dove, successivamente all’invio di un messaggio QUIT da parte dello UA, viene terminata la connessione e vengono rimossi dal mail server i messaggi contrassegnati durante la fase precedente


> [!example] Esempio
> Se la richiesta effettuata viene eseguita correttamente, il server risponderà con +OK, altrimenti con -ERR. Il comando *retr* permette di scaricare il messaggio, mentre il comando *dele* permette di marcare i messaggi da eliminare
> ```
> S: +OK POP3 server ready 
> C: user rob 
> S: +OK 
> C: pass hungry 
> S: +OK user successfully logged on 
> C: list 
> S: 1 498 
> S: 2 912
> S: . 
> C: retr 1 
> S: <message 1 contents>
> S: . 
> C: dele 1 
> C: retr 2 
> S: <message 2 contents> 
> S: . 
> C: dele 2 
> C: quit 
> S: +OK POP3 server signing off
> ```
>Successivamente, viene attivata la fase di aggiornamento, cancellando dal mail server i messaggi marcati tramite dele

Oltre ad essere un protocollo stateless, il protocollo POP3 non fornisce all’utente la possibilità di creare cartelle remote tra cui poter suddividere i messaggi, costringendo la creazione di tali cartelle solo a livello locale, implicando che esse non siano condivise tra i vari dispositivi dell’utente.

## IMAP


> [!info] Definizione: Protocollo IMAP
> Il protocollo **IMAP (Internet Message Access Protocol)** è un protocollo a livello di applicazione utilizzato per l’accesso ai messaggi di posta elettronica ricevuti. La sua porta di riferimento comune all’interno dei socket è la **porta 143**. 
> 
> A differenza del protocollo POP3, tutti i messaggi vengono conservati nel mail server, permettendo all’utente di avere solo copie locali. Per via di ciò, il protocollo IMAP permette di: 
> 1. Associare ogni messaggio ricevuto ad una cartella, detta inbox 
> 2. Creare cartelle remote e spostare messaggi tra di esse
> 3. Effettuare ricerche nelle cartelle remote 
> 4. Conservare lo stato tra le varie sessioni dell’utente (**protocollo stateful**)

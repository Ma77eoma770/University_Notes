---
date:
  - 2025-03-30
tags:
  - Posta_elettronica
  - SMTPMIME
  - Protocollo
  - Livello_applicazione
---
## SMTP

> [!info] Definizione: Protocollo SMTP
> **Il protocollo SMTP** (Simple Mail Transfer Protocol) è un protocollo a livello di applicazione utilizzato per l’invio di messaggi di posta elettronica in formato ASCII. La sua porta di riferimento comune all’interno dei socket è la **porta 25**. 
> 
> Il protocollo SMTP effettua un trasferimento diretto, ossia dal mail server mittente a quello destinatario (dunque senza mail server intermedi), basato su un’interazione comando/risposta: viene inviato un comando in testo ASCII e viene ricevuta una risposta equivalente ad un codice di stato. 
> 
> Inoltre, il protocollo SMTP fa uso del protocollo TCP: 
> 1. Il client avvia una connessione TCP con il server utilizzando la porta 25, rimanendo in attesa che il server accetti la connessione (TCP handshaking).
> 2. Vengono scambiati messaggi di posta tra client e server (connessione persistente).
> 3. La connessione TCP viene chiusa.


> [!example]- Esempio
> 1. Alice usa il suo UA per comporre il messaggio da inviare all’indirizzo di posta elettronica bob@someschool.edu.
> 2. L’UA di Alice invia il messaggio al mail server di Alice, il quale porrà tale messaggio nella sua coda di messaggi. Successivamente, il client SMTP presente sul mail server di Alice apre una connessione TCP con il mail server di Bob.
> 3. Il client SMTP invia il messaggio di Alice sulla connessione TCP tramite il suo MTA.
> 4. Il mail server di Bob riceve il messaggio e lo pone nella casella di posta di Bob.
> 5. Bob invoca il suo UA per leggere il messaggio, il quale preleverà il messaggio tramite l’MAA presente sul suo mail server (NB: tale operazione non è svolta dal protocollo SMTP, bensì dal [[Protocolli POP3 e IMAP|protocollo POP3 o dal protocollo IMAP]])
>    
> ![[Schema_Protocolli|1000]]

In particolare, lo scambio di messaggi viene gestito dal protocollo SMTP nel seguente modo: 
1. Il client SMTP tenta di stabilire una connessione TCP sulla porta 25 con il server STMP. Se il server è attivo, la connessione TCP viene stabilita. Altrimenti, il client riproverà dopo un determinato lasso di tempo. 
2. Una volta stabilita la connessione, il client e il server effettuano una forma aggiuntiva di handshaking, dove il client indica al server l’indirizzo email del mittente e del destinatario.
3. Il client invia il messaggio sulla connessione TCP. Una volta ricevuto il messaggio, se ci sono altri messaggi da inviare viene utilizzata la stessa connessione TCP ([[Web e Protocollo HTTP#^9fd35d|connessione persistente]]). Altrimenti, il client invia al server una richiesta di chiusura della connessione.


> [!example] Esempio
> ```
> S: 220 hamburger.edu 
> C: HELO crepes.fr 
> S: 250 Hello crepes.fr, pleased to meet you 
> C: MAIL FROM: <alice@crepes.fr>
> S: 250 alice@crepes.fr... Sender ok 
> C: RCPT TO: <bob@hamburger.edu>
> S: 250 bob@hamburger.edu ... Recipient ok 
> C: DATA
> S: 354 Enter mail, end with "." on a line by itself 
> C: Do you like ketchup?
> C: How about pickles? 
> C: . 
> S: 250 Message accepted for delivery 
> C: QUIT 
> S: 221 hamburger.edu closing connection

Lo standard **RFC 822** definisce la struttura che ogni messaggio di posta elettronica deve assumere:
1. Un **header** composto dai seguenti campi:
   
   ![[Campi_header_SMTP.PNG]]
2. Un **body**, contenente il messaggio da inviare (solo **caratteri ASCII**)

## MIME


> [!info] Definizione: Protocollo MIME
> Per poter inviare contenuti diversi dal semplice test ASCII, gli standard RFC 2045 e 2046 definiscono il **protocollo MIME** (Multipurpose Internet Mail Extension), in grado di estendere i normali messaggi di posta elettronica in messaggi multimediali.

Vengono aggiunte alcune righe all’interno dell’[[Messaggi di richiesta e risposta#^4a8b71|header]] del messaggio inviato, in particolare una riga *Version*, indicante la versione del protocollo MIME utilizzata, e una riga *Type*, indicante il tipo di dati multimediali inviati, i quali, prima di essere spediti, vengono convertiti in una codifica testuale (solitamente base64), specificata da un campo aggiuntivo *Content-Transfer-Encoding*, in modo da poter essere trasmesso sottoforma di testo ASCII, per poi venir decodificati una volta che il messaggio è giunto al destinatario.
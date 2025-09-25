---
date:
  - 2025-03-30
tags:
  - Cookie
  - Caching
  - HTTP
  - Livello_applicazione
---
## Cookies

> [!info] Definizione: Cookie
> Un cookie è un piccolo file di testo contenente brevi informazioni (preferenze sull’utilizzo, parametri preferiti, token di autorizzazione, ...) salvato all’interno di un client da parte di un server web

Poiché il protocollo HTTP è un protocollo **stateless**, i cookie vengono utilizzati all’interno delle applicazioni web per conservare indirettamente alcune informazioni sulle varie comunicazioni client-server effettuate, rendendo ogni richiesta HTTP indipendente dall’altra.

A seguito di un messaggio di riposta da un web server contenente il campo header Set-Cookie, il client salva il contenuto del cookie all’interno di un file. Durante le successive richieste effettuate dallo stesso client allo stesso server, tutti i cookie impostati da tale server vengono allegati ad ogni richiesta HTTP. 

Solitamente, il cookie fornito dal server contiene un ID univoco, in modo da legare una voce nel suo database interno a quel client specifico.

![[Http_cookies.PNG]]

La durata di un cookie inviato viene specificata tramite un campo header [[Messaggi di richiesta e risposta#^4a6505|Max-Age]], tramite il quale viene specificato il tempo di vita di tale cookie in secondi. Allo scadere di tali secondi, il client eliminerà automaticamente tale cookie. Inoltre, non c’è limite alla quantità di secondi specificabili, implicando che sia possibile specificare anche una quantità di secondi pari a mesi o anni

## Web Caching 

> [!info] Definizione: Proxy Server
> Un **proxy server** e un server utilizzato come **intermediario** tra un client e il vero server destinatario.

Solitamente, tale tipologia server viene utilizzato per il web caching: 
- Se il documento richiesto è presente nella cache del proxy server, esso viene restituito al client senza dover raggiungere il server originale (risparmiando tempo).
- Se il documento richiesto non è presente nella cache, il proxy server inoltra la richiesta del client al server di origine, memorizzando nella sua cache il documento ricevuto nella risposta, restituendolo al client.
  
Tramite il web caching è possibile ridurre notevolmente i tempi di risposta e il traffico nei link di accesso alla rete del server di origine, consentendo ai fornitori di contenuti di essere più efficienti.

![[Proxy_server.PNG]]
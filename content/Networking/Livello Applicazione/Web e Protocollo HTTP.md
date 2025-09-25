---
date:
  - 2025-03-30
tags:
  - Protocollo
  - HTTP
  - Prestazioni
  - Internet
  - Livello_applicazione
---

Una pagina web è composta da **oggetti**, ognuno dei quali può essere archiviato su un diverso web server. In particolare, una pagina web consiste in un file HTML il quale include diversi oggetti referenziati tramite vari URL

![[Struttura_url_parziale.PNG]]

> [!info] Definizione: Protocollo HTTP
> Il **protocollo HTTP (Hypertext Transfer Protocol)** è un protocollo a livello di applicazione utilizzato per la realizzazione di servizi web. La sua porta di riferimento comune all’interno dei socket è la **porta 80**. 
> Il protocollo HTTP è **stateless**, ossia non conservante alcuna informazione sulle richieste passate, e basato sul paradigma [[Principi delle applicazioni di rete#^79bb29|client-server]], dove il client invia messaggi detti [[Messaggi di richiesta e risposta#Richiesta|richieste]] e il server invia messaggi detti [[Messaggi di richiesta e risposta#Risposta|risposte]]. **Inoltre**, il protocollo HTTP fa uso del protocollo [[Principi delle applicazioni di rete#^5a238a|TCP]]: 
> 1. Il client **avvia una connessione TCP** con il server utilizzando la porta 80, rimanendo in attesa che il server accetti la connessione (TCP handshaking) .
> 2. Vengono **scambiati messaggi HTTP** tra client e server .
> 3. La **connessione TCP viene chiusa**.

Le **connessioni HTTP** si differenziano in due tipologie: 
- **Connessione non persistente**: dove viene aperta la connessione TCP e viene inviato massimo **un oggetto** prima di chiudere la connessione TCP.
- **Connessione persistente (HTTP/1.1)**: dove viene aperta la connessione TCP e vengono inviati **multipli oggetti** in successione prima di chiudere la connessione TCP. ^9fd35d

> [!success] [[Versioni di HTTP]]

> [!example] Esempio connessione HTTP
> 1. Supponiamo che un utente inserisca l’URL dell’oggetto "www.someSchool.edu/someDepartment/home.index", contenente del testo e 10 riferimenti ad immagini. 
> 2. Il client HTTP dell’utente (browser, cURL, ...) avvia la connessione TCP con il server HTTP tramite la porta 80.
> 3. Il server HTTP sull’host "www.someSchool.edu" riceve la richiesta di connessione, accettandola e notificando il client.
> 4. Il client HTTP invia un messaggio di richiesta HTTP, contenente il path dell’oggetto desiderato, ossia "/someDept/index.html".
> 5. Il server HTTP riceve il messaggio di richiesta e invia il messaggio di risposta contenente l’oggetto desiderato, il quale a sua volta contiene i riferimenti alle 10 immagini. 
> 6. A questo punto, si creano due scenari: 
> 	- Se la connessione **non è persistente**: il server chiude immediatamente la connessione TCP, implicando che l’intero processo debba essere ripetuto per tutti e 10 i riferimenti necessari.
> 	- Se la connessione **è persistente**: il client invierà in successione altre 10 richieste al server, richiedendo quindi solo la ripetizione dei passaggi 4 e 5 (per 10 volte), per poi chiudere la connessione TCP.

> [!info] Definizone: Round Trip Time (RTT)
> Definiamo come **Round Trip Time (RTT)** il tempo impiegato da un pacchetto di piccole dimensioni per compiere il percorso client-server-client.

> [!warning] Osservazione: Tempo di risposta HTTP
> - Se la connessione **non è persistente**, per **ogni oggetto** sono necessari **due RTT**, uno per avviare la connessione TCP ed uno per l’invio di richiesta e risposta, seguiti dal tempo necessario ad inviare l’oggetto:
>   
> 	$T_{tot} = (2 RTT + Tempo_{invioogg}) · Num_{oggetti}$ 
> 	
>  - Se la connessione è persistente, invece, saranno necessari un RTT per poter stabilire la connessione TCP, seguiti da un solo RTT per oggetto (con annesso tempo di invio):
>
> 	$T_{tot} = 1 RTT + (1 RTT + Tempo_{invioogg}) · Num_{oggetti}$
>

![[Http_pers_vs_npers.PNG]]

> [!error] Attenzione: HTTP non conserva informazioni tra le trasmissioni, per ovviare sono stati creati i [[Cookies e Web Caching| cookies]] 


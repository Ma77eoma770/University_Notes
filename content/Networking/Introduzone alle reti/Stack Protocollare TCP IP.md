---
date:
  - 2025-03-23
tags:
  - Stack_protocollare
  - Protocollo
  - Introduzione
  - TCP/IP
aliases:
  - Stack protocollare TCP/IP
---

## Definizioni


> [!info] Definizione: Protocollo
> Un **protocollo** definisce l’insieme di regole che il dispositivo mittente e il dispositivo destinatario, così come tutti i sistemi intermedi coinvolti, devono rispettare per essere in grado di comunicare.

## Idea

In situazioni più complesse, potrebbe essere opportuno suddividere i compiti necessari alla comunicazione fra **più livelli (layer)**, nel qual caso è richiesto **un protocollo per ciascun livello**. Tramite un layering dei protocolli, dunque, è possibile suddividere un compito complesso in compiti più semplici, ognuno gestibile da un singolo protocollo. 

In particolare, ogni layer è **indipendente dagli altri** (modularizzazione), utilizzando i servizi forniti dal layer inferiore e offrendo servizi al layer superiore. 

Ogni layer, dunque, può essere considerato come una **black box** con opportuni ingressi ed uscite, senza necessità di essere a conoscenza delle modalità con cui i dati in ingresso vengano trasformati in quelli di uscita. 

Quando è richiesta una **comunicazione bidirezionale**, ciascun layer deve essere in grado di effettuare **entrambi i compiti richiesti**, ossia **manipolare i dati in input per inviarli al livello superiore o manipolarli per inviarli al livello inferiore.**

![[Stack_layer.PNG]]

In particolare, l’effetto ottenuto tramite una suddivisione in uno stack di layer equivalenti permette l’instaurazione di un **collegamento logico** tra ogni livello dello stack: il protocollo implementato in ciascun livello specifica una comunicazione diretta tra i pari livelli delle due parti: il layer N di un dispositivo comunica solo ed esclusivamente con il layer N di tutti i dispositivi.

![[Protocol_service.PNG]]

Inoltre, per via dell’estrema **modularizzazione** ottenuta, viene facilitata la manutenzione e l’aggiornamento del sistema, poiché il cambiando dell’implementazione del servizio di un layer rimane trasparente al resto del sistema.

## Stack protocollare TCP/IP

> [!info] Definizione: Stack protocollare TCP/IP
> La principale forma di stack protocollare utilizzata corrisponde allo stack protocollare TCP/IP, la cui struttura a layer corrisponde a:
> - **Livello di Applicazione**: il quale fornisce supporto alle applicazioni facente uso della rete (protocolli HTTP, SMTP, FTP, DNS, ...). 
> - **Livello di Trasporto**: il quale gestisce il trasferimento dei pacchetti dal processo del dispositivo mittente a quello del dispositivo destinatario (protocolli TCP, UDP, ...). 
> - Livello di Rete, il quale gestisce l’instradamento dei pacchetti dall’origine alla destinazione (protocolli IP, ...). 
> - **Livello di Collegamento (o Link)**: il quale gestisce la trasmissione dei pacchetti da un nodo a quello successivo sul percorso (protocolli Ethernet, Wi-Fi, PPP, ...). Lungo il percorso, un pacchetto può essere gestito da protocolli diversi. 
> - **Livello Fisico**: dove avviene il vero e proprio trasferimento dei singoli bit.
>   
>   ![[Stack_TCPIP.PNG]]
>   
>   I livelli di Applicazione e di Trasporto sono gestiti tramite **software**, mentre i livelli di Collegamento e Fisico tramite **hardware**.

Durante l’invio di un pacchetto, quest’ultimo, partendo dal livello applicazione del dispositivo sorgente, **percorre tutti i layer dello stack protocollare**, fino a giungere al livello fisico, dove viene effettivamente inviato al nodo successivo. 

Tutti i nodi intermedi presenti sul percorso lavoreranno utilizzando solo i **livelli necessari**. In particolare, ogni dispositivo utilizzerà il livello di collegamento, in modo da poter spedire il pacchetto stesso verso il nodo successivo del percorso. 

Nel caso in cui si raggiunga il punto di scambio tra due reti, solitamente un edge router, verrà utilizzato anche il livello di rete.

![[Incapsulamento_pacchetti.PNG]]

Prima di essere spedito al livello inferiore, ogni pacchetto viene incapsulato: una volta ricevuto il pacchetto dal layer superiore, il layer attuale applica un proprio header (o intestazione), aggiungendo informazioni necessarie al layer del dispositivo di destinazione corrispondente a quello attuale. 

## Nomenclatura a seconda del livello

In particolare, ad ogni livello un pacchetto assume il nome di:
- **Messaggio (al livello di applicazione)**: corrispondente al pacchetto originale, senza alcuna intestazione.
- **Segmento (al livello di trasporto)**: corrispondente al messaggio ricevuto dal layer superiore a cui viene aggiunto un header di trasporto.
- **Datagramma (al livello di rete)**: corrispondente al segmento ricevuto dal layer superiore a cui viene aggiunto un header di rete.
- **Frame (al livello di collegamento)**: corrispondente al datagramma ricevuto dal layer superiore a cui viene aggiunto un header di collegamento.

![[Intestazione_pacchetti.PNG]]

## Operazioni necessarie per ogni livello

Poiché lo stack protocollare TCP/IP prevede la presenza di **più protocolli nello stesso livello**, ogni livello deve essere in grado di effettuare operazioni di:
-  **Multiplexing**: dove ogni protocollo deve essere in grado di incapsulare (uno alla volta) i pacchetti ricevuti da più protocolli presenti al livello superiore.

![[Multiplexing.PNG]]

- **Demultiplexing**: dove ogni protocollo deve essere in grado di decapsulare i pacchetti ricevuti ed inviarli a più protocolli presenti nel livello superiore.

![[Demultiplexing.PNG]]

Per realizzare ciò, nell’header di ogni layer viene inserito un campo speciale in grado di identificare quale sia il protocollo di appartenenza di tale pacchetto. 

## Open Systems Interconnection (OSI)

Un’evoluzione dello stack protocollare TCP/IP è il modello Open Systems Interconnection (OSI), dove vengono interposti due livelli tra il livello di applicazione e il livello di trasporto: 
- **Livello di Presentazione**: utilizzato per consentire alle applicazioni di interpretare i dati (es: crittografia, compressione, ...).
- **Livello di Sicurezza**: utilizzato per gestire servizi come la sincronizzazione o il ripristino dello scambio di dati.

![[Modello_OSI.PNG]]
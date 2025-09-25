---
date:
  - 2025-03-23
tags:
  - Internet
  - Introduzione
---

## Definizioni

> [!info] Definizione: Rete Internet
> Definiamo come **internet** (abbreviativo di inter-network) una **rete di reti**, ossia una rete che mette in comunicazione due o più reti tra di loro. 
> *Attenzione*: nonostante ciò che viene comunemente chiamato l’**Internet** sia una internet, è necessario puntualizzare che con tale termine comune viene indicata la rete di tutte le reti.

## Struttura

Al suo interno la struttura di Internet risulta essere composta da:

- **Periferia della rete (network edge)**: corrispondente all’insieme di tutti i sistemi terminali connessi.

 ![[Periferia_rete.PNG]]

- **Reti di accesso (access network)**: corrispondente ai collegamenti fisici che connettono un sistema terminale al primo **edge router**, ossia il primo router presente nel percorso dal sistema terminale di origine ad un qualsiasi altro sistema terminale di destinazione. In particolare l'accesso all'Internet puo essere affettuato in piu modi:
	- **Accesso via cavo**: tramite supporti fisici connessi direttamente ad una rete di distribuzione, detta cable headend (es: il centralino di un [[ISP]]). 
	- **Accesso via Digital Subscriber Line (DSL)**: dove viene utilizzata la linea telefonica esistente per collegarsi alla rete dell’ISP. 
	- **Accesso via Wireless LAN (WLAN)**: tramite un collegamento wireless ad una stazione base detta [[access point]] connessa con il [[Rete, Host e Collegamenti#^c64bd3|router]], a sua volta connesso con un cable headend.
	- **Accesso via rete cellulare**: dove viene utilizzata la rete cellulare esistente per collegarsi alla rete dell’ISP.
	- **Accesso via rete aziendale**: tramite una rete aziendale (o universitaria, privata, ...) direttamente connessa ad Internet.

![[Reti_accesso.PNG]] 

- **Nucleo di rete (core o backbone)**: ossia un sistema di router interconnessi tra di loro, corrispondente all’insieme di nodi tramite cui viene realizzata la vera interconnessione tra tutte le reti. In particolare, all’interno del backbone di Internet sono presenti più livelli di reti ISP (es: regionali, nazionali, aziendali, ...), le quali devono essere interconnesse tra di loro tramite degli [[Internet Exchange Point (IXP)]]. Inoltre, nel recente periodo, nel backbone di Internet sono state integrate anche delle grandi reti private aziendali, ossia le reti dei **content provider** (es: Google, Netflix, ...), le quali, ormai, funzionano come vere e proprie ISP.

![[Nucleo_rete.PNG]]
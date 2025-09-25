---
date:
  - 2025-04-06
tags:
  - DNS
  - Livello_applicazione
  - Caching
---
## Definizioni


> [!info] Definizione: Domain Name System (DNS)
> Il **Domain Name System (DNS)** è un sistema utilizzato per mappare singoli nodi di una rete ad un nome che li identifichi. Viene realizzato tramite un database distribuito, implementato come una gerarchia di **name server**.

Tra le funzioni fornite dal servizio DNS troviamo: 
1. **Traduzione** da nome host all’indirizzo IP relativo
2. **Distribuzione** del carico, permettendo a più indirizzi IP, ognuno legato ad un server copia di quello originale, di corrispondere ad un unico nome. Quando un client effettua una richiesta, il servizio restituisce l’insieme di indirizzi legati a tale nome in un ordine casuale (rotazione DNS).
3. **Host Aliasing**, ossia l’associazione di più sinonimi (alias) allo stesso indirizzo IP, permettendo l’associazione di un nome più semplice rispetto ad uno complesso (es: al nome relay1.west-coast.enterprise.com associamo l’alias enterprise.com e l’alias www.enterprise.com) 

> [!hint] Per via delle sue funzioni, il servizio DNS risulta essere **fondamentale** per Internet.
> 

In particolare, la decentralizzazione del servizio DNS risulta essere critica: se il servizio fosse centralizzato (ossia effettuato da un singolo nodo o rete) sarebbe sufficiente un singolo punto di fallimento affinché il servizio diventi inutilizzabili. Inoltre, se il servizio fosse centralizzato si avrebbe un volume di traffico troppo elevato dovuto alle miliardi di richieste effettuate giornalmente (es: il server DNS Comcast riceve 600 miliardi di richieste al giorno)
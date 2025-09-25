---
date:
  - 2025-04-22
tags:
  - Livello_Trasporto
  - Equita
---

> [!info] Proposizione: Equita nelle connessioni
> Affinché un protocollo di trasporto sia definibile equo, se K sessioni di tale protocollo condividono lo stesso bottleneck link con larghezza di banda R, ciascuna delle K sessioni deve avere una velocità media pari a $R / K$

Notiamo con facilità che il protocollo UDP sia un protocollo non equo per via dell’assenza di un controllo della congestione e di limiti sulla banda utilizzabile.
Per tale motivo, spesso applicazioni multimediali utilizzanti il protocollo UDP (es: i servizi
streaming) "rubano" velocità di connessione ad altre applicazioni.
Per quanto riguarda il protocollo TCP, invece, è necessario effettuare uno studio:

- Consideriamo il seguente scenario con due sessioni TCP con algoritmo AIMD concorrenti sullo stesso bottleneck link
  
![[Equita_scenario.png|700]]
- Tramite l’additive increase viene generata una pendenza pari ad 1
- Tramite il multiplicative decrease viene ridotto proporzionalmente il throughput
  
![[Schema_equita_scenario.png|700]]

Sotto ipotesi idealizzate, dunque, il protocollo TCP risulta essere equo (es: stesso RTT, numero fisso di sessioni, ...). Tuttavia, molte applicazioni moderne utilizzano più di una connessione TCP parallela tra due host (es: un web browser). Di conseguenza, anche se la larghezza di banda fosse equamente distribuita tra tutte le connessioni possibili tra i due host, tale applicazione otterrebbe comunque una quantità di banda superiore alle altre applicazioni.
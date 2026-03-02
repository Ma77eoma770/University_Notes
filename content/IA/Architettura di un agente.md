---
date:
  - 2026-02-27
tags:
  - IA
  - Agenti
---
> [!abstract] La formula base 
> Agente = Architettura + Programma 
> L'architettura è l'hardware/sistema fisico che riceve percezioni ed esegue azioni; il programma è l'implementazione software della funzione agente. 

Esistono quattro tipologie principali di programmi agente, in ordine crescente di generalità: 
1. **Agenti reattivi semplici:** L'azione è determinata unicamente dalla percezione corrente tramite regole "condizione-azione". Semplici ma limitati se l'azione richiede informazioni passate. 
2. **Agenti reattivi basati su modello:** Valutano la percezione corrente insieme a uno stato interno (un modello del mondo) che tiene traccia della parte di ambiente attualmente non osservabile. 
3. **Agenti basati su obiettivi:** Oltre allo stato, considerano i propri obiettivi. Poiché un obiettivo potrebbe non essere raggiungibile con una singola azione, questi agenti utilizzano algoritmi di ricerca e pianificazione. 
4. **Agenti basati sull'utilità:** Non si limitano a raggiungere un obiettivo, ma cercano di massimizzare un'utilità attesa (es. il percorso non solo corretto, ma anche più veloce e sicuro). Fondamentali in ambienti incerti. 

>[!tip] Agenti in grado di apprendere 
>Qualsiasi architettura può essere migliorata con l'apprendimento. Elementi chiave: 
>- **Performance element:** Seleziona le azioni. 
>- **Critic:** Fornisce feedback per migliorare il performance element. 
>- **Problem generator:** Suggerisce azioni esplorative per nuove esperienze.


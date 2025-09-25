---
date:
  - 2025-03-23
tags:
  - Prestazioni
  - Introduzione
  - Delay
---

## Definzioni


> [!info] Definizione: **Larghezza di banda e Transmission rate**
> Con il termine larghezza di banda (bandwidth) indichiamo due concetti strettamente legati tra loro: 
> - La quantità (espressa in Hz) rappresentante la **larghezza dell’intervallo di frequenze** utilizzato dal sistema trasmissivo, ossia l’intervallo di frequenze utilizzato dal sistema trasmissivo. Maggiore è tale quantità, maggiore è la quantità di informazioni veicolabili tramite il mezzo di trasmissione. 
> - La quantità (espressa in b/s) detta anche **transmission rate (o bit rate)** rappresentante la quantità di bit al secondo che un link garantisce di trasmettere. Tale quantità è proporzionale alla larghezza di banda (in Hz).


> [!info] Definizione: Throughtput
> Con il termine throughput indichiamo la quantità di bit al secondo che passano attraverso un nodo della rete.


> [!bug] Osservazione
> A differenza del transmission rate, il quale fornisce una misura della potenziale velocità di un link, il throughput fornisce una misura dell’effettiva velocità di un link. In generale, dunque, si ha che T < R dove T è il throughput e R è il transmission rate


> [!info] Definizione: Collo di Bottiglia
> Dato un percorso end-to-end, ossia tra un dispositivo e un altro, definiamo come collo di bottiglia il link limitante il throughput dei link presenti su tale percorso.


> [!example]- Esempio collo di bottiglia
> ![[Collo_bottiglia.PNG]]
> 
>Il link L2 risulta essere il collo di bottiglia di tale percorso, limitando il throughput del percorso a 100 kb/s


> [!info] Definizione: Delay di trasmissione
Definiamo come delay (o latenza) di trasmissione il tempo necessario ad un nodo per immettere un pacchetto su un link, corrispondente a: 
$D_t = \frac{L}{R}$ 
dove L è la dimensione del pacchetto e R è il transmission rate del link


> [!info] Definizione: Delay di propagazione
> Definiamo come delay (o latenza) di propagazione il tempo impiegato dall’ultimo bit di blocco di dati posto su un link ad essere propagato fino al nodo di destinazione, corrispondente a:
> $D_p = \frac{k}{v}$ 
> dove k è la lunghezza del link e v è la velocità di propagazione del link.


> [!info] Definizone: Delay di un pacchetto
> Definiamo come delay (o latenza) di un pacchetto il tempo totale necessario ad un pacchetto per essere inviato completamente da un nodo origine ad un nodo destinatario.
> 
> $D_n = D_e + D_q + D_t + D_p$ 
> 
> Dove: 
> - $D_e$ è il delay di elaborazione del nodo, dipendente dalle operazioni di controllo svolte dal nodo.
> - $D_q$ è il delay di queueing, ossia l’attesa del pacchetto all’interno della coda del nodo prima di essere trasmesso, dipendente dalla quantità di pacchetti presenti nella coda .
> - $D_t$ è il delay di trasmissione del link.
> - $D_p$ è il delay di propagazione del link.


> [!info] Definizone: Prodotto rate per delay di propagazione
> Dato un link con transmission rate R e delay di propagazione Dp, il prodotto:
> $B_{max} = R · D_p = \frac{L · k} {D_t · v}$
> rappresenta il **massimo numero di bit distribuiti tutto sul cavo**.

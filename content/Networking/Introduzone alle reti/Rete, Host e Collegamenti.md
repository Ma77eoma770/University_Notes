---
date:
  - 2025-03-23
tags:
  - Rete
  - Link
  - Nodi
  - Tipologie_Reti
  - Introduzione
---

## Definizioni iniziali

> [!info] Definizione: Rete e Link
> Una rete è un’infrastruttura composta da dispositivi detti nodi della rete in grado di scambiarsi informazioni tramite dei mezzi di comunicazione, wireless o cablati, detti link (o collegamenti)

> [!info] Definizione: Nodi di una rete
> I nodi costituenti una rete vengono differenziati in due macro-categorie:
> - **Sistemi terminali**: differenziati a loro volta in: 
> 	  - **Host**: ossia un dispositivo di proprietà dell’utente dedicato ad eseguire applicazioni utente.
> 	  - **Server**: ossia un dispositivo di elevate prestazioni destinato ad eseguire programmi che forniscono un servizio a diverse applicazioni utente.
> 	  
>- **Dispositivi di interconnessione**: ossia dei dispositivi atti a modificare o prolungare il segnale ricevuto, differenziati a loro volta in: 
> 	 - **Router**: ossia dispositivi che collegano una rete ad una o più reti 
> 	 - **Switch**: ossia dispositivi che collegano più sistemi terminali all’interno di una rete 
> 	 - **Modem**: ossia dispositivi in grado di trasformare la codifica dei dati in segnale e viceversa

^c64bd3

## Tipologie di reti

Classificazione delle varie tipologie di reti in:
- **Personal Area Network (PAN)**: avente scala ridotta, solitamente equivalente a pochi metri (es: una rete Bluetooth) 
- **Local Area Network (LAN)**: solitamente corrispondente ad una rete privata che collega i sistemi terminali di un appartamento (es: una rete Wi-Fi o Ethernet). Ogni sistema terminale possiede un indirizzo che lo identifica univocamente all’interno della LAN. Si differenziano in:
	- **LAN con cavo condiviso**, ossia dove tutti i dispositivi sono connessi al router tramite un cavo comune. ![[LAN_cavo_condiviso.PNG]]
	- **LAN con switch**, ossia dove tutti i dispositivi sono connessi ad uno o più switch, i quali a loro volta sono connessi al router .![[LAN_switch.PNG]]
- **Metropolitan Area Network (MAN)**: avente scala pari ad una città 
- **Wide Area Network (WAN)**: avente scala pari ad un paese o una nazione, solitamente gestita da un Internet Service Provider (ISP). Si differenziano in WAN point-to-point, ossia collegante due reti tramite un singolo mezzo di trasmissione, e WAN a commutazione, ossia collegante più reti tramite più mezzi e dispositivi di collegamento 
- **L’Internet**: avente scala globale

> [!example]- Esempio LAN composta 
> ![[Rete_composta.PNG]]

## Supporti fisici

I supporti fisici utilizzabili per una trasmissione si differenziano in: 
- **Doppino intrecciato** (ad esempio un cavo Ethernet), composto da due fili di rame isolati, uno utilizzato per inviare i dati ed uno per riceverli.
- **Cavo coassiale** composto da due conduttori di rame concentrici, entrambi bidirezionali, avente una larghezza di banda maggiore.
- **Cavo in fibra ottica**: composto da una fibra di vetro che trasporta impulsi luminosi (dunque alla velocità della luce) al suo interno, ognuno rappresentante un singolo bit.
- **Trasmissione wireless**: realizzata tramite l’invio di un segnale radio propagato nell’aria (es: rete cellulare, satellitare o Wi-Fi).

## Back Links

[[Networking/Index]]
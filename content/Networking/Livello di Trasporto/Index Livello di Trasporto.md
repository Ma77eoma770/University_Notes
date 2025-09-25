---
date:
  - 2025-04-06
tags:
  - Index
  - Livello_Trasporto
---
I servizi forniti nel livello di trasporto forniscono comunicazione **logica** tra processi applicativi in esecuzione su dispositivi, a differenza del livello di rete, il quale si occupa della comunicazione logica direttamente tra i dispositivi stessi. In particolare, il dispositivo mittente suddivide i messaggi dell’applicazione in **segmenti**, passandoli al livello di rete, mentre il dispositivo destinatario riassembla i segmenti in messaggi.

 1. [[Index Livello di Trasporto|Livello di Trasporto]]
	1. [[Multiplexing e Demultiplexing]]
	2. [[Protocollo UDP]]
	3. [[Trasferimento affidabile dei dati]]
		1. [[Protocollo RDT 1.0 e 2.0]]
		2. [[Protocollo RDT 2.1 e 2.2]]
		3. [[Protocollo RDT 3.0]]
		4. [[Go-back-N e Selective repeat]]
	4. [[Protocollo TCP]]
		1. [[Gestione dei timeout e stima del RTT]]
		2. [[Controllo del flusso]]
		3. [[Gestione della connessione]]
	5. [[Controllo della congestione]]
		1. [[Cause e costi della congestione]]
		2. [[Controllo della congestione nel TCP]]
	6. [[Equita dei protocolli di trasporto]]
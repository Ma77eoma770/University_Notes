---
date:
  - 2026-03-02
tags:
  - Crypto
  - Sicurezza
---
# Crittografia Simmetrica

> [!summary] Definizione
> La crittografia simmetrica (nota anche come crittografia convenzionale o a chiave singola) è la tecnica universale per garantire la **confidenzialità** dei dati trasmessi o memorizzati. 

[Image of Symmetric Encryption block diagram]

Per un utilizzo sicuro, sono necessari due requisiti fondamentali:
1. Un algoritmo di cifratura forte.
2. Mittente e destinatario devono aver ottenuto una copia della chiave segreta in modo sicuro e devono mantenerla rigorosamente protetta.

## Attacchi alla Crittografia Simmetrica
Esistono due approcci principali per attaccare questo tipo di algoritmo:
* **Crittoanalisi (Cryptanalysis):** Sfrutta la natura stessa dell'algoritmo. L'attaccante si basa su una conoscenza parziale delle caratteristiche generali del testo in chiaro o su alcuni campioni di coppie "testo in chiaro - testo cifrato". L'obiettivo è dedurre un testo specifico o, peggio, la chiave utilizzata. Se la chiave viene scoperta, tutti i messaggi (passati e futuri) cifrati con essa sono compromessi.
* **Attacco Brute-Force:** Consiste nel provare sistematicamente tutte le chiavi possibili su un testo cifrato finché non si ottiene una traduzione intelligibile in testo in chiaro. Statisticamente, in media è necessario testare la metà di tutte le chiavi possibili per ottenere un successo.

## Principali Algoritmi Simmetrici

| Algoritmo | Dimensione Blocco | Dimensione Chiave | Note Tecniche |
| :--- | :--- | :--- | :--- |
| **DES (Data Encryption Standard)** | 64 bit | 56 bit | Conosciuto anche come DEA. Fino a poco tempo fa il più usato e studiato. Oggi la lunghezza della chiave (56 bit) è considerata totalmente inadeguata a causa dell'elevata velocità dei processori commerciali. |
| **3DES (Triple DES)** | 64 bit | 112 o 168 bit | Standardizzato nel 1985 (ANSI X9.17) per applicazioni finanziarie. Ripete l'algoritmo DES tre volte con 2 o 3 chiavi uniche. La chiave da 168 bit supera le vulnerabilità al brute-force, ma l'algoritmo risulta molto lento via software e mantiene il limite del blocco a 64 bit. |
| **AES (Advanced Encryption Standard)** | 128 bit | 128, 192 o 256 bit | Selezionato dal NIST nel 2001 (algoritmo Rijndael, FIPS 197) per sostituire il 3DES. Offre un'efficienza significativamente migliorata e un livello di sicurezza eccellente. |

> [!tip] Tempi di decifratura e minaccia quantistica
> Con processori capaci di $10^{13}$ decifrazioni al secondo, forzare un DES richiede circa 1 ora. Forzare un AES a 128 bit richiederebbe $5.3 \times 10^{17}$ anni. Tuttavia, l'avvento dei computer quantistici minaccia gli algoritmi attuali, in quanto sono in grado di risolvere facilmente problemi matematici complessi (come la fattorizzazione di interi o i logaritmi discreti su curve ellittiche). Per questo si studiano algoritmi crittografici *quantum-resistant*.

## Tipi di Cifratura Simmetrica

### Block Cipher (Cifratura a blocchi)
Elabora l'input suddividendolo in blocchi di elementi di dimensione fissa, producendo un output della stessa dimensione per ciascun blocco. È l'approccio più comune e permette il riutilizzo delle chiavi.
* **Modalità ECB (Electronic Codebook):** È l'approccio più semplice alla cifratura a blocchi multipli, in cui ogni blocco di testo in chiaro è cifrato usando la stessa identica chiave.
* **Vulnerabilità dell'ECB:** Un crittoanalista può sfruttare le regolarità nel testo in chiaro. Ad esempio, se è noto che un messaggio inizia sempre con campi predefiniti, l'attaccante avrà a disposizione numerose coppie note di "testo in chiaro - testo cifrato" con cui lavorare.
[Image of Electronic Codebook ECB mode encryption]

### Stream Cipher (Cifratura a flusso)
Elabora gli elementi di input in modo continuo, producendo l'output un elemento alla volta (tipicamente un byte alla volta).
* **Vantaggi:** È quasi sempre più veloce dei cifrari a blocchi e utilizza molto meno codice.
* **Funzionamento:** Utilizza un generatore per creare un flusso di byte pseudo-casuale (incalcolabile e imprevedibile senza la conoscenza della chiave di input), che viene combinato (solitamente tramite XOR) con il testo in chiaro.
[Image of Stream cipher encryption process]
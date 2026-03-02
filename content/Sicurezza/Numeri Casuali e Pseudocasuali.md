---
date:
  - 2026-03-02
tags:
  - Sicurezza
  - Crypto
---
# Numeri Casuali e Pseudocasuali

Le applicazioni crittografiche dipendono pesantemente dalla generazione di numeri casuali.

## Casi d'Uso Principali

I numeri casuali vengono utilizzati per la generazione di:
* **Chiavi** per algoritmi a chiave pubblica (es. RSA).
* **Stream key** (chiavi di flusso) per i cifrari simmetrici a flusso (Stream ciphers).
* **Chiavi simmetriche temporanee**, come le chiavi di sessione.
* Chiavi one-time utilizzate per la creazione di **Buste Digitali** (Digital Envelopes).
* Valori per il processo di **Handshaking**, fondamentali per prevenire i *replay attacks* (attacchi di ripetizione, in cui un attaccante intercetta e riutilizza un vecchio pacchetto dati).

## Requisiti di un Buon Numero Casuale

Per essere sicura, una sequenza di numeri deve rispettare precisi criteri statistici:
1. **Casuali (Randomness):** * *Distribuzione Uniforme:* La frequenza di occorrenza di ciascun numero all'interno della sequenza deve essere approssimativamente identica.
   * *Indipendenza:* Nessun valore nella sequenza deve poter essere dedotto dagli altri (anche se questa è una proprietà molto difficile da dimostrare in modo assoluto).
2. **Imprevedibilità (Unpredictability):** Ogni numero deve essere statisticamente indipendente dagli altri elementi della sequenza. Un avversario non deve in alcun modo essere in grado di prevedere gli elementi futuri basandosi sulla conoscenza degli elementi precedenti.
## Generatori Pseudocasuali vs Veramente Casuali

Le applicazioni fanno tipicamente uso di tecniche algoritmiche per generare questi numeri, ma esistono due approcci nettamente diversi:

* **Pseudorandom Numbers (PRNG):** Gli algoritmi sono per natura deterministici, pertanto producono sequenze che, seppur soddisfino eccellentemente i test statistici di casualità (randomness), non sono matematicamente casuali. A causa del determinismo, sono "probabilmente prevedibili" se un attaccante scopre l'algoritmo o lo stato iniziale.
* **True Random Number Generator (TRNG):** Utilizzano una vera e propria sorgente "non deterministica" per produrre casualità. Invece di usare formule matematiche, misurano processi fisici o naturali altamente imprevedibili (es. rumore termico, shot noise, instabilità elettronica o effetti quantistici). I TRNG (Hardware RNG) sono sempre più spesso integrati nativamente nei processori moderni per garantire livelli di sicurezza superiori.
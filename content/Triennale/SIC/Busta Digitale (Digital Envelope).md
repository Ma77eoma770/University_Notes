---
date:
  - 2026-03-02
tags:
  - Sicurezza
  - Crypto
---
# Busta Digitale (Digital Envelopes)

> [!summary] Il Contesto
> La crittografia a chiave pubblica è utilizzata per proteggere una chiave simmetrica. Questo approccio è vitale quando Bob vuole inviare un messaggio confidenziale ad Alice, ma i due **non condividono** in anticipo una chiave segreta simmetrica.
## Il Processo di Creazione (Mittente - Bob)
1. Bob prepara il messaggio in chiaro che vuole inviare.
2. Bob genera una **chiave simmetrica casuale** che verrà utilizzata esclusivamente per questa singola trasmissione (one-time key).
3. Bob cifra il messaggio utilizzando l'algoritmo di crittografia simmetrica e la chiave one-time appena creata (molto veloce).
4. Bob cifra la chiave simmetrica one-time utilizzando la crittografia a chiave pubblica con la **chiave pubblica di Alice** (idealmente ottenuta tramite un certificato verificato, per avere la garanzia che sia valida).
5. Bob allega la chiave one-time cifrata al messaggio cifrato, creando così la "Digital Envelope", e la invia ad Alice.

## Il Processo di Apertura (Destinatario - Alice)

1. Alice riceve la busta digitale.
2. Utilizza la propria **chiave privata** per decifrare la chiave simmetrica one-time.
3. Ottenuta la chiave simmetrica in chiaro, la utilizza per decifrare il payload principale contenente il messaggio.

> [!info] Sicurezza
> Solo Alice è in grado di decifrare la chiave one-time (poiché l'operazione richiede la sua chiave privata, che non viaggia mai in rete), e di conseguenza è l'unica a poter recuperare il messaggio originale. Questo sistema unisce la velocità della crittografia simmetrica con la sicurezza della distribuzione asimmetrica.
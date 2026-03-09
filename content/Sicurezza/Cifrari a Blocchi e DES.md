---
date:
  - 2026-03-09
tags:
  - Crypto
  - Sicurezza
---
> [!abstract] Block Ciphers
> Un cifrario a blocchi simmetrico consiste in una sequenza di round con sostituzioni e permutazioni controllate da una chiave. Molti si basano sulla struttura di Feistel.

## Parametri di Progettazione
- **Dimensione del blocco (Block size)**: Dimensioni maggiori offrono più sicurezza ma riducono la velocità. Un blocco da 128 bit è un ragionevole compromesso ed è quasi universale.
- **Dimensione della chiave (Key size)**: Più è grande, maggiore è la sicurezza, anche se può diminuire la velocità di esecuzione. La lunghezza più comune va da 128 a 256 bit.
- **Numero di round**: Rounds multipli incrementano la sicurezza. La dimensione tipica è 16 round.
- **Algoritmo generazione sotto-chiavi** e **Funzione di round**: Una maggiore complessità in questi elementi porta a una maggiore resistenza alla criptoanalisi.

> [!example] Data Encryption Standard (DES)
> Standard del 1977. Il plaintext ha una lunghezza di 64 bit e la chiave di 56 bit. Usa 16 round di elaborazione basati su una variazione della rete di Feistel. Dalla chiave originale vengono generate 16 subkeys. La decrittografia usa le subkey in ordine inverso ($K_{16}$ al primo round, ecc.).

## Triple DES (3DES)
Introdotto nel 1985.
- Usa tre chiavi e tre esecuzioni dell'algoritmo DES.
- Operazioni: Crittografa (K1) -> Decrittografa (K2) -> Crittografa (K3).
- La fase di decrittografia intermedia permette la retrocompatibilità per leggere dati crittografati con il vecchio DES singolo (ponendo K1=K2 o K1=K3 per simulare una sola chiave).
- Lunghezza chiave: 168 bit (3 chiavi distinte) o 112 bit (con K1=K3).
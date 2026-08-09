---
date:
  - 2026-03-09
tags:
  - Crypto
  - Sicurezza
---
> [!info] Cos'è l'AES
> L'AES ha un blocco di 128 bit e una chiave lunga 128, 192 o 256 bit. A differenza del DES, non usa una struttura di Feistel, ma processa l'intero blocco di dati in parallelo. Il blocco di input è visto come una matrice quadrata di byte 4x4 chiamata *State array*.

## Struttura e Fasi (Versione 128-bit)
Ci sono 10 round. In ogni round si susseguono 4 fasi, ad eccezione dell'ultimo che ne ha solo 3:

1. **Substitute Bytes (S-box)**: Sostituzione byte per byte tramite tabella di lookup (S-box) che contiene la permutazione di tutti i possibili valori a 8 bit. È progettata per resistere agli attacchi crittanalitici fornendo bassa correlazione tra input e output.
2. **Shift Rows**: Permutazione circolare applicata riga per riga (la prima riga non cambia, la seconda viene shiftata di 1 byte, la terza di 2 e la quarta di 3).
3. **Mix Columns**: Sostituzione che altera ogni byte in una colonna in funzione di tutti i byte presenti nella stessa colonna, fornendo un ottimo livello di "mixing".
4. **Add Round Key**: Semplice operazione di XOR bit a bit tra il blocco corrente e la porzione di chiave espansa. È l'unico stadio che usa la chiave.

> [!note] L'inversione nell'AES
> L'algoritmo inizia e finisce con lo stadio *Add Round Key*. Tutte e quattro le fasi sono facilmente reversibili (tramite le funzioni InvSubBytes, Inverse Shift Rows, ecc.) ma la decrittografia non è perfettamente identica alla crittografia a causa della struttura del cifrario.

## Espansione della chiave
La chiave da 16 byte viene espansa per creare un array lineare di 44 word (176 byte). Questo garantisce materiale crittografico per l'Add Round Key iniziale (4 word) e per i successivi 10 round (4 word ciascuno).
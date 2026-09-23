---
date:
  - 2026-03-09
tags:
  - Crypto
  - Sicurezza
---
> [!info] Definizione
> La crittografia simmetrica è anche nota come crittografia convenzionale o a chiave segreta/singola. Richiede che mittente e destinatario utilizzino la stessa chiave.

## I Cinque Ingredienti Fondamentali
Un sistema di crittografia simmetrica si basa su cinque elementi:
- **Plaintext**: il messaggio o dato originale.
- **Algoritmo di crittografia**: esegue sostituzioni e trasformazioni basate sulla chiave.
- **Chiave segreta (Secret key)**: input indipendente per l'algoritmo. Due chiavi diverse produrranno due ciphertext differenti per lo stesso messaggio.
- **Ciphertext**: il messaggio mescolato (output), dipendente dal plaintext e dalla chiave.
- **Algoritmo di decrittografia**: l'inverso dell'algoritmo di crittografia, che prende in input il ciphertext e la chiave per ripristinare il plaintext.

## Dimensioni di Classificazione dei Sistemi Crittografici
I sistemi (che proteggono confidenzialità, integrità, autenticazione e non ripudio) si classificano lungo tre dimensioni:
1. **Tipo di operazioni**: 
   - *Sostituzione*: ogni elemento del plaintext viene mappato in un altro elemento.
   - *Trasposizione*: gli elementi del plaintext vengono riorganizzati. 
   - *Requisito fondamentale*: nessuna informazione deve essere persa (reversibilità).
2. **Numero di chiavi**: Simmetrica (singola chiave) o Asimmetrica (chiave pubblica/privata).
3. **Modalità di elaborazione**: a Blocchi (Block cipher) o a Flusso (Stream cipher).
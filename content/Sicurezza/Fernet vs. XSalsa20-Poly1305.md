---
date:
  - 2026-03-19
tags:
  - Sicurezza
  - Tirocinio
---
## Padding (Fusso vs Blocchi)

[[Advanced Encryption Standard (AES)|AES]] (il cuore di Fernet) è un cifrario a _blocchi_ da 16 byte. Se il tuo messaggio è di 17 byte, AES va in panico. Ha bisogno che tu aggiunga dati inutili (padding) per arrivare a 32 byte esatti. XSalsa20 è un cifrario di _flusso_: cifra bit per bit. Se il messaggio è di 17 byte, il ciphertext è esattamente di 17 byte. Nessuno spreco, nessuna gestione del padding.

## Prestazioni 

AES è notoriamente difficile da implementare in software in modo sicuro, perché è vulnerabile ad attacchi di tipo "timing" (gli hacker calcolano quanto tempo ci mette la CPU per risalire alla chiave). Per essere sicuro e veloce, AES necessita di istruzioni hardware dedicate direttamente dentro la CPU (chiamate AES-NI). **Salsa20 è nato per dominare via software**. Usa solo istruzioni base del processore (addizioni, rotazioni, XOR) che richiedono un tempo costante (constant-time). 

## Per una mole più grande di dati...

La modalità CBC (usata da Fernet) richiede che ogni blocco sia concatenato al precedente. Inoltre, l'HMAC di Fernet richiede di leggere l'intero file per validare la firma. Se hai un video da 1GB, Fernet ti costringe a riempire 1GB di RAM. Con XSalsa20-Poly1305 (come avete fatto nel CCV3) puoi fare il _chunking_: spezzi il video in pezzettini da 1MB, ognuno col suo mini-tag Poly1305, e li decifri al volo in streaming asincrono consumando solo 1MB di RAM alla volta.
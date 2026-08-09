---
date:
  - 2026-03-19
tags:
  - Sicurezza
  - Tirocinio
---

> [!NOTE] Intro
> Fernet è uno standard di crittografia simmetrica garantita. Non è un singolo algoritmo matematico, ma piuttosto una **"ricetta crittografica" preimpostata e sicura**.
> Il suo scopo principale è garantire due cose su un messaggio:
> 1. **Confidenzialità:** Nessuno può leggerlo senza la chiave.
> 2. **Integrità e Autenticità:** Nessuno può alterarlo, e siamo certi che sia stato generato da chi possiede la chiave.

Quando si chiede a Fernet di generare una chiave, ti restituisce una stringa di 32 byte codificata in Base64 (URL-safe). Questa chiave da 32 byte viene spaccata segretamente a metà dal sistema:

- **Primi 16 byte (128 bit):** Vengono usati come **Signing Key** (Chiave di Firma) per l'HMAC.
- **Secondi 16 byte (128 bit):** Vengono usati come **Encryption Key** (Chiave di Cifratura) per AES.

### Funzionamento

1. **Registra il Tempo (Timestamp):** Ottiene l'ora esatta dal sistema operativo (espressa in secondi dal 1 gennaio 1970) e la salva in un formato a 64 bit (8 byte).
2. **Genera il Seme (IV):** Genera casualmente un _Initialization Vector_ (IV) da 16 byte. Serve per garantire che cifrando due volte lo stesso identico messaggio si ottengano due risultati finali completamente diversi.
3. **Applica il Padding (PKCS7):** Fernet usa l'algoritmo AES, che lavora esclusivamente su "blocchi" rigidi da 16 byte. Se il tuo messaggio è di 20 byte, manca del testo per riempire il secondo blocco. Fernet usa lo standard PKCS7 per aggiungere byte fittizi alla fine del messaggio per fargli raggiungere un multiplo perfetto di 16.
4. **Cifra i Dati (AES-128-CBC):** Prende il messaggio paddato, l'IV generato al punto 2, e la _Encryption Key_. Fa passare tutto attraverso l'algoritmo AES a 128 bit in modalità CBC (Cipher Block Chaining). Esce il testo cifrato (Ciphertext).
5. **Costruisce la Busta e Firma (HMAC-SHA256):** Qui Fernet applica il principio aureo _Encrypt-then-MAC_ (cifra prima, autentica dopo). Prende:
	1. Un byte che indica la versione (`0x80`)
	2. Il Timestamp
	3. L'IV
	4. Il Ciphertext Incolla tutti questi pezzi in una singola stringa lunga e la dà in pasto alla funzione di hash HMAC-SHA256, usando la _Signing Key_. Il risultato è una "Firma" o "Tag" di 32 byte.
### L'Anatomia del Token Finale

Alla fine del processo di cifratura, Fernet prende tutti gli elementi, li concatena esattamente in quest'ordine e li codifica in Base64, restituendoti il "Token" finale. La struttura binaria reale è questa:
- **Version:** 1 Byte (fisso a `0x80`)
- **Timestamp:** 8 Byte (quando è stato creato)
- **IV:** 16 Byte (il seme per decifrare)
- **Ciphertext:** Grandezza variabile (il tuo messaggio cifrato)
- **HMAC:** 32 Byte (la firma a garanzia di tutto il blocco precedente)


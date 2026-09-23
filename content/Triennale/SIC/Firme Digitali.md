---
date:
  - 2026-03-02
tags:
  - Sicurezza
  - Crypto
---
# Firme Digitali

> [!summary] Definizione (NIST FIPS PUB 186-4)
> Una firma digitale è il risultato di una trasformazione crittografica dei dati che fornisce un meccanismo per verificare:
> 1. L'**autenticazione dell'origine** (il mittente è chi dice di essere).
> 2. L'**integrità dei dati** (il messaggio non è stato alterato).
> 3. Il **non ripudio** del firmatario (il mittente non può negare di aver firmato il messaggio).

In sintesi, una firma digitale è un pattern di bit dipendente dai dati, generato da un utente come funzione di un file, di un messaggio o di un altro blocco di dati.

> [!warning] Confidenzialità NON inclusa
> La firma digitale fornisce autenticazione della fonte e integrità dei dati, ma **non fornisce confidenzialità**. Il messaggio viaggia in chiaro, a meno che non venga cifrato con un processo separato.

## Standard e Algoritmi (FIPS 186-4)
Lo standard del NIST specifica l'uso di tre possibili algoritmi per le firme digitali:
* **DSA** (Digital Signature Algorithm)
* **RSA** Digital Signature Algorithm
* **ECDSA** (Elliptic Curve Digital Signature Algorithm)

## Il Processo di Firma e Verifica

1. **Generazione (Mittente - es. Bob):** * Bob applica una funzione di hash crittografica al Messaggio $M$, ottenendo un valore hash $h$.
   * Questo hash $h$ viene dato in pasto all'*Algoritmo di Generazione della Firma Digitale*, che utilizza la **chiave privata di Bob**.
   * Il risultato è la firma $S$, che viene accodata al messaggio originale $M$.
2. **Verifica (Destinatario - es. Alice):** * Alice riceve il messaggio $M$ e la firma $S$.
   * Ricalcola in autonomia l'hash del messaggio $M$ ricevuto, ottenendo un nuovo valore $h$.
   * Inserisce la firma $S$ e l'hash ricalcolato $h$ nell'*Algoritmo di Verifica della Firma*, utilizzando la **chiave pubblica di Bob**.
   * L'algoritmo restituisce un output binario: "Firma Valida" o "Firma Non Valida".

## La Vulnerabilità dello Schema Base
Questo schema presenta una debolezza fatale legata al principio stesso della crittografia asimmetrica: **la chiave pubblica è pubblica!**
* **Il vantaggio:** Chiunque può inviare la propria chiave pubblica a un altro partecipante o trasmetterla in broadcast all'intera comunità.
* **Il problema (Forging):** Chiunque può falsificare questo annuncio pubblico. Un utente malintenzionato potrebbe fingere di essere Bob e distribuire una chiave pubblica da lui generata, associandola falsamente al nome di Bob.
* **Le conseguenze:** L'impostore sarà in grado di leggere tutti i messaggi cifrati destinati a Bob e potrà usare le chiavi falsificate per autenticarsi a nome suo.
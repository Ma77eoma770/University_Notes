---
date:
  - 2026-03-02
tags:
  - Sicurezza
  - Crypto
---
# Certificati a Chiave Pubblica

> [!important] La Soluzione al Forging
> Per risolvere la vulnerabilità della distribuzione delle chiavi pubbliche, si utilizzano i **Certificati a Chiave Pubblica**, garantiti da una terza parte fidata chiamata **CA (Certification Authority)**.

Una CA è un'entità (es. agenzia governativa, istituto finanziario) considerata fidata dall'intera comunità degli utenti. Esempi italiani citati sono Aruba PEC S.p.A., InfoCert S.p.A., Poste Italiane S.p.A. e Register.it S.p.A.

Un certificato consiste in:
* Un ID utente (le informazioni del proprietario).
* La **chiave pubblica** dell'utente.
* Informazioni sulla Certification Authority (CA).
* Un periodo di validità.

L'intero blocco viene poi **firmato dalla CA**. Lo standard di formattazione universale per questi certificati è lo **X.509**.

## 1. Creazione del Certificato Firmato

1. Il software client dell'utente crea una coppia di chiavi (pubblica e privata).
2. Il client prepara un "certificato non firmato" contenente l'ID utente, la chiave pubblica e le info della CA.
3. L'utente fornisce in modo sicuro il certificato non firmato alla CA.
4. La CA crea la firma in due passaggi:
   * Usa una funzione di hash per calcolare l'hash code del certificato non firmato.
   * Genera la firma digitale utilizzando la **chiave privata della CA** e un algoritmo di generazione della firma.
5. La CA unisce la firma al certificato non firmato, creando il "Certificato Firmato".
6. La CA restituisce il certificato firmato al client, che ora può pubblicarlo o inviarlo a chiunque ne abbia bisogno.

## 2. Verifica e Utilizzo del Certificato

Quando un utente (es. Alice) vuole usare la chiave pubblica di Bob, deve prima verificare il suo certificato:
1. Alice calcola l'hash del certificato (escludendo la sezione della firma).
2. Alice verifica la firma digitale usando l'algoritmo di verifica e la **chiave pubblica della CA** (che è universalmente nota e fidata).
3. L'algoritmo restituisce "valido" o "non valido". 
4. **Se è valido:** Alice ha la garanzia assoluta che la chiave pubblica contenuta nel certificato appartiene davvero a Bob e può procedere a utilizzarla in sicurezza.
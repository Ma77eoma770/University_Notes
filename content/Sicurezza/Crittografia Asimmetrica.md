---
date:
  - 2026-03-02
tags:
  - Sicurezza
  - Crypto
---
# Crittografia Asimmetrica (A Chiave Pubblica)

Proposta pubblicamente per la prima volta da Diffie e Hellman nel 1976. A differenza dei sistemi simmetrici classici, non si basa sulla semplice sostituzione e trasposizione dei bit, ma su complesse **funzioni matematiche**.

> [!abstract] Il concetto di Asimmetria
> Il sistema impiega due chiavi separate e distinte:
> * **Chiave Pubblica ($PU$):** Viene resa di dominio pubblico affinché chiunque possa utilizzarla.
> * **Chiave Privata ($PR$):** Deve essere mantenuta rigorosamente segreta dal suo proprietario.
> 
> *Nota: Essendo le chiavi asimmetriche, è sempre necessaria una qualche forma di protocollo per gestire la distribuzione sicura delle chiavi.*

## I Due Usi Fondamentali della Crittografia a Chiave Pubblica

### 1. Orientata alla Confidenzialità
L'utente mittente (es. Bob) cifra i dati utilizzando la *Chiave Pubblica* del destinatario (Alice). L'unico modo per decifrare il testo cifrato risultante è utilizzare la *Chiave Privata* corrispondente, in possesso solo di Alice.
* **Formula:** $Y = E[PU_a, X]$ $\rightarrow$ $X = D[PR_a, Y]$


### 2. Orientata all'Autenticazione / Integrità (Firma Digitale)
L'utente (Bob) cifra i dati utilizzando la *propria Chiave Privata*. Chiunque possieda la chiave pubblica di Bob sarà in grado di decifrare il messaggio. Dato che solo Bob possiede quella chiave privata, la corretta decifratura garantisce matematicamente che il messaggio proviene da lui e non è stato alterato.
* **Formula:** $Y = E[PR_b, X]$ $\rightarrow$ $X = D[PU_b, Y]$


[Image of Public-Key Encryption with private key]


## Requisiti di base (Diffie e Hellman, 1976)
Un algoritmo crittografico a chiave pubblica per essere efficace deve soddisfare i seguenti requisiti:
1. Deve essere computazionalmente **facile** per un utente generare una coppia di chiavi ($PU$, $PR$).
2. Deve essere computazionalmente **facile** per un mittente, conoscendo il messaggio $M$ e la chiave pubblica del ricevente, generare il testo cifrato $C = E(PU, M)$.
3. Deve essere computazionalmente **facile** per il ricevitore decifrare il messaggio usando la sua chiave privata: $M = D(PR, C)$.
4. Deve essere computazionalmente **infattibile** per un avversario, pur conoscendo la chiave pubblica $PU$, determinare la chiave privata $PR$.
5. Deve essere computazionalmente **infattibile** per un avversario recuperare il messaggio originale $M$ conoscendo unicamente la chiave pubblica $PU$ e il testo cifrato $C$.
6. *Requisito utile ma non strettamente necessario in tutti i sistemi:* Entrambe le chiavi (pubblica e privata) possono essere usate in modo intercambiabile per cifrare e decifrare. $M = D[PU, E(PR, M)] = D[PR, E(PU, M)]$.

## Principali Algoritmi Asimmetrici e Applicazioni

| Algoritmo | Firma Digitale | Distribuzione Chiavi | Cifratura Chiavi Segrete | Note |
| :--- | :--- | :--- | :--- | :--- |
| **RSA** (1977) | Sì | Sì | Sì | Sviluppato da Rivest, Shamir e Adleman. È l'approccio più ampiamente accettato e implementato. È un cifrario a blocchi in cui sia il testo in chiaro che quello cifrato sono rappresentati da numeri interi compresi tra 0 e $n-1$. |
| **Diffie-Hellman** (1976) | No | Sì | No | Permette a due utenti di generare in modo sicuro un segreto condiviso che può essere poi usato come chiave per una successiva cifratura simmetrica. È strettamente limitato allo scambio di chiavi. |
| **DSS (Digital Signature Standard)** | Sì | No | No | Fornisce esclusivamente una funzione di firma digitale accoppiata all'algoritmo di hash SHA-1. Non può in alcun modo essere usato per cifrare documenti o scambiare chiavi. |
| **Elliptic Curve Cryptography (ECC)** | Sì | Sì | Sì | Basato su curve ellittiche, offre una sicurezza del tutto paragonabile a quella dell'RSA, ma con il vantaggio di utilizzare chiavi di dimensioni drasticamente inferiori. |

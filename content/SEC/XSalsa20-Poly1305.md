---
date:
  - 2026-03-19
tags:
  - Tirocinio
  - Sicurezza
---
> [!NOTE] Intro
> Per capire come funziona XSalsa20-Poly1305 dobbiamo dividere i due attori:
> 1. XSalsa20 è un **cifrario di flusso**.
> 2. Poly1305 è un **Message Authentication Code (MAC)**

## Salsa20

A differenza di algoritmi più vecchi che prendono i tuoi dati, li spezzano in blocchi rigidi (es. 16 byte alla volta) e li mescolano, XSalsa20 è un **cifrario di flusso** (stream cipher).
### Funzionamento 

L'algoritmo prende la tua Master Key (32 byte) (possibilmente derivata da [[Argon2Id]]) e un **Nonce** (un numero usato una volta sola). Poi, usando un complesso sistema di rotazioni, somme logiche e spostamenti di bit sui registri del processore, inizia a "macinare" questi due elementi generando una sequenza infinita di bit pseudo-casuali. Questa sequenza si chiama **Keystream** (flusso di chiave).

### La X in XSalsa20

Salsa20 originale usava un Nonce da 8 byte. Se generi nonce a caso, dopo un po' potresti generare due volte lo stesso numero (collisione). Se usi lo stesso Nonce e la stessa Chiave due volte in un cifrario di flusso, il sistema crolla ed è facilmente craccabile. La "X" sta per _eXtended_: **XSalsa20 usa un Nonce enorme da 24 byte** (192 bit). La probabilità di generare due volte lo stesso Nonce da 24 byte in modo casuale è paragonabile a quella di vincere alla lotteria ogni giorno per millenni. Puoi generarli a caso (tramite `os.urandom`) senza doverti preoccupare di tenere un contatore.

## Poly1305

Nascondere i dati (XSalsa20) non basta. Se un hacker intercetta il tuo testo cifrato e capovolge un bit a caso (da 0 a 1), quel bit modificato farà cambiare una lettera nel messaggio decifrato. Potrebbe cambiare un "Paga 100€" in "Paga 900€". Serve un **MAC** (Message Authentication Code).

### Funzionamento

Prima di cominciare a cifrare il messaggio, XSalsa20 genera 32 byte speciali di Keystream e li regala a Poly1305. Questa diventa la chiave segreta "usa e getta" per autenticare quello specifico messaggio.

> [!hint] La parte matematica...
> Poly1305 prende ogni singolo blocco del tuo testo cifrato e lo usa come coefficiente per valutare un polinomio su un campo finito specifico: $GF(2^{130}-5)$.

Questo polinomio è come una formula matematica immensa e delicatissima. Poly1305 fa passare tutto il testo cifrato in questa formula e alla fine sputa un **Tag di 16 byte**. Se un hacker altera anche un solo, insignificante bit nel testo cifrato, il risultato del polinomio sballa completamente. Quando il tuo sistema riceve il messaggio, ricalcola il polinomio: se il Tag risultante non è _identico_ al 100% a quello allegato al messaggio, solleva l'eccezione `CryptoError` e si rifiuta di decifrare anche solo un byte.

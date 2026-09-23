---
date:
  - 2026-03-02
tags:
  - Crypto
  - Sicurezza
---
# Autenticazione dei Messaggi e MAC

> [!important] Confidenzialità vs Autenticazione
> Mentre la cifratura semplice protegge dalle intercettazioni (attacchi passivi), l'autenticazione è fondamentale per proteggersi dagli **attacchi attivi**, come la falsificazione di dati e transazioni.

L'autenticazione dei messaggi verifica tre elementi essenziali:
1. I contenuti non sono stati alterati.
2. Il messaggio proviene da una fonte autentica.
3. Il messaggio è tempestivo e nella sequenza corretta.

## Autenticazione senza Confidenzialità

L'autenticazione è spesso fornita come funzione separata dalla cifratura. Ci sono casi specifici in cui è preferibile autenticare un messaggio senza cifrarne il contenuto:
* Quando lo stesso messaggio viene inviato in **broadcast** a molteplici destinazioni.
* Quando un sistema ricevente ha un **carico di lavoro pesante** e non può permettersi i tempi di elaborazione necessari per decifrare tutti i messaggi in entrata.
* Quando si desidera autenticare un **programma informatico** che deve rimanere in testo in chiaro per essere eseguito o ispezionato agevolmente.

## MAC (Message Authentication Code)

L'algoritmo MAC richiede che mittente e ricevente condividano una chiave segreta ($K$). La funzione MAC elabora il messaggio e la chiave per produrre un blocco di dati di dimensione fissa, chiamato *tag* di autenticazione, che viene aggiunto al messaggio stesso.

> [!info] Le Assicurazioni fornite dal MAC
> Il sistema si basa sull'**assunzione** che solo mittente e destinatario conoscano l'identità della chiave segreta.
> Se il codice calcolato dal ricevente combacia con il MAC ricevuto, si ha la certezza matematica che:
> 1. **Il messaggio non è stato alterato:** un attaccante che modifica il messaggio non può calcolare il nuovo MAC corrispondente senza possedere la chiave.
> 2. **Il mittente è autentico:** nessuno al di fuori del mittente legittimo avrebbe potuto preparare un messaggio con un MAC valido.
> 3. **La sequenza è corretta:** se il messaggio include un numero di sequenza (come avviene con protocolli quali X.25, HDLC e TCP), il ricevente è assicurato che la sequenza sia corretta, poiché un attaccante non può alterare con successo il numero di sequenza senza invalidare il MAC.
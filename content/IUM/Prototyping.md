---
date:
  - 2025-10-19
tags:
  - IUM
  - Prototyping
---
Un prototipo è un **modello di sistema interattivo** che simula **alcune caratteristiche**, aspetti o funzionalità **ma non tutte**. Un prototipo deve essere realistico solo per gli aspetti che ci interessano in quel momento e deve essere valutato con le stesse condizioni dell’interfaccia finale. Per progettare la UI per un determinato storyboards bisogna immaginare l’interazione che l’utente avrà con l’interfaccia considerando anche gli strumenti disponibili, come hardware, widget e stili di interazione. L’obiettivo del prototyping è di generare nuove idee e testarle con gli utenti con tecniche diverse in base allo stage della progettazione e dalle persone che dovranno testarlo: utenti, clienti, manager,. . . . Non bisogna pensare alla UI prima di concentrarsi sul task che questa deve svolgere.

> [!hint] Prima task poi UI

Ci sono diversi **approcci** al prototyping:
- **Throw away**: conoscenza utilizzata, prototipo scartato
- **Incrementale**: la varie funzioni vengono aggiunte una alla volta al prototipo
- **Evolutivo**: un prototipo serve come base per il successivo, che lo migliora

La prototipazione è un ciclo in cui finché un prototipo non viene valutato positivamente, si
torna a riprogettarlo. Ad ogni iterazione bisogna capire cosa è sbagliato (le cause non solo i
sintomi), ipotizzare come migliorare ma bisogna avere anche un buon punto di partenza (non
si ripropone un’idea iniziale scartata).

I principali **problemi** del prototyping possono essere:
- **Tempo**: fare prototipi richiede tempo e se uno viene scartato sembra tempo buttato
- **Pianificazione**: è difficile pianificare il processo di design e i costi durante il prototyping
- **Caratteristiche** non funzionali **sacrificate**: sicurezza, affidabilità, tempo di risposta, ...
## Tipi di prototipi
### Low Fidelity Prototypes

I Low Fidelity Prototypes sono dei **prototipi disegnati su carta** che modellano la UI, rappresentando finestre, menu, caselle di testo. I paper prototype sono utili perché facili da realizzare, da cambiare durante i test con gli utenti e non richiedono nessuna skill di programmazione.

Per testare un paper prototype il gruppo di designers deve coprire **tre ruoli precisi**:
1. **Computer**: simula il computer e cambia schermata all'occorrenza senza dare nessun feedback che il computer non darebbe.
2. **Facilitator**: presenta l’interfaccia all'utente e gli spiega il task che deve compiere, facendo domande per capire cosa sta facendo e lo tiene sui binari del task da eseguire.
3. **Osservatore**: sta zitto e prende appunti.

I paper prototype **permettono di capire se l’interfaccia fa quello che deve**, se è facilmente
navigabile, se si capiscono le etichette dei pulsanti e cosa è necessario che ci sia sullo schermo.

### Medium Fidelity Prototypes

I Medium Fidelity Prototypes sono **prototipi fatti al computer** che renderizzano la UI e **accettano degli input switchando pagina**. Sono ancora composi da un design semplice, composto da poche pagine predefinite con informazioni statiche. Questo causa una ”ricerca” delle poche cose che sono veramente cliccabili nell'interfaccia, però **aiuta a capire se la UI è facilmente capibile e come è l’andamento delle schermate**.

### High Fidelity Prototypes

Gli High Fidelity Prototypes **sono vere e proprie applicazioni con layout**, grafica e colori che rappresentano una versione finale della progettazione. Sono **molto più costose** da realizzare e si concentrano maggiormente sulla grafica che sull'interazione.

Permettono di **capire**:
- Se i **colori, font** e altri elementi sono scelti bene.
- Se i **pulsanti** sono abbastanza grandi e posizionati nel punto giusto.
- Se l’utente nota i **feedback dell’interfaccia**, come cambi di cursore.

## Tecnica "Mago di Oz"

La tecnica ”Mago di Oz” **viene usata per testare un’applicazione** che è veramente finita, cioè con UI e algoritmi finiti, ma senza scrivere effettivamente il codice dietro. Questa tecnica si basa sull'avere una **persona che simula la macchina**.

Per applicarla bisogna creare un’interfaccia che implementi parte del sistema ma permetta al
”**mago**” di eseguire le azioni al posto della macchina tramite un’interfaccia ”dietro”. Bisogna anche definire quando dovrebbe rispondere il ”mago” e come secondo l’algoritmo, questo può essere difficile perché bisogna tenere conto delle limitazioni del sistema e **emularlo molto velocemente**. Inoltre **si potrebbe essere troppo ottimisti** rispetto al sistema (un sistema che non sbaglia mai).

Questa tecnica ha diversi **vantaggi**:
- Più **veloce ed economica** della maggior parte dei prototipi interattivi.
- Più “**reale**” dei paper prototype.
- Facile fare variazioni.
- Aiuta a **identificare bug** e problemi con la progettazione attuale.
- Permette di **immaginare applicazioni difficili** da realizzare.
- Fare il ”**mago**” permette di capire meglio i **requisiti degli algoritmi**.

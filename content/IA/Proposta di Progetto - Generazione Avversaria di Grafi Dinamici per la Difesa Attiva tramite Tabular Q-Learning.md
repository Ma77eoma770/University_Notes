---
date: 2026-06-25
tags:
  - IA
  - Progetto
---
## Il contesto e il problema

Oggi il traffico web è dominato dai bot: scraper e crawler che setacciano costantemente la rete per mappare siti ed esfiltrare dati. Per contrastarli, vengono spesso usati degli honeypot (trappole che simulano vulnerabilità o dati preziosi). Questo progetto si concentrerà sui Tarpit adattivi, sistemi pensati per rallentare o bloccare i bot autonomi.

Se isoliamo il web scraping dai dettagli tecnici dei protocolli HTTP o dello sviluppo web, ci accorgiamo che si tratta fondamentalmente di un problema di ricerca su un grafo diretto. L'attaccante esplora questo grafo cercando di raccogliere quanti più nodi utili nel minor tempo possibile.

L'idea del progetto è studiare come un Agente Difensore possa generare la topologia di questo grafo in tempo reale. Invece di bloccare l'attaccante con un muro (soluzione che lo farebbe insospettire, portandolo a cambiare bersaglio), il difensore crea un labirinto infinito. L'obiettivo è intrappolarlo e portarlo a consumare tutta la memoria o le risorse computazionali, ritardando il più possibile le sue operazioni senza che se ne accorga (studiando ogni volta il suo comportamento).

## Funzionamento ad alto livello

Il sistema metterà a confronto le strategie dei due agenti:

1. **L'Attaccante:** Utilizzerà tecniche classiche di esplorazione degli spazi degli stati con controllo dei nodi già visitati. Verrà usata sia la ricerca non informata (come la _Breadth-First Search_ - BFS) sia quella informata (come A* o Greedy Best-First).
2. **Il Difensore:** Sfrutterà il Tabular Q-Learning (Reinforcement Learning) e sarà modellato come un Processo Decisionale di Markov (MDP) discreto:
    - **Stato:** Un'astrazione della situazione attuale (es. profondità del ramo, fattore di branching, azioni recenti dell'attaccante e parole chiave intercettate).
    - **Azione:** Il tipo di "sottografo" da agganciare alla struttura corrente per ingannare il bot (es. un vicolo cieco molto profondo, un loop infinito mascherato o un nodo esca con un'euristica fittizia molto invitante).
    - **Reward (Ricompensa):** Sarà proporzionale al tempo e alla memoria sprecati dall'attaccante prima che si accorga dell'inganno ed effettui il backtracking. Nel caso in cui il difensore esagererà creando strutture così anomale da far insospettire il bot e spingerlo ad abbandonare subito il ramo, verrà applicata una penalità.

## Elementi di novità del progetto

- **Ambienti avversari e dinamici:** A differenza dei problemi statici visti a lezione (come i classici CSP o il pathfinding), qui l'ambiente reagisce attivamente per mandare in crisi l'algoritmo di ricerca.
- **Le vulnerabilità degli algoritmi ottimi:** Dimostreremo come le garanzie di ottimalità e completezza di algoritmi famosi come $A^*$ si trasformino in punti deboli se l'ambiente manipola le euristiche in modo ingannevole.
- **Generazione topologica tramite RL:** Applicheremo il Reinforcement Learning per creare strutture logiche in tempo reale, uscendo dal classico Machine Learning supervisionato.

## Architettura del Software

Per concentrarci sulla logica algoritmica ed evitare i colli di bottiglia computazionali del Deep Learning, il simulatore sarà scritto in Python puro, senza framework pesanti (come PyTorch). Il codice sarà diviso in tre moduli principali:

- **Ambiente (Engine):** Gestisce il grafo dinamico usando la libreria `NetworkX`, scandisce i turni di gioco del bot e del difensore, e raccoglie le metriche (tempo di esecuzione e memoria virtuale allocata).
- **Agente Attaccante:** Il modulo che implementa le varie strategie di navigazione sul grafo.
- **Agente Difensore:** Il nucleo logico che aggiorna la Q-Table durante le simulazioni.

Il risultato finale consisterà in un'analisi dei grafici finali: metteremo a confronto il degrado delle prestazioni del bot quando affronta il difensore "intelligente" (addestrato con Q-Learning) rispetto a un difensore base che genera trappole in modo puramente casuale (o non ne genera affatto).

## Riferimenti bibliografici iniziali

- R. S. Sutton, A. G. Barto, (2018). _Reinforcement Learning: An Introduction_.
- Hayatle, Otrok, and Youssef, 2013. _A Markov Decision Process Model for High Interaction Honeypots_. Information Security Journal.
- Dowling, Schukat and Barrett, 2018. _Using Reinforcement Learning to Conceal Honeypot Functionality_.
- Wagener, State, Dulaunoy and Engel, 2009. _Self Adaptive High Interaction Honeypots Driven by Game Theory_.
- Krawetz, 2004. _Anti-honeypot technology_; 
- Holz, Raynal, 2005. _Detecting honeypots and other suspicious environments_.
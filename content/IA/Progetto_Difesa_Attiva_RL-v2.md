---
title: "Generazione Avversaria di Grafi Dinamici per la Difesa Attiva tramite Tabular Q-Learning"
tags: [ "#formalizzazione", "#architettura", "#reinforcement-learning", "#q-learning", "#difesa-attiva", "#pomdp", "#game-theory" ]
date: "2026-07-27"
version: "2.0"
---

# 1. Introduzione e Motivazione Teorica

Negli scenari moderni di sicurezza informatica, il traffico web è dominato da agenti autonomi (bot, scraper, crawler) il cui scopo è l'esfiltrazione sistematica di dati o la mappatura di vulnerabilità. Le difese tradizionali, inclusi gli honeypot statici, mostrano limiti evidenti: gli attaccanti hanno sviluppato contromisure sofisticate per l'identificazione degli ambienti civetta [Krawetz, 2004], analizzando discrepanze hardware, latenze o anomalie nel file system [Holz e Raynal, 2005]. 

Per superare queste limitazioni, la letteratura recente suggerisce l'impiego di *Honeypot Adattivi*, capaci di reagire dinamicamente per prolungare l'interazione con l'attaccante e ritardarne il rilevamento [Wagener et al., 2009]. L'obiettivo non è semplicemente bloccare il bot, bensì massimizzare il consumo delle sue risorse computazionali e di memoria. 
Sfruttando concetti di apprendimento per rinforzo (Reinforcement Learning), è possibile generare un *Tarpit* strutturato come un labirinto infinito. 

> [!info] Novità Accademica del Progetto
> Mentre i lavori precedenti in letteratura applicano il Reinforcement Learning a comandi del sistema operativo o chiamate di sistema (con azioni difensive quali *Allow, Block, Substitute, Insult*) [Dowling et al., 2018] [Wagener et al., 2009], questo progetto propone una traslazione innovativa del dominio di intervento: lo spazio delle azioni non opera a livello di shell OS, ma si manifesta come **perturbazione topologica di un grafo** (es. introduzione di *Bait, Loop, Dead-end*).

# 2. Formalizzazione Formale del Problema

Il problema di intrappolamento del bot viene modellato come un gioco avversario asimmetrico. Astraendo il livello di rete, l'attacco è ridotto a una ricerca euristica su un grafo diretto $\mathcal{G}_{t} = (V_{t}, E_{t})$, dove $V_t$ rappresenta l'insieme dei nodi (stati dell'applicazione/sito) e $E_t$ le transizioni possibili al tempo $t$.

### 2.1 Modello dell'Attaccante
Guidato dall'algoritmo $A^*$, l'attaccante valuta costantemente la coerenza del grafo esplorato. Seguendo il framework teorico introdotto da [Wagener et al., 2009], le reazioni dell'attaccante agli ostacoli generati dal sistema difensivo sono modellate probabilisticamente. Mappiamo le azioni del nostro bot su tale modello:
- **Passo normale / Riprova**: $Pr(Retry)$
- **Backtrack**: Mappato su $Pr(Alternative)$, ovvero la ricerca di percorsi alternativi.
- **Give Up (Fuga)**: Mappato su $Pr(Quit)$, l'abbandono del sistema.

Queste probabilità sono regolate dal parametro interno di *patience* del bot. Affinché il modello sia formalmente valido, deve rispettare il vincolo matematico definito in [Wagener et al., 2009]:
$$ Pr(Retry) + Pr(Alternative) + Pr(Quit) = 1 $$

### 2.2 Processo Decisionale di Markov Parzialmente Osservabile (POMDP)
> [!abstract] Definizione 1: Formulazione POMDP
> A causa dell'impossibilità del Difensore di conoscere con esattezza lo stato interno dell'Attaccante (in particolare la pazienza reale $p$), il sistema è formalizzato rigorosamente come un POMDP [Hayatle et al., 2013], definito dalla tupla $\mathcal{P} = \langle \mathcal{S}, \mathcal{A}, \mathcal{T}, \mathcal{R}, \Omega, \mathcal{O}, \gamma \rangle$.

- **Spazio degli Stati Reali ($\mathcal{S}$)**: Un singolo stato $s = \langle p, d, \tau \rangle$, con pazienza reale $p$, distanza dal target $d$, e presenza di trappole $\tau$.
- **Spazio delle Azioni ($\mathcal{A}$)**: Modifiche topologiche. $\mathcal{A} = \{ a_{bait}, a_{loop}, a_{deadend}, a_{wait} \}$
- **Belief State (Stato di Credenza, $b(s)$)**: Poiché lo stato $s$ non è pienamente osservabile, il difensore mantiene una distribuzione di probabilità sugli stati reali, definita $b(s)$. Secondo [Hayatle et al., 2013], per gestire l'incertezza, il Belief State viene aggiornato dinamicamente a ogni turno mediante il Teorema di Bayes:
  $$ b(s)_{t+1} = \frac{Pr(o \mid s, b_t, a) \cdot Pr(s \mid b_t, a)}{Pr(o \mid b_t, a)} $$
- **Spazio delle Osservazioni ($\Omega$) e Funzione $\mathcal{O}$**: L'Ambiente osserva un *bucket* discreto $o_p$ e restituisce l'osservazione $\tilde{s} \in \Omega$ che aggiorna il belief state. 
- **Funzione di Ricompensa ($\mathcal{R}$)**:
  $$ \mathcal{R}(s, a) = w_1 \cdot \Delta_{steps} - w_2 \cdot c(a) - \mathcal{P}_{escape} $$
  Massimizza lo spreco di risorse ($\Delta_{steps}$), sottraendo i costi d'azione $c(a)$ e penalizzando fortemente l'abbandono prematuro $\mathcal{P}_{escape}$.

# 3. Progettazione Architetturale ad Alto Livello

L'architettura del software si basa sulla separazione logica degli agenti e sull'astrazione dell'ambiente.

1. **Modulo Ambiente ([[Engine]])**
   - Gestisce il grafo dinamico tramite `NetworkX`.
   - Funge da arbitro del POMDP: applica $\mathcal{T}$, osserva le interazioni ed espone il *Belief State* approssimato o l'osservazione $\tilde{s}$ per l'agente difensivo.
2. **Agente Attaccante ([[Malicious Bot]])**
   - Implementa l'algoritmo di pathfinding $A^*$ vincolato dalla `patience`.
   - Genera transizioni stocastiche regolate dalle distribuzioni $Pr(Retry)$, $Pr(Alternative)$ e $Pr(Quit)$.
3. **Agente Difensore ([[Active Tarpit]])**
   - Interagisce con lo stato approssimato per decidere in tempo reale come alterare il grafo $\mathcal{G}_t$.
   - **Nota Algoritmica**: Mentre la letteratura di riferimento sui Tarpit adattivi utilizza l'algoritmo on-policy **SARSA** per valutare le azioni [Dowling et al., 2018] [Wagener et al., 2009], questo progetto implementa il **Tabular Q-Learning**. Trattandosi di un algoritmo *off-policy*, consente di disaccoppiare la fase di esplorazione stocastica ($\epsilon$-greedy) dalla fase di massimizzazione del reward mirato, rendendo l'apprendimento più robusto rispetto a policy sub-ottimali intraprese dall'attaccante.

# 4. Flusso Logico e Aggiornamento Q-Learning

Durante l'addestramento, il ciclo di vita della simulazione segue le seguenti fasi a ogni turno $t$:

1. **Estrazione Osservazione**: L'ambiente stima lo stato e aggiorna la percezione del sistema.
2. **Decisione Off-Policy**: Il Difensore sceglie $a_t \in \mathcal{A}$ tramite policy $\epsilon$-greedy sulla Q-Table corrente. L'azione altera la topologia $\mathcal{G}_t \rightarrow \mathcal{G}_{t+1}$.
3. **Reazione**: L'Attaccante calcola le nuove euristiche, adatta la sua patience e si muove nel grafo consumando risorse (generando il reward $r_t$).
4. **Apprendimento (Equazione di Bellman per Q-Learning)**:
   A differenza dell'aggiornamento SARSA usato in [Dowling et al., 2018], la matrice dei valori di stato-azione si aggiorna ipotizzando l'azione ottima futura:
   $$ Q(\tilde{s}_t, a_t) \leftarrow Q(\tilde{s}_t, a_t) + \alpha \left[ r_t + \gamma \max_{a'} Q(\tilde{s}_{t+1}, a') - Q(\tilde{s}_t, a_t) \right] $$

# 5. Riferimenti Bibliografici

- [Dowling et al., 2018] S. Dowling, M. Schukat, and E. Barrett, "Improving adaptive honeypot functionality with efficient reinforcement learning parameters for automated malware", *Journal of Cyber Security Technology*, 2018. (Utilizzato per giustificare l'efficacia del RL sui malware automatizzati, pur divergendo sull'algoritmo di base passando da SARSA a Q-Learning e sul dominio applicativo).
- [Hayatle et al., 2013] O. Hayatle, H. Otrok, and A. Youssef, "A Markov Decision Process Model for High Interaction Honeypots", *Information Security Journal*, 2013. (Riferimento primario per la modellazione dell'incertezza tramite POMDP e la regola bayesiana del Belief State).
- [Holz e Raynal, 2005] T. Holz and F. Raynal, "Detecting honeypots and other suspicious environments", *Proceedings of the 2005 IEEE Workshop on Information Assurance and Security*, 2005. (Analisi delle firme degli honeypot tradizionali e limitazioni delle difese statiche).
- [Krawetz, 2004] N. Krawetz, "Anti-honeypot technology", *IEEE Security & Privacy*, 2004. (Evoluzione delle tecniche di evasione ed esfiltrazione).
- [Wagener et al., 2009] G. Wagener, R. State, A. Dulaunoy, and T. Engel, "Self Adaptive High Interaction Honeypots Driven by Game Theory", *Symposium on Stabilization, Safety, and Security*, 2009. (Framework formale probabilistico $Pr(Retry, Alternative, Quit)$ per le reazioni di un attaccante umano/bot all'interno della teoria dei giochi).

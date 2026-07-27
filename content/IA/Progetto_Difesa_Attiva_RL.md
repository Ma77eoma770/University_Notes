---
title: "Generazione Avversaria di Grafi Dinamici per la Difesa Attiva tramite Tabular Q-Learning"
tags: [ "#formalizzazione", "#architettura", "#reinforcement-learning", "#q-learning", "#difesa-attiva" ]
date: "2026-07-27"
version: "1.0"
---

# 1. Introduzione e Motivazione Teorica

Negli scenari moderni di sicurezza informatica, il traffico web è dominato da agenti autonomi (bot, scraper, crawler) il cui scopo è l'esfiltrazione sistematica di dati o la mappatura di vulnerabilità. Le difese tradizionali, inclusi gli honeypot statici, mostrano limiti evidenti: gli attaccanti hanno sviluppato contromisure sofisticate per l'identificazione degli ambienti civetta [Krawetz, 2004], analizzando discrepanze hardware, latenze o anomalie nel file system [Holz e Raynal, 2005]. 

Per superare queste limitazioni, la letteratura recente suggerisce l'impiego di *Honeypot Adattivi*, capaci di reagire dinamicamente per prolungare l'interazione con l'attaccante e ritardarne il rilevamento [Wagener et al., 2009]. L'obiettivo non è semplicemente bloccare il bot, bensì massimizzare il consumo delle sue risorse computazionali e di memoria. 
Sfruttando concetti di apprendimento per rinforzo (Reinforcement Learning), è possibile generare un *Tarpit* strutturato come un labirinto infinito. Questa architettura difensiva attiva genera dinamicamente topologie ingannevoli (es. loop, vicoli ciechi) che contrastano algoritmi di esplorazione euristica come l'algoritmo $A^*$ utilizzato dall'attaccante. Come dimostrato nell'ambito dei botnet automatizzati, limitare lo spazio delle azioni del difensore a specifici interventi mirati ottimizza drasticamente i tempi di convergenza della policy difensiva [Dowling et al., 2018].

# 2. Formalizzazione Formale del Problema

Il problema di intrappolamento del bot viene modellato come un gioco avversario asimmetrico. Astraendo il livello di rete, l'attacco è ridotto a una ricerca euristica su un grafo diretto $\mathcal{G}_{t} = (V_{t}, E_{t})$, dove $V_t$ rappresenta l'insieme dei nodi (stati dell'applicazione/sito) e $E_t$ le transizioni possibili al tempo $t$.

> [!abstract] Definizione 1: Processo Decisionale di Markov Parzialmente Osservabile (POMDP)
> A causa dell'impossibilità del Difensore di conoscere con esattezza lo stato interno dell'Attaccante (in particolare il suo livello di "pazienza"), il sistema è formalizzato come un POMDP [Hayatle et al., 2013], definito dalla tupla $\mathcal{P} = \langle \mathcal{S}, \mathcal{A}, \mathcal{T}, \mathcal{R}, \Omega, \mathcal{O}, \gamma \rangle$.

- **Spazio degli Stati Reali ($\mathcal{S}$)**: Un singolo stato $s \in \mathcal{S}$ è descritto dal vettore $s = \langle p, d, \tau \rangle$, dove:
  - $p \in [0, P_{max}]$ è la *patience* reale dell'attaccante.
  - $d \in \mathbb{N}$ è la distanza effettiva tra la posizione dell'attaccante e il nodo target.
  - $\tau \in \{0, 1\}^k$ è un vettore booleano che indica la presenza di trappole nei nodi adiacenti.

- **Spazio delle Azioni ($\mathcal{A}$)**: Le perturbazioni topologiche attuabili dal difensore. 
  $\mathcal{A} = \{ a_{bait}, a_{loop}, a_{deadend}, a_{wait} \}$

- **Funzione di Transizione ($\mathcal{T}$)**: Definisce la dinamica dell'ambiente, $\mathcal{T}(s, a, s') = \mathbb{P}(s' \mid s, a)$. Essa ingloba la reazione dell'algoritmo $A^*$ dell'attaccante, che aggiorna la sua frontiera di esplorazione basandosi sulla funzione costo $f(n) = g(n) + h(n)$.

- **Spazio delle Osservazioni ($\Omega$) e Funzione $\mathcal{O}$**: L'Ambiente mappa la pazienza continua $p$ in *bucket* discreti osservabili $o_p$, fornendo al Difensore uno stato stimato $\tilde{s} = \langle o_p, \tilde{d}, \tau \rangle \in \Omega$. 

- **Funzione Obiettivo e Reward ($\mathcal{R}$)**: Il Difensore deve massimizzare il numero di nodi espansi dall'Attaccante. La funzione di ricompensa è definita come:
  $$ \mathcal{R}(s, a) = w_1 \cdot \Delta_{steps} - w_2 \cdot c(a) - \mathcal{P}_{escape} $$
  Dove $\Delta_{steps}$ è il tempo perso dall'attaccante, $c(a)$ è il costo vivo dell'azione $a$ (es. $c(a_{deadend}) > c(a_{wait})$), e $\mathcal{P}_{escape}$ è una forte penalità applicata se l'attaccante azzera la sua *patience* e abbandona prematuramente il grafo. Questo schema di learning penalizzato è cruciale per mascherare l'Honeypot [Dowling et al., 2018].

# 3. Progettazione Architetturale ad Alto Livello

L'architettura del software si basa sulla separazione logica degli agenti (Attaccante e Difensore) e sull'astrazione dell'ambiente, mappando direttamente gli elementi formali in moduli computazionali.

1. **Modulo Ambiente (Engine)**
   - Costruito sfruttando la libreria `NetworkX`, gestisce il grafo dinamico $\mathcal{G}_t$.
   - Funge da arbitro del POMDP: ad ogni step temporale, raccoglie le metriche, calcola le funzioni euristica $h(n)$, applica la funzione di transizione $\mathcal{T}$ simulando la dinamica dell'esplorazione, ed estrae l'osservazione $\tilde{s} \in \Omega$ per il Difensore.
2. **Agente Attaccante (Malicious Bot)**
   - Implementa l'algoritmo di pathfinding ottimo $A^*$ ma con vincoli psicologici: la variabile `patience` $p$.
   - Adatta dinamicamente il proprio comportamento: un decremento incoerente di $h(n)$ (nodi con punteggi esageratamente positivi) riduce $p$. Al raggiungimento di $p=0$, innesca strategie di *Backtracking* o termina l'attacco (Give Up).
3. **Agente Difensore (Active Tarpit via Tabular Q-Learning)**
   - Interagisce solo con lo stato osservabile $\tilde{s}$.
   - Non necessita di architetture Deep Learning pesanti; sfrutta una *Q-Table* bidimensionale (Stato $\times$ Azione) che garantisce rapidità e trasparenza computazionale. 

# 4. Flusso Logico e Dettagli Implementativi

Il ciclo di vita della simulazione è strutturato in una serie di episodi di addestramento. Durante ogni turno $t$, la logica di apprendimento segue la classica formalizzazione dell'equazione di aggiornamento di Bellman:

1. **Osservazione**: L'ambiente innesca il rilevamento, aggregando la pazienza residua stimata e il contesto locale in $\tilde{s}_t$.
2. **Decisione ($\epsilon$-greedy)**: Il Difensore consulta la Q-Table $Q(\tilde{s}_t, a)$ ed estrae l'azione ottima con probabilità $1-\epsilon$, oppure un'azione casuale (Exploration) con probabilità $\epsilon$. L'azione $a_t \in \mathcal{A}$ selezionata modifica $\mathcal{G}_t \rightarrow \mathcal{G}_{t+1}$.
3. **Reazione**: L'Attaccante esegue un passo nel nuovo grafo $\mathcal{G}_{t+1}$, aggiornando l'open set di $A^*$. L'ambiente ne decreta il consumo computazionale e determina $\tilde{s}_{t+1}$ e la reward locale $r_{t}$.
4. **Apprendimento (Update Bellmaniano)**:
   $$ Q(\tilde{s}_t, a_t) \leftarrow Q(\tilde{s}_t, a_t) + \alpha \left[ r_t + \gamma \max_{a'} Q(\tilde{s}_{t+1}, a') - Q(\tilde{s}_t, a_t) \right] $$
   Dove $\alpha$ è il *learning rate* e $\gamma$ il fattore di sconto [Hayatle et al., 2013].

La validazione dell'efficacia avverrà confrontando il degrado prestazionale (allocazione di memoria virtuale e tempo (passi/turni) d'esecuzione sprecati) dell'Attaccante affrontando il Difensore addestrato rispetto a un modello randomico o statico.

# 5. Riferimenti Bibliografici

- [Dowling et al., 2018] S. Dowling, M. Schukat, and E. Barrett, "Improving adaptive honeypot functionality with efficient reinforcement learning parameters for automated malware", *Journal of Cyber Security Technology*, 2018.
- [Hayatle et al., 2013] O. Hayatle, H. Otrok, and A. Youssef, "A Markov Decision Process Model for High Interaction Honeypots", *Information Security Journal*, 2013.
- [Holz e Raynal, 2005] T. Holz and F. Raynal, "Detecting honeypots and other suspicious environments", *Proceedings of the 2005 IEEE Workshop on Information Assurance and Security*, 2005.
- [Krawetz, 2004] N. Krawetz, "Anti-honeypot technology", *IEEE Security & Privacy*, 2004.
- [Wagener et al., 2009] G. Wagener, R. State, A. Dulaunoy, and T. Engel, "Self Adaptive High Interaction Honeypots Driven by Game Theory", *Symposium on Stabilization, Safety, and Security*, 2009.
- [Sutton e Barto, 2018] R. S. Sutton and A. G. Barto, "Reinforcement Learning: An Introduction", *MIT Press*, 2018.

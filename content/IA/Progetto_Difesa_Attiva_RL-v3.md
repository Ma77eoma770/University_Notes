---
title: "Generazione Avversaria di Grafi Dinamici per la Difesa Attiva tramite Tabular Q-Learning"
tags: [ "#formalizzazione", "#architettura", "#reinforcement-learning", "#q-learning", "#difesa-attiva", "#pomdp", "#game-theory" ]
date: "2026-07-27"
version: "3.0"
---

# 1. Introduzione e Motivazione Teorica

Negli scenari moderni di sicurezza informatica, il traffico web è dominato da agenti autonomi (bot, scraper, crawler) il cui scopo è l'esfiltrazione sistematica di dati o la mappatura di vulnerabilità. Le difese tradizionali, inclusi gli honeypot statici, mostrano limiti evidenti: gli attaccanti hanno sviluppato contromisure sofisticate per l'identificazione degli ambienti civetta [Krawetz, 2004], analizzando discrepanze hardware, latenze o anomalie nel file system [Holz e Raynal, 2005]. 

Per superare queste limitazioni, la letteratura recente suggerisce l'impiego di *Honeypot Adattivi*, capaci di reagire dinamicamente per prolungare l'interazione con l'attaccante e ritardarne il rilevamento [Wagener et al., 2009]. L'obiettivo del nostro approccio di Difesa Attiva è intrappolare il bot in un *Tarpit* dinamico strutturato come un labirinto infinito, massimizzando lo spreco delle sue risorse computazionali, di memoria e di tempo.

> [!info] Novità Accademica del Progetto
> Mentre i lavori precedenti in letteratura applicano il Reinforcement Learning a comandi del sistema operativo o chiamate di sistema (con azioni difensive quali *Allow, Block, Substitute*) [Dowling et al., 2018], questo progetto propone una traslazione del dominio di intervento: lo spazio delle azioni non opera a livello di shell OS, ma si manifesta come **perturbazione topologica di un grafo** (es. introduzione di *Bait, Loop, Dead-end*). 

Inoltre, modelliamo l'agente malevolo non come un visitatore casuale (Breadth-First Search), ma come un **Focused Crawler** [Chakrabarti et al., 1999]: un bot guidato da algoritmi euristici (assimilabili ad $A^*$) capaci di valutare e dare priorità ai nodi più promettenti al fine di ottimizzare la raccolta di informazioni.

# 2. Formalizzazione Formale del Problema

Astraendo i dettagli dei protocolli di rete, l'interazione è formalizzata come un processo di esplorazione avversaria su un grafo diretto $\mathcal{G}_{t} = (V_{t}, E_{t})$. 

### 2.1 Modello dell'Attaccante e Razionalità Limitata
L'attaccante esplora il grafo utilizzando l'algoritmo di pathfinding ottimo $A^*$. L'algoritmo $A^*$ di per sé è deterministico; tuttavia, nella realtà i bot avversari operano in condizioni di incertezza e con vincoli di risorse. Spieghiamo quindi la stocasticità delle mosse dell'attaccante introducendo il concetto di *Razionalità Limitata (Bounded Rationality)*: l'attaccante non possiede una mappa perfetta né risorse infinite, ma è vincolato da un parametro psicologico-computazionale che chiamiamo **patience**. La mancanza di informazione globale e il calo di questa "pazienza" introducono rumore e deviazioni euristiche nelle decisioni dell'agente.

Questa stocasticità permette di mappare le reazioni dell'attaccante al framework probabilistico di [Wagener et al., 2009], definendo formalmente:
- **Passo normale**: $Pr(Retry)$
- **Backtrack**: $Pr(Alternative)$
- **Give Up (Fuga)**: $Pr(Quit)$

Nel rispetto del vincolo formale: $Pr(Retry) + Pr(Alternative) + Pr(Quit) = 1$.

### 2.2 Processo Decisionale di Markov Parzialmente Osservabile (POMDP)

> [!abstract] Definizione 1: Formulazione POMDP e Aggregazione degli Stati
> Dato che il Difensore non può conoscere l'esatto livello di pazienza o la coda interna dell'Attaccante, modella l'ambiente come un POMDP $\mathcal{P} = \langle \mathcal{S}, \mathcal{A}, \mathcal{T}, \mathcal{R}, \Omega, \mathcal{O}, \gamma \rangle$ [Hayatle et al., 2013].

- **Spazio degli Stati e State Aggregation ($\mathcal{S}$)**: Per evitare l'esplosione combinatoria tipica del Tabular Q-Learning (la *Curse of Dimensionality*), le variabili continue e discrete ampie (come la pazienza $p$ e la distanza $d$) vengono discretizzate in intervalli tramite tecniche di **State Aggregation** [Sutton e Barto, 2018, Cap. 9]. Uno stato si esprime quindi come $s = \langle bucket(p), bucket(d), \tau \rangle$.
- **Spazio delle Azioni ($\mathcal{A}$)**: Le perturbazioni topologiche $\mathcal{A} = \{ a_{bait}, a_{loop}, a_{deadend}, a_{wait} \}$.
- **Spazio delle Osservazioni e Belief State**: L'Ambiente estrae un'osservazione $\tilde{s} \in \Omega$ che il difensore utilizza per aggiornare la propria percezione dello stato corrente (Belief State), decidendo l'azione ottimale anche in presenza di incertezza.
- **Funzione di Ricompensa (Reward Shaping) ($\mathcal{R}$)**:
  $$ \mathcal{R}(s, a) = w_1 \cdot \Delta_{steps} - w_2 \cdot c(a) - \mathcal{P}_{escape} $$
  L'utilizzo dei pesi $w_1, w_2$ e della penalità $\mathcal{P}_{escape}$ è rigorosamente basato sui principi matematici del **Potential-based Reward Shaping** [Ng et al., 1999]. Questo garantisce che le ricompense intermedie guidino rapidamente il learning del Difensore senza corrompere la garanzia di convergenza verso la politica ottima originaria (evitando il fenomeno del *reward hacking*).

# 3. Progettazione Architetturale e Dettagli Implementativi

Per concentrarci sulla logica algoritmica ed evitare i colli di bottiglia computazionali del Deep Learning, il simulatore è scritto in Python puro, senza framework pesanti (come PyTorch). Il codice è diviso in tre moduli principali.

### 3.1 L'Ambiente (`class environment`)
Essenzialmente il campo di battaglia dove si scontrano i due agenti. I suoi compiti sono:
- **Gestire il grafo**: Utilizzando la libreria `NetworkX`, crea un grafo valido iniziale (con un cammino da *start* a *target* e un'euristica consistente) definendone la topologia (densità degli archi, numero totale di nodi, distanza minima).
- **Scandire i turni di gioco**: Raccoglie metriche (tempo di esecuzione e memoria virtuale allocata) e funge da tramite per il grafo, simulando le informazioni che i due agenti percepiscono.
- **Dedurre uno Stato**: Da tutte le metriche raccolte (posizione dell'attaccante, ultima trappola posizionata, stima della pazienza) deduce lo stato parzialmente osservabile da passare al Difensore.

### 3.2 L'Attaccante (`class attaccante`)
Rappresenta il bot malevolo (*Focused Crawler*). Il suo obiettivo è raggiungere il nodo *target* (con euristica 0). Utilizza l'algoritmo $A^*$ dotato di un parametro aggiuntivo: la `patience`. All'inizio è settata a `max_patience` e diminuisce secondo questi criteri:
- **Euristica inconsistente**: Se i nodi presentano valori troppo promettenti per essere veri, sospetta una trappola e perde pazienza.
- **Vicolo cieco**: Esplorare un lungo cammino che si risolve in un *dead end* (degenerazione in DFS) comporta una drastica perdita di pazienza.
- **Passo normale**: L'esplorazione di un nodo coerente ripristina o fa perdere una quantità minima di pazienza.

Se la `patience` scende a 0:
- **Backtrack**: Il bot torna al primo nodo precedente con `patience > 0` e vicini da esplorare (solo se `max_backtrack > 0`).
- **Give Up**: Esauriti i tentativi, abbandona prematuramente il grafo.

### 3.3 Il Difensore (`class difensore`)
Il nucleo logico intelligente. È addestrato tramite **Tabular Q-Learning** (un algoritmo *off-policy* che separa l'esplorazione dall'aggiornamento, differentemente dal SARSA usato in [Dowling et al., 2018]). Altera la topologia scegliendo tra queste trappole, ognuna con un proprio costo operativo:
- **Bait**: Un singolo nodo con euristica estremamente allettante.
- **Loop**: Una serie di nodi (es. 3) che si ricongiungono ciclicamente.
- **Death end**: Un cammino lineare che termina con un vicolo cieco.
- **Wait**: Non altera il grafo.

Ad ogni turno il difensore valuta la *Q-Table*:
- **$\epsilon$-greedy**: Decide tra *exploitation* e *exploration* (fattore `epsilon_decay` fino a `min_epsilon`).
- **Learning**: Osserva l'effetto e aggiorna la Q-Table tramite l'equazione di Bellman:
  `new_q = current_q + learning_rate * (reward + gamma * max_future_q - current_q)`

# 4. Flusso Logico: Un Turno di Simulazione

Ecco come si svolge nel dettaglio un singolo turno di gioco:
1. **Rilevamento dello Stato Iniziale**: L'ambiente deduce lo stato (stima della pazienza divisa in bucket, distanza stimata dal target, trappole adiacenti).
2. **Scelta e Azione del Difensore**: Il Difensore consulta la Q-Table e sceglie la mossa (esca, loop, dead end, wait), alterando immediatamente la topologia del grafo.
3. **Applicazione del Costo Operativo**: Il Difensore riceve una penalità immediata (costo vivo dell'azione, es. -1.0 per un'esca) per prevenire loop infiniti di generazione.
4. **Mossa dell'Attaccante**: L'Attaccante ($A^*$) reagisce, espande la frontiera e decide se avanzare, effettuare backtracking o fuggire.
5. **Aggiornamento dell'Ambiente**: L'ambiente registra le conseguenze (stima della pazienza residua, nuova distanza dal target) formando il *Next State*.
6. **Valutazione e Ricompensa Finale**: Se l'Attaccante abbandona (*Gave Up*), raggiunge il target, o esaurisce le opzioni (*Stuck*), il turno si chiude calcolando un *End-reward* proporzionale ai nodi sprecati (es. `10 * Step`). Il turno restituisce la tupla (stato iniziale, azione, ricompensa, stato finale) per l'apprendimento.

# 5. Considerazioni su Stato e Osservabilità e Domande Aperte

I due agenti si muovono in uno spazio parzialmente osservabile: l'Attaccante non conosce l'intera topologia (si affida alla sua euristica e alla `patience`), mentre il Difensore deve stimare la pazienza avversaria tramite bucketizzazione. Parametri cruciali dello stato includono la `DistanceToEnd`, `OnTrap` (se il bot è attualmente su una trappola), e `Trappole adiacenti`.

**Principali dubbi algoritmici in via di ottimizzazione:**
- *Lo stato è sufficientemente descrittivo o rischia la Curse of Dimensionality pur con la State Aggregation?*
- *Il sistema di patience e backtracking è coerente? Ci sono casistiche in cui l'attaccante rimane permanentemente "stuck"?*
- *Come bilanciare analiticamente i reward operativi per garantire un "Fair Game"?*
- *Per un addestramento ottimale, è necessario mantenere costante il numero di nodi iniziali e la lunghezza del cammino minimo nei vari episodi?*
- *$A^*$ è la scelta migliore o è necessario supportare costi degli archi differenziati (es. float) per creare trappole più complesse nel dominio di un sito web?*

# 6. Riferimenti Bibliografici

- [Chakrabarti et al., 1999] S. Chakrabarti, M. van den Berg, and B. Dom, "Focused crawling: a new approach to topic-specific Web resource discovery", *Computer Networks*, 31(11-16), 1623-1640, 1999.
- [Dowling et al., 2018] S. Dowling, M. Schukat, and E. Barrett, "Improving adaptive honeypot functionality with efficient reinforcement learning parameters for automated malware", *Journal of Cyber Security Technology*, 2018.
- [Hayatle et al., 2013] O. Hayatle, H. Otrok, and A. Youssef, "A Markov Decision Process Model for High Interaction Honeypots", *Information Security Journal*, 2013.
- [Holz e Raynal, 2005] T. Holz and F. Raynal, "Detecting honeypots and other suspicious environments", *Proceedings of the 2005 IEEE Workshop on Information Assurance and Security*, 2005.
- [Krawetz, 2004] N. Krawetz, "Anti-honeypot technology", *IEEE Security & Privacy*, 2004.
- [Ng et al., 1999] A. Y. Ng, D. Harada, and S. Russell, "Policy invariance under reward transformations: Theory and application to reward shaping", *ICML*, 1999.
- [Sutton e Barto, 2018] R. S. Sutton and A. G. Barto, "Reinforcement Learning: An Introduction", *MIT Press*, 2018.
- [Wagener et al., 2009] G. Wagener, R. State, A. Dulaunoy, and T. Engel, "Self Adaptive High Interaction Honeypots Driven by Game Theory", *Symposium on Stabilization, Safety, and Security*, 2009.

---
title: "Generazione Avversaria di Grafi Dinamici per la Difesa Attiva tramite Tabular Q-Learning"
tags: [ "#formalizzazione", "#architettura", "#reinforcement-learning", "#q-learning", "#difesa-attiva", "#mdp", "#game-theory" ]
date: "2026-07-27"
version: "4.0"
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

### 2.2 Modellazione come Processo Decisionale di Markov (MDP) Discreto

> [!abstract] Definizione 1: Formulazione MDP e Aggregazione degli Stati
> Sebbene l'intento interno dell'attaccante sia parzialmente nascosto, il Difensore ha pieno accesso alle metriche della simulazione fornite dall'ambiente. Evitiamo deliberatamente la formulazione POMDP per mantenere intatte le garanzie di convergenza del Tabular Q-Learning. Costruiamo invece un **MDP completamente osservabile** $\mathcal{M} = \langle \mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma \rangle$ astraendo il comportamento dell'attaccante tramite una tecnica di *State Aggregation* [Sutton e Barto, 2018].

- **Spazio degli Stati e State Aggregation ($\mathcal{S}$)**: Per evitare l'esplosione combinatoria tipica del Tabular Q-Learning (la *Curse of Dimensionality*), le variabili continue e discrete ampie vengono discretizzate in intervalli ("bucket"). Lo stato $s \in \mathcal{S}$ al tempo $t$ è definito come una tupla discreta:
  $$s_t = \langle \text{bucket}(p_t), \text{bucket}(d_t), trap\_type \rangle$$
  dove $p_t$ è la stima della pazienza residua, $d_t$ è la distanza stimata dal target, e $trap\_type$ è l'ultimo inganno piazzato. Assumiamo che questa rappresentazione sintetica rispetti sufficientemente la proprietà di Markov, permettendo al Q-Learning di mappare in modo univoco le dinamiche dell'ambiente.
- **Spazio delle Azioni ($\mathcal{A}$)**: Le perturbazioni topologiche $\mathcal{A} = \{ a_{bait}, a_{loop}, a_{deadend}, a_{wait} \}$.
- **Funzione di Ricompensa e Logica del Tarpit ($\mathcal{R}$):**

Poiché il sistema è un honeypot basato su dati fittizi (_fake data_), non esiste una vera e propria "sconfitta catastrofica" (il bot non può rubare dati reali), né il bot rimarrà permanentemente _stuck_: se le trappole sono troppo invasive, la sua _pazienza_ si esaurirà e si darà alla fuga (_Give Up_). Di conseguenza, l'obiettivo del Difensore non è vincere una partita a scacchi, ma **massimizzare lo spreco di tempo e risorse del bot**, logorandone la pazienza prima che raggiunga l'uscita o che fugga per eccesso di trappole.

Per modellare questo obiettivo senza incorrere in cicli di _reward hacking_, utilizziamo il **Potential-based Reward Shaping** [Ng et al., 1999]. La ricompensa base dell'ambiente $R(s, a, s')$ è definita come:

$$R(s, a, s') = \Delta \text{pazienza\_persa} + w_3 \cdot \text{passi\_effettuati} - c(a) - \mathcal{P}_{\text{fuga\_precoce}}$$

Dove $\Delta \text{pazienza\_persa}$ premia il consumo di risorse del bot, $c(a)$ è il costo vivo per l'impiego della trappola (per evitare lo spam inutile di nodi), e $\mathcal{P}_{\text{fuga\_precoce}}$ penalizza le configurazioni troppo aggressive che fanno scappare subito il bot.

Per guidare l'apprendimento (_reward shaping_), definiamo il potenziale $\Phi(s)$ dello stato legandolo allo stato di logoramento e avanzamento del bot:

$$\Phi(s) = w \cdot \left( \text{MaxDist} - \text{Dist}(s) \right) \times \left( \text{MaxPat} - \text{Pat}(s) \right)$$

Il reward di shaping finale è calcolato come differenza di potenziale $F(s,a,s') = \gamma \Phi(s') - \Phi(s)$, garantendo che l'agente riceva feedback positivi solo quando le sue azioni stringono d'assedio il bot, spingendolo verso il consumo totale della sua pazienza.$$ \mathcal{R}'(s,a,s') = R(s, a, s') + F(s, a, s') $$

# 3. Progettazione Architetturale e Dettagli Implementativi

Il simulatore sarà implementato in Python standard. La scelta di utilizzare il Tabular Q-Learning rispetto ad approcci di Deep Reinforcement Learning (es. DQN via PyTorch) non è dettata da limiti computazionali, ma da una precisa scelta architetturale: grazie alla corretta discretizzazione dello spazio (*State Aggregation*), lo spazio stato-azione è mantenuto compatto. Questo permette di utilizzare una Q-Table esatta, garantendo totale **interpretabilità** e **spiegabilità** delle politiche di difesa apprese (aspetto cruciale nel dominio della cybersecurity) e una maggiore *sample efficiency* (convergenza più rapida e stabile rispetto all'approssimazione tramite reti neurali).

Il codice è diviso in tre moduli principali:

### 3.1 L'Ambiente (`class environment`)
Essenzialmente il campo di battaglia dove si scontrano i due agenti. I suoi compiti sono:
- **Gestire il grafo**: Utilizzando la libreria `NetworkX`, crea un grafo valido iniziale (con un cammino da *start* a *target* e un'euristica consistente) definendone la topologia (densità degli archi, numero totale di nodi, distanza minima).
- **Scandire i turni di gioco**: Raccoglie metriche (tempo di esecuzione e memoria virtuale allocata) e funge da tramite per il grafo, simulando le informazioni che i due agenti percepiscono.
- **Dedurre uno Stato**: Da tutte le metriche raccolte (posizione dell'attaccante, ultima trappola posizionata, stima della pazienza) estrae le *features* necessarie a formare lo stato aggregato da passare al Difensore.

### 3.2 L'Attaccante (`class attaccante`)
Rappresenta il bot malevolo (*Focused Crawler*). Il suo obiettivo è raggiungere il nodo *target* (con euristica 0). Utilizza l'algoritmo $A^*$ dotato di un parametro aggiuntivo: la `patience`. All'inizio è settata a `max_patience` e diminuisce secondo questi criteri:
- **Euristica inconsistente**: Se i nodi presentano valori troppo promettenti per essere veri, sospetta una trappola e perde pazienza in modo marcato.
- **Vicolo cieco**: Esplorare un lungo cammino che si risolve in un *dead end* (degenerazione in DFS) comporta una drastica perdita di pazienza.
- **Passo normale**: L'esplorazione di un nodo coerente fa perdere una quantità minima e naturale di pazienza.

Se la `patience` scende a 0:
- **Backtrack**: Il bot torna al primo nodo precedente con `patience > 0` e vicini da esplorare (solo se `max_backtrack > 0`).
- **Give Up**: Esauriti i tentativi di backtracking, abbandona prematuramente il grafo.

### 3.3 Il Difensore (`class difensore`)
Il nucleo logico intelligente. È addestrato tramite **Tabular Q-Learning** (un algoritmo *off-policy* che separa l'esplorazione dall'aggiornamento dell'ottimo, differentemente dal SARSA). Altera la topologia scegliendo tra queste trappole:
- **Bait**: Un singolo nodo con euristica estremamente allettante per sviare la ricerca.
- **Loop**: Una serie di nodi (es. 3) che si ricongiungono ciclicamente per intrappolare il bot.
- **Death end**: Un cammino lineare che termina con un vicolo cieco per forzare un backtrack pesante.
- **Wait**: Non altera il grafo, risparmiando il costo operativo dell'azione.

Ad ogni turno il difensore valuta la *Q-Table*:
- **$\epsilon$-greedy**: Decide tra *exploitation* e *exploration* (fattore `epsilon_decay` fino a `min_epsilon`).
- **Learning**: Osserva l'effetto topologico e aggiorna la Q-Table tramite l'equazione di Bellman standard.

# 4. Flusso Logico: Un Turno di Simulazione

Ecco come si svolge nel dettaglio un singolo turno di gioco:
1. **Rilevamento dello Stato Iniziale**: L'ambiente deduce lo stato aggregato (stima della pazienza divisa in bucket, distanza stimata dal target in bucket, tipo di trappole adiacenti).
2. **Scelta e Azione del Difensore**: Il Difensore consulta la Q-Table e sceglie la mossa (esca, loop, dead end, wait), alterando immediatamente la topologia del grafo.
3. **Applicazione del Costo Operativo**: Il Difensore computa il costo vivo dell'azione $c(a)$ da sottrarre alla ricompensa base.
4. **Mossa dell'Attaccante**: L'Attaccante ($A^*$) reagisce, espande la frontiera topologica e decide se avanzare, effettuare backtracking o fuggire.
5. **Aggiornamento dell'Ambiente**: L'ambiente registra le conseguenze strutturali (nuova stima della pazienza, nuova posizione) formando il *Next State*.
6. **Valutazione e Reward Shaping**: L'ambiente calcola il differenziale di potenziale $\Phi(s') - \Phi(s)$ in base allo spreco di risorse causato e lo somma al reward base. Il turno restituisce la tupla `(stato_iniziale, azione, reward_totale, stato_finale)` per l'aggiornamento della Q-Table.

# 5. Considerazioni su Stato, Osservabilità e Domande Aperte

Sebbene la natura intrinseca del dominio presenti elementi di parziale osservabilità (l'Attaccante non conosce l'intera topologia a priori e il Difensore deve "indovinare" la pazienza avversaria), l'ingegnerizzazione dello stato e la successiva State Aggregation astraggono queste dinamiche in un MDP discretizzato e completamente osservabile per l'agente Difensore. Parametri cruciali dello stato includono la `DistanceToEnd`, l'informazione `OnTrap` (se il bot è attualmente su una struttura anomala), e l'identificativo dell'ultimo inganno generato.

**Principali dubbi algoritmici in via di ottimizzazione:**
- *La discretizzazione in bucket dello stato è sufficientemente descrittiva per catturare le sfumature strategiche dell'attaccante senza ricadere nella Curse of Dimensionality?*
- *Il sistema di decrease della patience e conseguente backtracking è strutturalmente solido? Ci sono casistiche topologiche in cui l'algoritmo $A^*$ dell'attaccante degenera in uno stato "stuck" non previsto?*
- *Come bilanciare analiticamente i pesi scalari $w$ della funzione di potenziale per calibrare correttamente il "Fair Game" tra i due agenti?*
- *Per favorire una corretta generalizzazione della policy, è preferibile mantenere costante la distanza minima start-target negli episodi di training o introdurre una randomizzazione strutturale controllata?*

# 6. Riferimenti Bibliografici

- [Chakrabarti et al., 1999] S. Chakrabarti, M. van den Berg, and B. Dom, "Focused crawling: a new approach to topic-specific Web resource discovery", *Computer Networks*, 31(11-16), 1623-1640, 1999.
- [Dowling et al., 2018] S. Dowling, M. Schukat, and E. Barrett, "Improving adaptive honeypot functionality with efficient reinforcement learning parameters for automated malware", *Journal of Cyber Security Technology*, 2018.
- [Hayatle et al., 2013] O. Hayatle, H. Otrok, and A. Youssef, "A Markov Decision Process Model for High Interaction Honeypots", *Information Security Journal*, 2013.
- [Holz e Raynal, 2005] T. Holz and F. Raynal, "Detecting honeypots and other suspicious environments", *Proceedings of the 2005 IEEE Workshop on Information Assurance and Security*, 2005.
- [Krawetz, 2004] N. Krawetz, "Anti-honeypot technology", *IEEE Security & Privacy*, 2004.
- [Ng et al., 1999] A. Y. Ng, D. Harada, and S. Russell, "Policy invariance under reward transformations: Theory and application to reward shaping", *ICML*, 1999.
- [Sutton e Barto, 2018] R. S. Sutton and A. G. Barto, "Reinforcement Learning: An Introduction", *MIT Press*, 2018.
- [Wagener et al., 2009] G. Wagener, R. State, A. Dulaunoy, and T. Engel, "Self Adaptive High Interaction Honeypots Driven by Game Theory", *Symposium on Stabilization, Safety, and Security*, 2009.
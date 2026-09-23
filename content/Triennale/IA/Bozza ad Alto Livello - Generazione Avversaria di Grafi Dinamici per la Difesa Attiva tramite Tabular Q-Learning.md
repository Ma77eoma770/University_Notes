# 1. Introduzione e Motivazione Teorica

Negli scenari moderni di sicurezza informatica, il traffico web è dominato da agenti autonomi (bot, scraper, crawler) il cui scopo è l'esfiltrazione di dati o la mappatura di vulnerabilità. Le difese tradizionali, inclusi gli honeypot statici, mostrano limiti evidenti: gli attaccanti hanno sviluppato contromisure sofisticate per l'identificazione degli ambienti honeypot [Krawetz, 2004, Sez. 1–2], analizzando discrepanze hardware, latenze o anomalie nel file system [Holz e Raynal, 2005, Sez. III–IV]. 

Per superare queste limitazioni, la letteratura recente suggerisce l'impiego di *Honeypot Adattivi*, capaci di reagire dinamicamente per prolungare l'interazione con l'attaccante e ritardarne il rilevamento [Wagener et al., 2009, Sez. 1–2]. L'obiettivo del nostro approccio di Difesa Attiva è intrappolare il bot in un *Tarpit* dinamico strutturato come un labirinto infinito, massimizzando lo spreco delle sue risorse computazionali, di memoria e di tempo.

> [!info] Novità del Progetto
> Mentre i lavori precedenti in letteratura applicano il Reinforcement Learning a comandi del sistema operativo o chiamate di sistema (con azioni difensive quali *Allow, Block, Substitute*) [Dowling et al., 2018, Sez. 3; Dowling et al., 2019, Sez. 4], questo progetto propone una traslazione del dominio di intervento: lo spazio delle azioni non opera a livello di shell OS, ma si manifesta come **perturbazione topologica di un grafo** (es. introduzione di *Bait, Loop, Dead-end*). 

Inoltre, modelliamo l'agente malevolo non come un visitatore casuale (Breadth-First Search), ma come un **Focused Crawler** [Chakrabarti et al., 1999, Sez. 1–2]: un bot guidato da algoritmi euristici (assimilabili ad $A^*$) capaci di valutare e dare priorità ai nodi più promettenti al fine di ottimizzare la raccolta di informazioni.

# 2. Formalizzazione del Problema

Astraendo i dettagli dei protocolli di rete, l'interazione è formalizzata come un processo di esplorazione avversaria su un grafo diretto $\mathcal{G}_{t} = (V_{t}, E_{t})$. 

### 2.1 Modello dell'Attaccante e Razionalità Limitata
L'attaccante esplora il grafo utilizzando l'algoritmo di pathfinding $A^*$. L'algoritmo $A^*$ di per sé è deterministico; tuttavia, nella realtà i bot avversari operano in condizioni di incertezza e con vincoli di risorse. Spieghiamo quindi la stocasticità delle mosse dell'attaccante introducendo il concetto di *Razionalità Limitata (Bounded Rationality)*: l'attaccante non possiede una mappa perfetta né risorse infinite, ma è vincolato da un parametro psicologico-computazionale che chiamiamo **patience**. La mancanza di informazione globale e il calo di questa "pazienza" introducono rumore e deviazioni euristiche nelle decisioni dell'agente.

Questa stocasticità permette di mappare le reazioni dell'attaccante al framework probabilistico di [Wagener et al., 2009, Sez. 3, Eq. 1], definendo formalmente:
- **Passo normale**: $Pr(Retry)$
- **Backtrack**: $Pr(Alternative)$
- **Give Up (Fuga)**: $Pr(Quit)$

Nel rispetto del vincolo formale: $Pr(Retry) + Pr(Alternative) + Pr(Quit) = 1$.

### 2.2 Modellazione come Processo Decisionale di Markov (MDP) Discreto

> [!abstract] Definizione 1: Formulazione MDP e Aggregazione degli Stati
> Sebbene l'interazione avversaria presenti elementi di parziale osservabilità (come nei modelli difensivi MDP e POMDP su honeypot analizzati da [Hayatle et al., 2013, Sez. 2–3]), nel nostro ambiente il Difensore ha pieno accesso alle metriche della simulazione (es. posizione dell'attaccante e struttura completa della topologia). Evitiamo deliberatamente la formulazione POMDP per mantenere intatte le garanzie matematiche di convergenza esatta del Tabular Q-Learning. Costruiamo invece un **MDP completamente osservabile** $\mathcal{M} = \langle \mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma \rangle$ astraendo e "rendendo certo" il comportamento dell'attaccante tramite una tecnica di *State Aggregation* [Sutton e Barto, 2018, Cap. 9.3].

- **Spazio degli Stati e State Aggregation ($\mathcal{S}$)**: Per evitare l'esplosione combinatoria tipica del Tabular Q-Learning (la *Curse of Dimensionality*), le variabili continue e discrete ampie vengono discretizzate in intervalli ("bucket"). Lo stato $s \in \mathcal{S}$ al tempo $t$ è definito come una tupla discreta:
  $$s_t = \langle \text{bucket}(p_t), \text{bucket}(d_t), near\_trap \rangle$$
  dove $p_t$ è la stima della pazienza residua, $d_t$ è la distanza stimata dal target, e $near\_trap$ è un booleno sulla presenza di un nodo esca adiacente e quindi possibilmente visitabile dall'attaccante. Assumiamo che questa rappresentazione sintetica rispetti sufficientemente la proprietà di Markov (certezza dell'ambiente), permettendo al Q-Learning di mappare in modo univoco le dinamiche dell'ambiente.
- **Spazio delle Azioni ($\mathcal{A}$)**: Le perturbazioni topologiche $\mathcal{A} = \{ a_{bait}, a_{loop}, a_{deadend}, a_{wait} \}$.
- **$\mathcal{P}$ (Transition Probability Matrix / Function):** È la funzione delle probabilità di transizione. Rappresenta la probabilità di finire nello stato $s'$ eseguendo l'azione $a$ nello stato $s$.
- **Funzione di Ricompensa e Logica del Tarpit ($\mathcal{R}$):** Poiché il sistema è un honeypot basato su dati fittizi, non esiste una vera e propria "sconfitta", se anche raggiungesse il nodo *target* non ci sarebbero perdite (di dati ad es.), né il bot rimarrà permanentemente _stuck_: se le trappole sono troppo invasive, la sua _pazienza_ si esaurirà e farà _Give Up_. Di conseguenza, l'obiettivo del Difensore non è "vincere la partita", ma **massimizzare lo spreco di tempo e risorse del bot**, logorandone la pazienza prima che raggiunga l'uscita o che fugga per eccesso di trappole. Per modellare questo obiettivo senza incorrere in cicli di _reward hacking_, utilizziamo il **Potential-based Reward Shaping** [Harada et al., 1999, Sez. 3, Teor. 1]. La ricompensa base dell'ambiente $R(s, a, s')$ è definita focalizzandosi esclusivamente sui costi e sulla longevità dell'interazione: $$R(s, a, s') = w_3 \cdot \text{passi\_effettuati} - c(a) - \mathcal{P}_{\text{fuga\_precoce}}$$Dove $w_3 \cdot \text{passi\_effettuati}$ premia la permanenza prolungata del bot all'interno del labirinto, $c(a)$ è il costo vivo per l'impiego della trappola (per evitare lo spam inutile di nodi difensivi), e $\mathcal{P}_{\text{fuga\_precoce}}$ applica una penalità netta alle configurazioni troppo aggressive che causano l'abbandono prematuro del grafo da parte del bot.
  Per guidare l'apprendimento (_reward shaping_) e garantire rigorosamente l'invarianza della policy ottimale, definiamo il potenziale $\Phi(s)$ dello stato legandolo alla capacità strategica di mantenere il bot disorientato e logorato (patience bassa): $$\Phi(s) = w \cdot \text{Dist}(s) \times \left( \text{MaxPat} - \text{Pat}(s) \right)$$Il reward di shaping finale è calcolato come differenza di potenziale: $$F(s,a,s') = \gamma \Phi(s') - \Phi(s)$$Questa formulazione del potenziale raggiunge il suo massimo quando l'attaccante si trova lontano dall'obiettivo ($\text{Dist}(s)$ elevata) e con le risorse quasi esaurite ($\text{MaxPat} - \text{Pat}(s)$ elevata), agendo come una bussola strategica perfetta per l'agente. Il reward totale assegnato al difensore è quindi la somma del feedback ambientale e della differenza di potenziale:

$$\mathcal{R}'(s,a,s') = R(s, a, s') + F(s, a, s')$$

# 3. Progettazione Architetturale e Dettagli Implementativi

Il simulatore sarà implementato in Python standard. La scelta di utilizzare il Tabular Q-Learning rispetto ad approcci di Deep Reinforcement Learning (es. DQN via PyTorch) non è dettata da limiti computazionali, ma da una precisa scelta architetturale: grazie alla corretta discretizzazione dello spazio (*State Aggregation*, [Sutton e Barto, 2018, Cap. 9.3]), lo spazio stato-azione è mantenuto compatto. Questo permette di utilizzare una Q-Table esatta, garantendo totale **interpretabilità** e **spiegabilità** delle politiche di difesa apprese e una maggiore *sample efficiency* (convergenza più rapida e stabile rispetto all'approssimazione tramite reti neurali).

Il codice è diviso in tre moduli principali:

### 3.1 L'Ambiente (`class environment`)
Essenzialmente il campo di battaglia dove si scontrano i due agenti. I suoi compiti sono:
- **Gestire il grafo**: Utilizzando la libreria `NetworkX`, crea un grafo valido iniziale (con un cammino da *start* a *target* e un'euristica consistente) definendone la topologia (densità degli archi, numero totale di nodi, distanza minima).
- **Scandire i turni di gioco**: Raccoglie metriche (tempo di esecuzione e memoria virtuale allocata) e funge da tramite per il grafo, simulando le informazioni che i due agenti percepiscono.
- **Dedurre uno Stato**: Da tutte le metriche raccolte (posizione dell'attaccante, ultima trappola posizionata, stima della pazienza) estrae le *features* necessarie a formare lo stato aggregato da passare al Difensore.

### 3.2 L'Attaccante (`class attaccante`)
Modella il bot malevolo operando come un _Focused Crawler_ [Chakrabarti et al., 1999, Sez. 3] guidato da una variante euristica di $A^*$. L'elemento cardine di questo modulo è la gestione della **pazienza**, che introduce la stocasticità derivante dalla _Razionalità Limitata_: l'attaccante non possiede una mappa perfetta e subisce un logorio psicologico-computazionale durante l'esplorazione.
- **Dinamica del consumo di pazienza**:
    - _Passo normale_: La traversata di un arco coerente consuma una quantità minima di pazienza ($Pr(Retry)$).
    - _Euristica ingannevole / Vicoli ciechi_: L'incontro con trappole o anomalie topologiche (come _Bait_ o _Dead-end_) provoca un calo marcato e non lineare della pazienza, spingendo l'agente a valutare deviazioni.
- **Esito del logorio**: Quando la pazienza $p_t$ si esaurisce progressivamente, l'attaccante abbandona il percorso corrente optando per un'azione alternativa o di fuga:
    - **Backtrack ($Pr(Alternative)$)**: L'attaccante ritorna sui suoi passi verso il primo nodo precedente con risorse residue.
    - **Give Up / Fuga ($Pr(Quit)$)**: Se le trappole risultano troppo invasive e la pazienza si azzera del tutto, l'attaccante abbandona prematuramente il grafo.

### 3.3 Il Difensore (`class difensore`)
Il nucleo logico intelligente. È addestrato tramite **Tabular Q-Learning** (un algoritmo *off-policy* che separa l'esplorazione dall'aggiornamento dell'ottimo, differentemente dal SARSA [Sutton e Barto, 2018, Cap. 6.5, 12.7]). Altera la topologia scegliendo tra queste trappole:
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

Sebbene la natura intrinseca del dominio presenti elementi di parziale osservabilità (l'Attaccante non conosce l'intera topologia a priori e il Difensore deve "indovinare" la pazienza avversaria), l'ingegnerizzazione dello stato e la successiva State Aggregation astraggono queste dinamiche in un MDP discretizzato e completamente osservabile per l'agente Difensore. 

**Principali dubbi (discutere in call):**
- Le feature all'interno dello stato sono abbastanza (descrittive)?
- Il sistema di patience è abbstanza per poter parlare di razionalità limitata? Andrebbe introdotto più rumore?
- MDP formalmente corretto?
- L'attaccante non ha piena osservabilità sui nodi (non sa se sono trappole con certezza), il controllo (e conseguente aggiornamento di patience e probabilità di backtracking) con che criteri si dovrà basare? (Euristica incosistente, ristagnazione su nodi sempre promettenti ma mai veritieri, "backtracking")
- Per favorire una corretta generalizzazione della policy, è necessario alterare la topolgia del grafo sostanzialmente (numero di nodi totali, distanza start-target minima)? Se si quale metrica sarebbe più utile randomizzare?

# 6. Riferimenti Bibliografici

- **[Chakrabarti et al., 1999]** S. Chakrabarti, M. van den Berg, and B. Dom. *"Focused crawling: a new approach to topic-specific Web resource discovery"*. Computer Networks, 1999.
- **[Dowling et al., 2018]** S. Dowling, M. Schukat, and E. Barrett. *"Improving adaptive honeypot functionality with efficient reinforcement learning parameters for automated malware"*. Journal of Cyber Security Technology, 2018.
- **[Dowling et al., 2019]** S. Dowling, M. Schukat, and E. Barrett. *"Using Reinforcement Learning to Conceal Honeypot Functionality"*. SECRYPT / IEEE, 2019.
- **[Hayatle et al., 2013]** O. Hayatle, H. Otrok, and A. Youssef. *"A Markov Decision Process Model for High Interaction Honeypots"*. Information Security Journal, 2013.
- **[Holz e Raynal, 2005]** T. Holz and F. Raynal. *"Detecting honeypots and other suspicious environments"*. IEEE Workshop on Information Assurance and Security, 2005.
- **[Krawetz, 2004]** N. Krawetz. *"Anti-Honeypot Technology"*. IEEE Security & Privacy, 2004.
- **[Harada et al., 1999]** A. Y. Ng, D. Harada, and S. Russell. *"Policy invariance under reward transformations: Theory and application to reward shaping"*. ICML, 1999.
- **[Sutton e Barto, 2018]** R. S. Sutton and A. G. Barto. *"Reinforcement Learning: An Introduction (2nd Edition)"*. MIT Press, 2018.
- **[Wagener et al., 2009]** G. Wagener, R. State, A. Dulaunoy, and T. Engel. *"Self Adaptive High Interaction Honeypots Driven by Game Theory"*. SSS, 2009.
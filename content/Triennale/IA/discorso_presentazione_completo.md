# Generazione Avversaria di Tarpit Adattivi per la Difesa Attiva tramite Tabular Q-Learning
*Relazione e Testo di Presentazione Dettagliato*
*Autore: Matteo Logozzi*

---

## 1. Introduzione, Motivazione e Contesto della Cyber-Deception

Buongiorno a tutti, e benvenuti alla presentazione di questo lavoro di ricerca dal titolo: *"Generazione Avversaria di Tarpit Adattivi per la Difesa Attiva tramite Tabular Q-Learning"*. 

### Il Problema degli Honeypot Statici Tradizionali
Negli scenari di cybersecurity odierna, la stragrande maggioranza del traffico dannoso e delle scansioni di rete non proviene da operatori umani diretti, ma da agenti autonomi altamente specializzati: bot, web scraper e crawler avversari. Il loro obiettivo primario è la ricognizione sistematica, la mappatura delle architetture e l'esfiltrazione automatizzata di dati sensibili o credenziali.

Per decenni, la contromisura standard è stata l'impiego di **Honeypot statici**: sistemi esca progettati per simulare vulnerabilità o servizi esposti al fine di attrarre gli attaccanti. Tuttavia, la letteratura scientifica di settore dimostra che gli honeypot statici hanno ormai perso gran parte della loro efficacia. Come evidenziato da Krawetz, gli attaccanti moderni incorporano routine avanzate di fingerprinting per identificare gli ambienti trappola prima di impegnare risorse rilevanti. Inoltre, i lavori di Holz mostrano come i bot avversari riescano a rilevare la presenza di un honeypot analizzando sottili anomalie nel file system, discrepanze nelle latenze di risposta di rete o limitazioni nell'emulazione dell'hardware.

Di fronte a un blocco binario tradizionale (ad esempio, un muro HTTP `403 Forbidden` o l'interruzione immediata della connessione), l'agente malevolo comprende istantaneamente di essere stato intercettato o che la risorsa non è accessibile, virando immediatamente verso un altro obiettivo o cambiando indirizzo IP. Il punto centrale degli honeypot quindi non è "bloccare" l'attaccante in modo rigido, ma **impedirgli di comprendere la natura dell'ambiente**, trattenendolo all'interno di una struttura ingannevole che consumi le sue risorse senza insospettirlo e, contemporaneamente, studiando i suoi comportamenti.

### Tarpit e Honeypot: Unione dei Concetti
* **Tarpit** (letteralmente "pozza di catrame"): indica una trappola o un sistema progettato per rallentare l'avversario, costringendolo a mantenere le connessioni aperte e a consumare risorse computazionali.
* **Honeypot Dinamico**: un sistema che muta il suo comportamento nel tempo, riuscendo a mimetizzarsi perfettamente.

Il nostro obiettivo è combinare questi concetti elevando lo studio a un livello superiore: non ci occupiamo di protocolli di rete o chiamate di sistema di basso livello, ma ci concentriamo sulla **topologia del network rappresentata come un grafo**, dove ogni macchina o pagina web è un nodo e la possibilità di passare da una all'altra è un arco.

---

## 2. Modellazione dell'Attaccante e Razionalità Limitata

Come si muove un attaccante in una rete sconosciuta? Un approccio casuale (Breadth-First Search pura) sarebbe inefficiente e facilmente identificabile. D'altra parte, l'attaccante non possiede una visibilità globale a priori, ma scopre la topologia nodo per nodo (percezione locale).

### L'Attaccante come Focused Crawler e Algoritmo $A^*$
Modelliamo il bot malevolo come un **Focused Crawler** guidato dall'algoritmo di ricerca informata **$A^*$**. L'attaccante seleziona il prossimo nodo da espandere valutando la funzione di costo complessivo:
$$f(n) = g(n) + h(n)$$
dove $g(n)$ rappresenta il costo effettivo del cammino percorso dal nodo iniziale a $n$, mentre $h(n)$ è la funzione euristica che stima il costo rimanente da $n$ al nodo Target.

### Razionalità Limitata e Modello Dinamico di Pazienza
Nella realtà, i bot avversari sono vincolati da risorse e tempi di scansione limitati. Introduciamo il concetto di **Pazienza** ($P_t \in [0, 100]$): un parametro psicologico-computazionale che rappresenta le risorse residue del bot. Man mano che l'euristica percepita viene manipolata dal difensore, si creano anomalie che provocano la degradazione della pazienza:
* **Costo Passo Base:** Ogni movimento coerente consuma $1.0$ unità di pazienza ($\Delta P = -1.0$).
* **Penale di Backtracking (Salto):** Se l'attaccante deve effettuare un backtrack logico su un altro ramo, subisce una penale di $3.0$ unità ($\Delta P = -3.0$).
* **Penale da Vicolo Cieco (Dead-end):** Raggiungere un nodo senza uscenti infligge una penalità immediata di $15.0$ unità ($\Delta P = -15.0$).
* **Penale per Stagnazione:** Quando l'attaccante si sposta su un nodo che non garantisce un effettivo avanzamento euristico ($h_{next} \ge h_{curr} - 0.1$), viene applicata una penale progressiva non lineare legata a $k$ nodi di stasi:
  $$\Delta P_{stagnazione} = -4.0 	imes k$$

Gli stati di uscita dell'attaccante sono:
1. **Target Reached:** Ha raggiunto il nodo finale.
2. **GaveUp:** La pazienza scende a zero o a valori negativi.

---

## 3. Formulazione dell'Ambiente e Processi Decisionali di Markov (MDP)

Per addestrare il difensore, passiamo al **Reinforcement Learning (RL)**, modellando l'interazione tramite i **Processi Decisionali di Markov (MDP)** formalizzati dalla tupla $(\mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma)$.

### La Proprietà di Markov e il Tabular Q-Learning
Il fondamento degli MDP è la Proprietà di Markov: *"Il futuro è condizionatamente indipendente dal passato, dato il presente"*. L'obiettivo dell'agente è determinare una politica $\pi(a|s)$ che massimizzi il ritorno cumulativo scontato $G_t = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1}$, con $\gamma \in [0, 1)$ come fattore di sconto.

Poiché l'ambiente è ignoto (modello *model-free*), utilizziamo il **Tabular Q-Learning**, un algoritmo di controllo *off-policy* e *Temporal-Difference (TD)*. Sfrutta il *bootstrapping* per aggiornare la stima corrente senza attendere la fine dell'episodio tramite l'equazione di Bellman:
$$Q(s_t, a_t) \leftarrow Q(s_t, a_t) + lpha \left[ R_{t+1} + \gamma \max_{a} Q(S_{t+1}, a) - Q(s_t, a_t) 
ight]$$

### Il Problema della Dimensionalità e la State Aggregation
Se passassimo al Difensore l'intera topologia del grafo e la pazienza esatta dell'attaccante, andremmo incontro alla *Maledizione della Dimensionalità* e a un problema di tipo **POMDP** (Partially Observable Markov Decision Process), che invaliderebbe le garanzie di convergenza del Q-Learning.

La soluzione è l'adozione della **State Aggregation**: raggruppiamo le variabili in bucket discreti per approssimare l'ambiente a uno stato quasi-markoviano.

---

## 4. Spazio degli Stati, Spazio delle Azioni e Reward Shaping

### Lo Stato Sintetico a 3 Variabili
Lo stato del difensore $s_t$ viene rappresentato come una tupla sintetica:
$$s_t = (	ext{PatienceBucket}, 	ext{DistanceBucket}, 	ext{NearTrap})$$
1. **PatienceBucket (0-4):** Discretizzazione a 5 livelli della pazienza residua stimata.
2. **DistanceBucket (0-4):** Discretizzazione a 5 livelli della distanza orientata minima $d_t$ dal Target.
3. **NearTrap (0/1):** Flag booleano che indica la presenza di una trappola attiva tra i vicini.

### Spazio delle Azioni del Difensore ($\mathcal{A}$)
Il Difensore ha a disposizione 4 perturbazioni topologiche applicabili sul nodo corrente dell'attaccante:
* **Bait (Esca, $c = 0.4$):** Genera un nodo esca adiacente con un'euristica estremamente allettante ($h_{bait} = \max(0.5, h_{curr} - 1.5)$).
* **Loop (Ciclo Ingannevole, $c = 1.3$):** Aggancia una catena di 3 nodi ricongiunti in ciclo.
* **Deadend (Vicolo Cieco, $c = 1.2$):** Inietta un ramo lineare che termina in un nodo privo di uscite.
* **Wait (Nessuna Azione, $c = 0.0$):** Lascia la topologia inalterata.

### Potential-Based Reward Shaping (PBRS)
Per evitare lo *Sparse Reward* (premi solo alla fine) e il *Reward Hacking* (spam incontrollato di trappole), applichiamo il teorema di invarianza di Ng et al. tramite una funzione di shaping di potenziale $\Phi(s)$:
$$R'(s, a, s') = R_{base}(s, a, s') + \left( \gamma \Phi(s') - \Phi(s) 
ight)$$
La ricompensa base premia direttamente l'allungamento del cammino dell'attaccante ($PassiExtra / L_{min}$) sottraendo il costo operativo dell'azione, mentre il potenziale $\Phi(s)$ guida il difensore a concentrare le trappole in prossimità del target quando l'attaccante ha quasi esaurito le risorse.

---

## 5. Curriculum Learning e Risultati Sperimentali

### Architettura di Curriculum Learning Multifase
Per evitare l'overfitting su grafi fissi e la non-stazionarietà caotica del training puramente casuale, utilizziamo un'architettura a **3 Fasi**:
* **Fase 1:** Grafo fisso (apprendimento dei pattern elementari).
* **Fase 2:** Aggiunta progressiva di rami paralleli e cicli casuali (perturbazione controllata).
* **Fase 3:** Introduzione di grafi totalmente casuali (generalizzazione).
Ad ogni cambio di fase, gli iperparametri $\epsilon$ (esplorazione) e $lpha$ (apprendimento) subiscono un reset coordinato per riattivare la plasticità e l'esplorazione del modello.

### Analisi dei Risultati
Il confronto tra *Curriculum Learning (CL)* e *Direct Random Training (DRT)* su 100.000 episodi mostra risultati molto interessanti:
* Entrambi i metodi raggiungono una convergenza asintotica quasi identica (reward finale di $pprox 406$ per il CL contro $pprox 411$ per il DRT).
* **Perché questa parità?** Grazie all'estrema compattezza dello spazio degli stati aggregato (appresa in appena 50 macro-stati), il Direct Random Training ha modo di visitare a sufficienza tutte le coppie stato-azione, assorbendo nel lungo periodo il vantaggio iniziale del Curriculum Learning.
* In entrambi i casi, l'attaccante viene costretto a sprecare tra i 20 e i 28 passi in più, raddoppiando la lunghezza minima del percorso originario.

---

## 6. Conclusioni e Sviluppi Futuri
Il progetto dimostra con successo che il Reinforcement Learning può essere applicato efficacemente alla Cyber-Deception topologica, trasformando la rete in un Tarpit Adattivo capace di ingannare e rallentare algoritmi avanzati come $A^*$. 
Tra i lavori futuri figurano l'adozione di **Deep Q-Networks (DQN)** per scalare su reti enterprise con migliaia di nodi e l'estensione a scenari multi-agente avversari.

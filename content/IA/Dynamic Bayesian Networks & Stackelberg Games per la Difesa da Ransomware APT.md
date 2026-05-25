## 1. Introduzione e Motivazioni

### 1.1. Il problema dell'incertezza nella Cybersecurity

Nel panorama della sicurezza informatica, il contrasto alle minacce di tipo **Advanced Persistent Threat (APT)** e ai ransomware mette il difensore di fronte a una condizione di **incertezza intrinseca**. Come formalizzato nella teoria degli agenti razionali, tale incertezza è riconducibile a tre macro-categorie:

- **Pigrizia (Qualification Problem):** È computazionalmente infattibile e concettualmente oneroso mappare ogni singola interazione di sistema o pacchetto di rete in regole deterministiche prive di eccezioni.
    
- **Ignoranza tecnica e strategica:** Manca una teoria esatta e completa del comportamento avversariale. Non si conoscono a priori le capacità dell'attaccante, le zero-day a sua disposizione o le precise tempistiche di attivazione dei payload.
    
- **Ignoranza pratica (Osservabilità Parziale):** I sensori di sicurezza (EDR, SIEM) operano in ambienti parzialmente osservabili e sono intrinsecamente rumorosi, generando elevati tassi di falsi positivi a causa di comportamenti insoliti ma legittimi degli amministratori di sistema.
    

### 1.2. Limiti dei sistemi deterministici e ad albero di decisione

I sistemi di rilevamento classici (Signature-based o basati su Alberi di Decisione statici) soffrono di una rigidità strutturale intrinseca. Un albero di decisione valuta attributi fissi in finestre temporali rigide.

Gli attaccanti moderni sfruttano questa rigidità diluendo le attività nel tempo (tecniche di _low and slow_) per rimanere sotto le soglie di allarme deterministiche. Diventa quindi indispensabile un modello probabilistico capace di accumulare e pesare formalmente l'**evidenza probabilistica** lungo l'intero asse temporale.

## 2. Richiami Teorici (Il Modello Statico)

### 2.1. Sintassi e Semantica delle Reti Bayesiane

Una Rete Bayesiana è un formalismo grafico per la rappresentazione compatta di distribuzioni di probabilità congiunte. Strutturalmente è definita da un grafo orientato aciclico (DAG) $G=(V,E)$:

- I nodi $X \in V$ rappresentano le variabili casuali (es. stati del sistema, sintomi, evidenze).
    
- Gli archi orientati $X \to Y \in E$ modellano un'influenza causale diretta.
    

Ad ogni nodo $X_i$ è associata una Tabella di Probabilità Condizionata (CPT) $P(X_i \mid Parents(X_i))$. Sfruttando l'indipendenza condizionale di ogni nodo dai suoi non-discendenti dati i suoi genitori, la distribuzione congiunta globale si scompone nel prodotto delle distribuzioni locali:

$$P(X_1, \dots, X_n) = \prod_{i=1}^n P(X_i \mid Parents(X_i))$$

### 2.2. L'algoritmo di Eliminazione di Variabili e la sua complessità

Per calcolare la distribuzione _a posteriori_ delle variabili di query $X$ data un'evidenza $E=e$, l'approccio standard prevede l'**Algoritmo di Eliminazione di Variabili**. Esso evita le computazioni ripetute dell'enumerazione pura trasformando le sommatorie globali in prodotti locali di fattori tramite proprietà distributiva:

$$P(X \mid E=e) = \alpha \sum_{y \in Y} \prod_{i=1}^n P(X_i \mid Parents(X_i))$$

dove $Y$ è l'insieme delle variabili nascoste (nuisance variables) da marginalizzare (_summing-out_) e $\alpha$ è la costante di normalizzazione.

Tuttavia, l'inferenza esatta è **NP-hard**. Il problema dell'inferenza nelle reti a connessioni multiple ha complessità esponenziale: l'algoritmo è dominato dalla dimensione del fattore più grande generato durante l'esecuzione, la quale cresce esponenzialmente con la _treewidth_ del grafo, rendendo il modello statico inefficiente per topologie aziendali complesse.

## 3. Estensione Avanzata 1: Le Reti Bayesiane Dinamiche (DBN)

### 3.1. Modellazione della dimensione temporale (Modelli di Markov)

Per modellare l'evoluzione di un attacco APT che attraversa diverse fasi della _Kill Chain_ (es. Accesso Iniziale $\to$ Movimento Laterale $\to$ Cifratura), il formalismo statico si rivela insufficiente, in quanto incapace di catturarne le dipendenze sequenziali.

Estendiamo la formulazione introducendo le **Reti Bayesiane Dinamiche (DBN)**. Una DBN è un modello grafico probabilistico che rappresenta una sequenza temporale di variabili casuali, consentendo di modellare processi stocastici non stazionari o stazionari attraverso passi temporali discreti (time slices) indicizzati da $t = 0, 1, 2, \dots$.

Sia $\mathbf{X}_t$ l'insieme delle variabili di stato nascoste (unobserved) al tempo $t$ e sia $\mathbf{E}_t$ l'insieme delle variabili di evidenza (osservabili) al medesimo istante. La modellazione e il funzionamento di una DBN poggiano su due assunzioni fondamentali che ne garantiscono la trattabilità matematica:

1. **Ipotesi di Markov del Primo Ordine:** Si assume che lo stato corrente del sistema al tempo $t$ dipenda _esclusivamente e interamente_ dallo stato immediatamente precedente al tempo $t-1$, risultando condizionalmente indipendente da tutta la storia passata del processo. Formalmente, la legge di transizione si riduce a:
    
    $$P(\mathbf{X}_t \mid \mathbf{X}_0, \mathbf{X}_1, \dots, \mathbf{X}_{t-1}) = P(\mathbf{X}_t \mid \mathbf{X}_{t-1})$$
    
2. **Stazionarietà del Processo (Modello di Transizione Omogeneo):** Si assume che le leggi che governano i cambiamenti di stato dell'ambiente non mutino al variare del tempo. Di conseguenza, per ogni valore di $t$, la probabilità condizionata $P(\mathbf{X}_t \mid \mathbf{X}_{t-1})$ è definita dalla medesima struttura di rete e dalle medesime tabelle di probabilità.
    

Grazie a queste assunzioni, per rappresentare un processo su un orizzonte temporale infinito non è necessario definire infinite reti, ma è sufficiente specificare la distribuzione di probabilità congiunta globale per un intervallo generico, la quale si scompone come:

$$P(\mathbf{X}_0, \mathbf{X}_1, \dots, \mathbf{X}_T, \mathbf{E}_1, \dots, \mathbf{E}_T) = P(\mathbf{X}_0) \prod_{t=1}^T P(\mathbf{X}_t \mid \mathbf{X}_{t-1}) P(\mathbf{E}_t \mid \mathbf{X}_t)$$

### 3.2. Struttura a due fette di tempo (2-TBN) applicata alla Kill Chain

Il meccanismo operativo con cui una DBN definisce la transizione temporale prende il nome di **Rete di Transizione a Due Fette di Tempo (2-TBN)**, indicata con $B_{\to}$. Una 2-TBN è un frammento di grafo orientato aciclico che definisce la distribuzione condizionata $P(\mathbf{X}_t \mid \mathbf{X}_{t-1})$ per ogni coppia di fette adiacenti. Essa contiene i nodi appartenenti alla fetta temporale precedente $t-1$ (che agiscono esclusivamente come variabili condizionanti e non hanno genitori nella 2-TBN) e i nodi della fetta corrente $t$.

La semantica probabilistica della 2-TBN si traduce nella seguente scomposizione del modello di transizione:

$$P(\mathbf{X}_t \mid \mathbf{X}_{t-1}) = \prod_{i=1}^n P(X_{i,t} \mid Parents(X_{i,t}))$$

dove i genitori del nodo $X_{i,t}$ (ossia la $i$-esima variabile al tempo $t$) possono essere sia nodi della stessa slice $t$ (**archi intra-fetta**, che catturano le relazioni causali istantanee), sia nodi della slice precedente $t-1$ (**archi inter-fetta**, che catturano l'evoluzione temporale).

**Esempio di applicazione alla Cyber Kill Chain:** Consideriamo la modellazione del ciclo di vita di un ransomware avanzato all'interno di un host enterprise. Definiamo i componenti della nostra 2-TBN:

- **Variabile di Stato Secreta ($S_t$):** Rappresenta lo stato interno dell'attacco sul sistema. Il suo dominio discreto ordinato è $Dom(S_t) = \{\text{Sano}, \text{Privilegi\_Ottenuti}, \text{Cifratura}\}$.
    
- **Variabili di Evidenza Osservabili ($\mathbf{E}_t$):** Rappresentano i log raccolti dall'EDR: $E_{1,t} = \text{Connessioni\_Anomale}$ e $E_{2,t} = \text{IO\_Disco\_Anomalo}$ (entrambe a dominio booleano $\{T, F\}$).
    

Nel grafo della 2-TBN, inseriamo un **arco inter-fetta** $S_{t-1} \to S_t$. Questo arco esprime la dinamica temporale dell'attacco: se l'attaccante si trovava nello stato $\text{Privilegi\_Ottenuti}$ al tempo $t-1$, la CPT al tempo $t$ mostrerà una probabilità vicina all'85% di transire nello stato $\text{Cifratura}$ (escalation).

Parallelamente, definiamo gli **archi intra-fetta** $S_t \to E_{1,t}$ e $S_t \to E_{2,t}$. Essi modellano il fatto che se lo stato segreto corrente $S_t$ è $\text{Cifratura}$, l'evidenza $E_{2,t}$ ($\text{IO\_Disco\_Anomalo}$) passerà a $True$ con probabilità estremamente elevata, agendo da sintomo immediato dello stato nascosto.

## 4. Estensione Avanzata 2: Teoria delle Decisioni e Risposta Ottima

### 4.1. Estensione a Rete di Decisione e Teoria dell'Utilità

Fornire una stima probabilistica dello stato del sistema non è sufficiente: un agente di sicurezza intelligente deve saper scegliere l'azione difensiva ottimale. Per fare ciò, introduciamo la **Teoria dell'Utilità** e le **Reti di Decisione** (note anche come Diagrammi di Influenza).

#### Cos'è la Teoria dell'Utilità?

La Teoria dell'Utilità fornisce un formalismo assiomatico per modellare le preferenze di un agente in condizioni di incertezza. Essa assume che ogni stato del mondo possa essere mappato in un numero reale che ne esprime l'appetibilità per l'agente. Tale funzione di scoring prende il nome di **Funzione di Utilità $U(s)$**.

L'assioma cardine della teoria stabilisce che un agente razionale, di fronte a molteplici alternative stocastiche, deve selezionare l'azione che massimizza il valore atteso della sua utilità (**Principio della Massima Utilità Attesa - MEU**), calcolato pesando l'utilità di ogni possibile esito per la probabilità che quell'esito si verifichi.

#### Cos'è e come funziona una Rete di Decisione?

Una Rete di Decisione è un'estensione formale delle Reti Bayesiane che integra i meccanismi di scelta razionale sovrapponendo nodi decisionali e nodi di valore alla struttura probabilistica esistente. Graficamente e strutturalmente, introduce tre categorie di nodi distinte:

1. **Nodi Casuali (ovali):** Rappresentano le variabili stocastiche del sistema (sia nascoste che osservabili), esattamente come nelle Reti Bayesiane tradizionali. Ciascuno possiede una CPT.
    
2. **Nodi Decisione (rettangoli):** Rappresentano l'insieme delle azioni mutualmente esclusive che l'agente ha il potere di intraprendere direttamente. Questi nodi non hanno una CPT associata; il loro valore viene impostato dall'agente durante la valutazione delle diverse alternative. Nel nostro caso, definiamo il nodo decisionale $D_t$ con $Dom(D_t) = \{\text{Nessuna}, \text{Isola\_Host}, \text{Isola\_Sotto-rete}\}$.
    
3. **Nodi Utilità/Valore (rombi):** Rappresentano la funzione di utilità dell'agente. I suoi genitori sono i nodi che determinano direttamente il costo o il beneficio dell'esito, tipicamente il nodo di stato reale $S_t$ e il nodo di decisione $D_t$. Non possiede valori di probabilità, ma una tabella deterministica (Utility Table) che mappa ogni combinazione di stato-azione in uno scalare $U \in \mathbb{R}$.
    

#### Meccanismo di Inferenza e Scelta:

Sia $\mathbf{e}$ l'insieme delle evidenze correnti estratte dai sensori. Per selezionare l'azione ottima $d^*$, la rete esegue i seguenti passi computazionali:

1. Calcola l'inferenza probabilistica sulla variabile di stato nascosta data l'evidenza, ottenendo la distribuzione _a posteriori_ $P(S_t \mid \mathbf{e})$.
    
2. Valuta l'**Utilità Attesa** $EU(d \mid \mathbf{e})$ di ogni singola azione disponibile $d \in Dom(D_t)$, integrando rispetto all'incertezza dello stato:
    
    $$EU(d \mid \mathbf{e}) = \sum_{s \in Dom(S_t)} P(S_t = s \mid \mathbf{e}) \times U(S_t = s, D_t = d)$$
    
3. Applica il criterio MEU per emettere il comando di difesa: $d^* = \arg\max_{d} EU(d \mid \mathbf{e})$.
    

**Definizione della Matrice di Utilità per il Ransomware:** La tabella di utilità deve riflettere analiticamente il trade-off operativo tra la business continuity e la severità del danno da cifratura:

|**Stato Reale (St​)**|**Azione Difensiva (Dt​)**|**Utilità (U)**|**Razionale Operativo**|
|---|---|---|---|
|**Cifratura**|`Nessuna`|$-100.000$|**Disastro catastrofico:** Ransomware attivo, perdita totale dei dati e blocco aziendale.|
|**Cifratura**|`Isola_Host`|$-1.000$|**Contenimento riuscito:** Costo limitato al ripristino dell'endpoint infetto.|
|**Sano**|`Isola_Host`|$-500$|**Falso Positivo costoso:** L'host è sano, ma l'isolamento interrompe il lavoro dell'utente.|
|**Sano**|`Nessuna`|$0$|**Stato Nominale Ottimale:** Massima efficienza, nessun costo applicato.|

### 4.2. Interazione Difensore-Attaccante come Gioco di Stackelberg

Nei domini di cyber-sicurezza, considerare l'ambiente come una controparte passiva e puramente stocastica (la "Natura") è un'assunzione fallimentare. Il ransomware APT è guidato da un attaccante umano razionale che adatta la propria strategia per aggirare i controlli. Modelliamo questa interazione strategica evolvendo l'architettura in un **Gioco di Stackelberg della Sicurezza (SSG)**.

#### Cos'è un Gioco di Stackelberg della Sicurezza?

Un Gioco di Stackelberg è un modello matematico della teoria dei giochi che descrive uno scenario di competizione asimmetrica sequenziale non cooperativa. Nel contesto della sicurezza informatica, il gioco vede la partecipazione di due attori razionali con funzioni di utilità contrastanti:

1. **Il Leader (Difensore / Agente AI):** Muove per primo. Non potendo prevedere il momento esatto dell'attacco, deve impegnarsi formalmente in una strategia di protezione a lungo termine. La sua mossa consiste nel definire una **strategia mista** $\mathbf{x}$, ovvero una distribuzione di probabilità sull'allocazione delle proprie risorse di monitoraggio limitate (es. decidere con quale frequenza ispezionare un host o quale soglia di sensibilità impostare per i nodi della Rete Bayesiana).
    
2. **Il Follower (Attaccante):** Muove per secondo. Prima di sferrare l'attacco, esegue attività di ricognizione (reconnaissance), osservando empiricamente la configurazione e il comportamento del Leader (es. analizzando i tempi di risposta della rete, i leak dei sensori o scoprendo quali porte sono presidiate). Sulla base di questa osservazione, il Follower seleziona una **risposta ottima pura** $g^*$, ossia una sequenza di exploit all'interno della Kill Chain studiata per minimizzare la probabilità di attivare le evidenze ad alto costo per lui.
    

#### Come funziona il calcolo dell'Equilibrio di Stackelberg:

L'obiettivo dell'agente AI difensore è determinare la configurazione ottimale delle risorse che massimizzi il proprio pay-off, assumendo che l'attaccante risponda in modo perfettamente razionale alla strategia osservata. Questo problema di ottimizzazione si formula matematicamente tramite una programmazione lineare vincolata (o mista-intera):

$$\max_{\mathbf{x}} \sum_{i \in I} \sum_{j \in J} x_i \cdot g_j^*(\mathbf{x}) \cdot U_D(i, j)$$

$$\text{s.t. } \sum_{i \in I} x_i = 1, \quad x_i \ge 0 \quad \forall i \in I$$

$$\text{dove } g^*(\mathbf{x}) = \arg\max_{j \in J} \sum_{i \in I} x_i \cdot U_A(i, j)$$

In questa formulazione:

- $I$ e $J$ sono gli insiemi delle azioni del difensore e dell'attaccante.
    
- $U_D$ e $U_A$ sono le rispettive funzioni di pay-off.
    
- Il vincolo fondamentale impone che la risposta dell'attaccante $g^*(\mathbf{x})$ sia l'argomento che massimizza la sua utilità attesa data la strategia mista $\mathbf{x}$ implementata dal difensore.
    

**Integrazione logica con la DBN:** L'introduzione del Gioco di Stackelberg trasforma radicalmente la natura della nostra Rete Bayesiana Dinamica. Le probabilità di transizione a priori all'interno della 2-TBN (es. la probabilità che il sistema passi da `Sano` a `Privilegi_Ottenuti`) non sono più parametri statici derivati storicamente, ma diventano **funzioni matematiche dipendenti dall'equilibrio calcolato**. Se l'agente AI alloca più risorse di monitoraggio su una specifica fase, l'attaccante devierà il proprio cammino d'attacco per evitare la cattura, modificando dinamicamente i vettori di probabilità della DBN in base al calcolo strategico di Stackelberg.

## 5. Implementazione Pratica (PoC in Python)

Il seguente script Python implementa la componente intra-fetta del sistema di sicurezza probabilistico descritto. Il codice definisce la struttura grafica, calcola l'inferenza esatta mediante l'algoritmo di Eliminazione di Variabili e implementa la logica decisionale basata sulla massimizzazione dell'utilità attesa (MEU).

Python

```
import pgmpy
from pgmpy.models import BayesianNetwork
from pgmpy.factors.discrete import TabularCPD
from pgmpy.inference import VariableElimination

print("[+] Inizializzazione del Modello di Sicurezza Probabilistico...")

# 1. Definizione della struttura della rete (Fetta di Tempo t)
# I nodi modellano lo stato nascosto e le evidenze anomale osservabili.
security_model = BayesianNetwork([('Infezione', 'Connessioni_Anomale'), 
                                  ('Infezione', 'IO_Disco_Anomalo')])

# 2. Definizione delle Tabelle di Probabilità Condizionata (CPT)
# P(Infezione) -> Dominio: [Sano (0), Compromesso (1)]
# Assumiamo una probabilità a priori di compromissione iniziale pari all'1%
cpd_infezione = TabularCPD(variable='Infezione', variable_card=2, 
                           values=[[0.99], [0.01]])

# P(Connessioni_Anomale | Infezione)
# Cardinalità: 2 (False=0, True=1). Evidenza condizionante: Infezione
cpd_connessioni = TabularCPD(variable='Connessioni_Anomale', variable_card=2,
                             values=[[0.95, 0.20],  # No allarme se Sano (0.95) / Se Compromesso (0.20)
                                     [0.05, 0.80]], # Allarme se Sano (0.05) / Se Compromesso (0.80)
                             evidence=['Infezione'], evidence_card=[2])

# P(IO_Disco_Anomalo | Infezione)
# Cardinalità: 2 (False=0, True=1). Evidenza condizionante: Infezione
cpd_disco = TabularCPD(variable='IO_Disco_Anomalo', variable_card=2,
                       values=[[0.98, 0.10],  # No allarme se Sano (0.98) / Se Compromesso (0.10)
                               [0.02, 0.90]], # Allarme se Sano (0.02) / Se Compromesso (0.90)
                       evidence=['Infezione'], evidence_card=[2])

# Associazione delle CPT definite all'oggetto strutturale del modello
security_model.add_cpds(cpd_infezione, cpd_connessioni, cpd_disco)

# Validazione formale della coerenza degli assiomi probabilistici del grafo
assert security_model.check_model()

# 3. Istanziazione dell'algoritmo di inferenza esatta (Variable Elimination)
inference = VariableElimination(security_model)

print("\n--- SIMULAZIONE IN TEMPO REALE ---")

# SCENARIO A: Stato Nominale di Monitoraggio
res_A = inference.query(variables=['Infezione'], evidence={'Connessioni_Anomale': 0, 'IO_Disco_Anomalo': 0})
print("\nScenario A (Nessun sintomo rilevato):")
print(res_A)

# SCENARIO B: Presenza di anomalie di rete isolate (Segnale Debole)
res_B = inference.query(variables=['Infezione'], evidence={'Connessioni_Anomale': 1})
print("\nScenario B (Rilevate connessioni sospette - Segnale Debole):")
print(res_B)

# SCENARIO C: Correlazione di events multipli (Evidenza Schiacciante)
res_C = inference.query(variables=['Infezione'], evidence={'Connessioni_Anomale': 1, 'IO_Disco_Anomalo': 1})
print("\nScenario C (Connessioni di rete anomale + Scrittura intensiva su disco):")
print(res_C)

# 4. Logica Decisionale basata sul calcolo dell'Utilità (MEU semplificata)
prob_compromesso = res_C.values[1]
print(f"\n[Analisi Decisionale] Probabilità di compromissione calcolata: {prob_compromesso:.4f}")

# Soglia derivata analiticamente dalla massimizzazione della funzione di utilità attesa
soglia_isolamento = 0.50
if prob_compromesso > soglia_isolamento:
    print(">> AZIONE DETECTED: [ INTERVENTO AUTOMATICO - ISOLAMENTO HOST DALLA RETE ] <<")
else:
    print(">> AZIONE DETECTED: [ PROSEGUI MONITORAGGIO SILENTE ] <<")
```

## 6. Considerazioni Critiche e Frontiere della Ricerca

### 6.1. Trattabilità computazionale: dall'Inferenza Esatta all'Inferenza Approssimata

Nonostante l'algoritmo di Eliminazione di Variabili risulti matematicamente elegante per l'inferenza esatta su grafi isolati, la sua applicazione in contesti di monitoraggio enterprise real-time fallisce a causa della complessità **NP-hard**. All'espandersi della dimensione temporale ($t \to \infty$) e al crescere del numero di host monitorati simultaneamente, la rete genera connessioni multiple parallele. La dimensione dei fattori intermedi nell'eliminazione di variabili cresce in modo esponenziale ($O(2^n)$), introducendo ritardi computazionali (latenza) inaccettabili in un dominio in cui il ransomware cifra file ad altissima velocità.

Per preservare l'operatività in tempo reale a fronte di reti complesse, la ricerca moderna adotta paradigmi di **Inferenza Approssimata**, i quali rinunciano alla precisione analitica assoluta in favore di tempi di risposta rapidi e deterministici.

#### Campionamento MCMC (Markov Chain Monte Carlo)

I metodi MCMC sono una classe di algoritmi per il campionamento da distribuzioni di probabilità ad alta dimensionalità. Invece di calcolare analiticamente l'intera sommatoria dello spazio degli stati per ottenere il fattore di normalizzazione $\alpha$, gli algoritmi MCMC costruiscono una catena di Markov il cui stato stazionario coincide con la distribuzione _a posteriori_ desiderata.

L'algoritmo d'elezione è il **Gibbs Sampling**. Il suo funzionamento si articola nei seguenti passi:

1. Si assegna un valore iniziale casuale a tutte le variabili non osservate (nascoste) della rete, definendo lo stato corrente della catena.
    
2. A ogni iterazione, si seleziona una singola variabile nascosta $X_i$ alla volta e si campiona il suo nuovo valore condizionatamente ai valori correnti di tutte le altre variabili nel modello.
    
3. Proprietà fondamentale: per calcolare questa probabilità locale, non serve esplorare l'intera rete, ma basta valutare la **Coperta di Markov** del nodo $X_i$, definita come l'insieme dei suoi genitori, dei suoi figli e dei co-genitori dei suoi figli. Questo rende il calcolo estremamente locale e leggero.
    
4. Dopo un periodo iniziale di assestamento (_burn-in_), i campioni generati riflettono fedelmente la distribuzione reale e consentono di approssimare le probabilità per semplice conteggio statistico delle frequenze.
    

#### Particle Filtering (Sequential Monte Carlo)

Per i modelli temporali come le DBN, l'algoritmo di approssimazione standard è il **Particle Filtering**. Questo metodo traccia l'evoluzione delle variabili nascoste nel tempo rappresentando la distribuzione di probabilità ad ogni istante $t$ tramite un insieme di $N$ campioni discreti chiamati **particelle**. Ogni particella costituisce una "fotografia" o ipotesi concreta dello stato del mondo (es. la particella 1 assume che l'host sia sano, la particella 2 che sia compromesso).

Il funzionamento del filtro si sviluppa in un ciclo iterativo a tre fasi per ogni intervallo temporale:

1. **Propagazione (Prediction):** L'algoritmo prende ciascuna particella del tempo $t-1$ e la fa evolvere al tempo $t$, campionando il nuovo stato potenziale dalla CPT di transizione temporale $P(\mathbf{X}_t \mid \mathbf{X}_{t-1})$ definita nella 2-TBN.
    
2. **Pesatura (Correction):** Non appena giungono le evidenze reali dai sensori ($\mathbf{E}_t = \mathbf{e}_t$), l'algoritmo calcola un **peso di importanza** per ciascuna particella. Il peso è proporzionale alla verosimiglianza dell'evidenza data l'ipotesi della particella, calcolato come $w = P(\mathbf{E}_t = \mathbf{e}_t \mid \mathbf{X}_t)$. Le particelle le cui ipotesi contrastano con i dati dei log ricevono un peso prossimo a zero.
    
3. **Ricampionamento (Resampling):** Viene generata una nuova popolazione di $N$ particelle estraendo con rimpiazzo dalle vecchie particelle con una probabilità proporzionale al loro peso. Le ipotesi incoerenti vengono eliminate, mentre quelle ad alta verosimiglianza si moltiplicano.
    

Questo meccanismo vincola la complessità computazionale al numero fisso $N$ di particelle impostato dallo sviluppatore, garantendo un monitoraggio predittivo sub-secondario e scalabile.

### 6.2. Conclusioni

Il framework sviluppato dimostra che i pilastri teorici del ragionamento probabilistico in condizioni di incertezza — l'indipendenza condizionale, la scomposizione dei fattori grafici e la massimizzazione dell'utilità attesa — costituiscono l'architettura logica portante dei moderni sistemi di sicurezza.

L'estensione dinamica mediante DBN e la modellazione avversariale attraverso la teoria dei giochi di Stackelberg superano i vincoli di rigidità dei sistemi euristici tradizionali, delineando la transizione verso contromisure cibnetiche predittive, autonome e matematicamente ottimizzate contro le minacce informatiche persistenti.
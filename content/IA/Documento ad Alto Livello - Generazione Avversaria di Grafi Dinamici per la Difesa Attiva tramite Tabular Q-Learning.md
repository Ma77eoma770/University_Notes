## Contesto e Obiettivo

Il progetto affronta la mitigazione del traffico web malevolo (bot, scraper, crawler) introducendo un'architettura di Difesa Attiva basata su Tarpit dinamici. A differenza dei tradizionali sistemi di blocco perimetrale (firewall o honeypot statici) che si limitano a respingere l'attaccante inducendolo a mutare strategia, il nostro sistema mira a massimizzare lo spreco delle sue risorse (computazionali, di memoria e di tempo). L'obiettivo è intrappolare il bot in un labirinto fittizio in continua espansione, ritardando le sue operazioni.

A livello tecnico e di implementazione, il problema è stato astratto dai protocolli di rete e modellato come un processo di esplorazione avversaria su un grafo diretto. Sono presenti tre entità principali:

- **Un Ambiente di Simulazione (Engine):** Basato sulla libreria NetworkX, si occupa di istanziare la topologia del grafo, orchestrare i turni di gioco ed estrarre le metriche.
- **Un Agente Attaccante:** Implementato come un bot guidato dall'algoritmo di ricerca euristica A*. È dotato di parametri comportamentali (come la patience) che gli permettono di valutare dinamicamente la coerenza del grafo e decidere se continuare l'esplorazione, effettuare backtracking o abbandonare il sistema.
- **Un Agente Difensore:** Il nucleo intelligente del sistema. Addestrato tramite Tabular Q-Learning, ha il compito di alterare in tempo reale la topologia del grafo inserendo specifici "sotto-grafi" ingannevoli (esche, loop, vicoli ciechi).

L'interazione è formalizzata come un Processo Decisionale di Markov Parzialmente Osservabile (POMDP). Il Difensore non conosce i valori esatti interni dell'Attaccante, ma deve dedurre la politica ottimale di generazione delle trappole basandosi su stati approssimati, bilanciando l'inganno per non far fuggire prematuramente l'agente malevolo.

## Architettura e Componenti Principali

L'architettura è stata sviluppata interamente in python per focalizzarsi specialmente sulla logica dell'applicazione. Nel progetto sono presenti 3 componenti principali:

### L'ambiente (class environment)

Essenzialmente l'ambiente dove si scontrano i due agenti. Deve:

- **Gestire il grafo:** Utilizzando la libreria NetworkX deve creare un grafo valido (con un cammino da start a target, e euristica consistente) di una certa grandezza (parametri come densità degli archi, numero totale di nodi, distanza del percorso minima).
- **Scandire i turni di gioco:** Raccogliendo metriche e fungendo da tramite per il grafo, simulando le informazioni che i due agenti percepiscono (vd. Considerazioni su stato e osservabilità)
- **Dedurre uno Stato:** Da tutte le metriche che ha raccolto (posizione dell'attaccante, ultima trappola posizionata, stima della pazienza (vd. Difensore) deve poter dedurre uno stato (MDP/POMDP) da passare al difensore.

### L'attaccante (class attaccante)

Rappresenta il bot malevolo che vuole infiltrarsi nel grafo, il suo obiettivo è raccogliere più dati "preziosi" possibili facendosi guidare da un'euristica (nel mondo reale potrebbero essere parole come "password/admin/credenziali" nei file o delle cartelle di un sistema). A questo scopo nel grafo iniziale saranno presenti un nodo start (posizione iniziale) e un nodo target (con euristica 0), nel caso in cui il nodo target venga raggiunto il bot abbandonerà il grafo. Data la sua natura un algoritmo interessante da usare potrebbe essere A* ma non deve risultare "instancabile" ovvero deve poter distinguere in qualche modo tra un grafo/sotto-grafo coerente e da cui si possono estrarre informazioni importanti e uno fittizio (come quello che stiamo provando a creare noi) completamente inutile e da evitare. Per questo l'algoritmo sarà A* ma avrà un parametro aggiuntivo patience che all'inizio sarà settato a `max_patience` e pian piano diminuirà secondo dei criteri:

- **Euristica inconsistente:** se si accorge che l'euristica sta diminuendo in modo inconsistente (i nodi sono troppo belli per essere veri) perderà patience
- **Vicolo cieco:** se ha esplorato un lungo cammino di nodi che l'hanno portato ad un vicolo cieco perderà patience (passando da un nodo con euristica presumibilmente molto bassa (degenerazione in DFS) ad un nodo precedente con euristica media/alta)
- **Passo normale:** Ha esplorato un nodo adiacente con euristica (fin ora) ammissibile senza particolari osservazioni recupererà leggermente patience (o ne perderà pochissima).

Nel caso in cui la patience raggiunga 0 :

- **Backtrack:** tornerà al primo nodo precedente che ha patience > 0 e ha ancora dei vicini da visitare. Questo avviene se max_backtrack è > 0.
- **Give Up:** Se i tentativi di backtrack sono finiti (il bot ha perso speranza nel grafo) abbandona prematuramente l'esplorazione.

### Il difensore (class difensore)

Il suo obiettivo primario è far perdere più tempo possibile all'attaccante piazzando trappole preimpostate con un costo di operazione (?) (nodi con euristica minore per attirarlo fuori dal suo tracciato principale):

- **Bait:** Un singolo nodo con euristica molto allettante
- **Loop:** Una serie di nodi (e.g. 3) che si ricongiungono al primo nodo dalla divergenza. (Visto come death end da A*)
- **Death end:** un cammino di nodi (e.g. 3) che terminano con un vicolo cieco.
- **Wait:** Non fa nulla

Il difensore è addestrato tramite machine learning più in particolare con Tabular Q-learning per mappare l'efficacia delle sue azioni in base agli stati osservati (Q-Table), ad ogni turno:

- **$\epsilon$-greedy:** Decide se fare exploitation o exploration tramite un parametro \epsilon che diminuisce ad ogni episodio di un fattore epsilon_decay non scendendo mai oltre min_epsilon. In caso decida di esplorare farà un'azione casuale tra le 4 sopra. Altrimenti guarderà la Q-Table e deciderà secondo lo stato corrente che azione preferisce (in caso di pareggio dovrà definire una politica di decisione: casuale o la prima)
- **Step:** Esegue un'azione alternando (o non) il grafo.
- **Learning:** Alla fine del turno (dopo che anche il difensore avrà fatto la sua mossa) il difensore si ferma ad osservare le conseguenze delle sue azioni decidendo se l'azione che ha eseguito in questo turno e stata effettivamente utile a rallentare l'attaccante. Userà l'equazione di Bellman adattata a questo caso specifico:
> `new_q = current_q + learning_rate * (reward + gamma * max_future_q - current_q)`

dove:

- **new_q:** sarà lo stato aggiornato con ciò che ha imparato in questo turno.
- **current_q:** lo stato corrente con ciò che abbiamo imparato fino ad ora.
- **learning_rate:** un float che determina quanto pesano i nuovi apprendimenti su quelli vecchi.
- **reward:** il reward cumulato in questo turno.
- **gamma:** il fattore di sconto ovvero quanto ci interessano le ricompense future rispetto a quelle immediate (con valore 0.9 ad esempio predilige una pianificazione più lungimirante e meno miope).
- **max_future_q:** il potenziale stato futuro in cui è finito.

- **End-reward:** Alla fine il difensore verrà ricompensato proporzionalmente al numero di nodi che ha fatto sprecare all'attaccante. (e.g. 10*$\Delta$Step )

## Un turno

Ecco come si svolge nel dettaglio un singolo turno di gioco all'interno della simulazione:

1. **Rilevamento dello Stato Iniziale:** L'ambiente osserva la situazione corrente e deduce lo stato parzialmente osservabile (POMDP) da passare al Difensore. Questo stato sintetizza metriche fondamentali come la stima della pazienza dell'Attaccante (divisa in _bucket_), la distanza stimata dal nodo target e la presenza di trappole sul nodo attuale o su quelli adiacenti.
2. **Scelta e Azione del Difensore:** Basandosi sullo stato appena rilevato, il nucleo intelligente del Difensore consulta la propria politica (Q-Table) per decidere la mossa ottimale. L'azione scelta (posizionare un'esca, creare un loop, generare un vicolo cieco o attendere) viene immediatamente eseguita, alterando dinamicamente la topologia del grafo.
3. **Applicazione del Costo Operativo:** Per evitare che il Difensore posizioni trappole all'infinito, ogni alterazione del grafo comporta un "costo vivo". Questo si traduce in una penalità immediata sulla ricompensa del turno (ad esempio, -1.0 per un'esca o -2.5 per un vicolo cieco).
4. **Mossa e Risposta dell'Attaccante:** A questo punto, l'Attaccante reagisce alla nuova conformazione della rete. Guidato dal suo algoritmo di ricerca euristica (A*), espande la sua frontiera di nodi e decide se avanzare normalmente, compiere un salto, oppure effettuare un backtracking se si rende conto di essere in un vicolo cieco o se la sua pazienza è scesa a zero.
5. **Aggiornamento dell'Ambiente:** L'ambiente registra le conseguenze del movimento dell'Attaccante. A seconda che il bot abbia fatto un passo normale, un salto o sia tornato sui suoi passi, l'ambiente ricalcola la stima della pazienza residua e aggiorna la nuova distanza dal target. Questi nuovi dati formano il "Next State" (stato successivo).
6. **Valutazione della Terminazione e Ricompensa Finale:** Il turno si chiude verificando se l'Attaccante ha raggiunto il target, ha abbandonato la rete (GaveUp) o è rimasto bloccato (Stuck). Se la simulazione termina o l'Attaccante è stato costretto a fuggire, l'ambiente calcola un premio proporzionale al numero di nodi che il bot ha esplorato inutilmente (sprecando risorse). Il turno restituisce infine lo stato iniziale, l'azione, la ricompensa e lo stato finale per permettere l'apprendimento.
## Considerazioni su stato e osservabilità

I due agenti si muoveranno in uno spazio parzialmente osservabile per entrambi:

- Il difensore è a conoscenza dell'attaccante mentre l'attaccante deve definire se fidarsi o no del grafo affidandosi al suo parametro patience
- L'attaccante non conosce la topologia del grafo e lo esplora mano a mano che va avanti (fondamentalmente non può immediatamente accorgersi se un nodo è una trappola o meno ma deve basarsi su euristiche)
- La metrica patience è centrale (tutto il gioco si basa sull'allenare il difensore a far deviare abbastanza l'attaccante senza farlo andare via prematuramente) ma il difensore non può stimare con certezza il livello di pazienza rimanente quindi dovrà effettuare una stima (ad esempio con bucket in cui la pazienza, secondo il difensore, ricade entro un certo range/bucket).

Per quanto riguarda lo stato: patience è uno dei (se non il) parametro più importante che bisogna inserire nello stato da passare per il learning del difensore. Altri parametri da considerare:

- **DistanceToEnd:** quanto manca all'attaccante per arrivare al nodo target
- **OnTrap:** se l'attaccante è nello stato attuale su un nodo trappola.
- **Trappole adiacenti:** se l'attaccante ha trappole adiacenti nello stato attuale.

## Principali dubbi/incertezze (discutere in call)

- Stato abbastanza descrittivo? Curse of Dimensionality?
- Il sistema di patience e backtracking è coerente? Ci sono casi in cui l'attaccante può rimanere stuck? In quel caso come gestiamo l'episodio? Viene annullato?
- Costo delle operazioni?
- Come bilanciare i reward?
- Quando facciamo training il parametro dei grafi che dovrà rimanere costante sono i nodi o la lunghezza del cammino minimo? Bisogna avere questi punti fissi per un addestramento ottimale?
- A* è la scelta migliore? Nel grafo consideriamo tutti gli archi di costo 1 (costo di cambiare pagina in un sito web e sempre quello?), dati due nodi x,y con euristiche h_x e h_y, y è un'esca e (x,y) è l'arco che li collega, h_y = h_x - 2 per attirare correttamente l'attaccante rimanendo negli int o possiamo usare anche float per creare catene più lunghe?
- Bilanciamento degli strumenti del difensore contro il sistema di patience dell'attaccante corretto? Fair Game?
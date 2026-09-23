---
date:
  - 2026-01-15
tags:
  - Foster
---

> [!question] Perché?
> Quali sono le regole da seguire per partizionare il carico di lavoro fra i vari processi?

1. **Partizionamento** : si identificano delle attività di base indipendenti fra loro che possono essere eseguite in parallelo.
2. **Comunicazione** : determinare quali sono le attività stabilite nel punto precedente che per essere eseguite necessitano di uno scambio di messaggi.
3. **Aggregazione** : identificare le attività precedentemente stabilite che devono necessariamente essere eseguite in sequenza, ed aggregarle in un unica attività.
4. **Mapping** : assegnare ai vari processi le attività definite in precedenza in modo che il carico di lavoro sia uniformemente distribuito. Idealmente la comunicazione deve essere ridotta al minimo.

---

La struttura di un programma parallelo può essere definita secondo due pattern, si può dire che esistono due modi di parallelizzare un programma:
- **GPLS** (Globally Parallel, Locally Sequential) : L’applicazione vede diversi task sequenziali venire eseguiti in parallelo.
- **GSLP** (Globally Sequential, Locally Parallel) : L’applicazione segue uno specifico "flusso" di esecuzione sequenziale, di cui alcune parti vengono eseguite in parallelo.

### Esempi di GPLS

- **Single Program Multiple Data** : La logica dell’applicazione viene mantenuta in un unico eseguibile, tipicamente il programma segue la seguente struttura
	1. Inizializzazione del programma
	2. Ottenimento degli identificatori
	3. Esecuzione del programma in diverse ramificazioni in base ai core coinvolti
	4. Terminazione del programma
- **Multiple Program Multiple Data** : Quando la memoria da utilizzare è elevata è necessario suddividere il carico su più programmi, che spesso vengono eseguiti su differenti piattaforme.
	- **Master-Worker** : Ogni processo può essere
		- Worker - Esegue la computazione
		- Master - Gestisce il carico di lavoro e lo assegna ai processi worker, colleziona i risultati ottenuti da questi ultimi e si occupa spesso delle operazioni di I/O o interazione con l’utente.
	- **Map-Reduce** : Una versione modificata del paradigma Master-Worker, in cui i nodi worker eseguono due tipi di operazioni
		- Map : Esegue la computazione su un insieme di dati che risulta in un insieme di risultati parziali (ad esempio, esegue la somma su ogni elemento di un vettore)
		- Reduce : Colleziona i risultati parziali e ne deriva un risultato finale (ad esempio, somma tutti gli elementi di un vettore ottenendo un unico scalare)

### Esempi di GSLP

- **Fork-Join** : C’è un unico "padre" in cui avviene l’esecuzione, quando necessario, tale padre potrebbe eseguire una fork generando dei nodi figli, che eseguono la computazione per poi terminare, facendo si che il padre continui.
- **Loop-Parallelism** : Risulta estremamente semplice da utilizzare e viene spesso applicata quando un programma sequenziale deve essere adattato al multiprocesso. Consiste nel parallelizzare ogni esecuzione di un ciclo for , è necessario che le iterazioni però siano indipendenti fra loro.
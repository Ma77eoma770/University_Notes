---
date:
  - 2025-03-23
tags:
  - Pacchetti
  - Introduzione
  - Commutazione
  - Forwarding
---

## Definizioni


> [!info] Definizione: Pacchetto e Velocita di trasmissione
> Dato un messaggio m da trasferire tra due terminali, definiamo come pacchetti l’insieme di blocchi di L bit tali che m = {p1, . . . , pk}. Ogni pacchetto viene trasmesso nella rete ad una **velocità di trasmissione** R (anche detta larghezza di banda o capacità del collegamento).


> [!info] Definzione: Forwarding e Routing
> Le funzioni fondamentali di una rete si dividono in: 
> - **Forwarding o Switching (commutazione)**: ossia un’azione locale **tramite** cui vengono spostati i pacchetti in arrivo dal collegamento di ingresso del router al collegamento appropriato di uscita. Viene effettuato attraverso una local forwarding table, contenente gli indirizzi dei nodi locali.
> - **Routing (instradamento)**: ossia un’azione **globale** tramite cui vengono determinati i percorsi origine-destinazione seguiti dai pacchetti. Viene effettuato tramite algoritmi di instradamento

In particolare il **Forwarding (o commutazione)** puo avvenire in due modi:
- **Commutazione di pacchetto**:
	- La rete inoltra i pacchetti da un router all’altro attraverso i collegamenti presenti nell’instradamento dall’origine alla destinazione.
	- Una volta inviato, un pacchetto deve completamente raggiungere il nodo a cui sta attualmente venendo inviato prima di poter essere trasmesso al collegamento successivo (store & forward).
	- Se la velocità di trasmissione sul link di entrata supera la velocità di trasmissione di quello in uscita, i pacchetti verranno messi all’interno di una coda, in attesa di essere trasmessi sul link di uscita.
	- Se il buffer della coda raggiunge capienza massima, i pacchetti verranno scartati (**perdita di pacchetti**), per poi, se necessario, essere rinviati.
	
	![[Commutazione_pacchetti.PNG]]
- **Commutazione di circuito**:  ^b98bc4
	- La banda dei mezzi di trasmissione viene suddivisa in parti, riservando ognuna di essere ad una comunicazione tra un’origine ed una destinazione. 
	- Per via di tale suddivisione, il numero di utenti massimo della rete risulta essere limitato dal numero di suddivisioni.
	- La suddivisione può essere effettuata in due modalità:
		- **Frequency Division Multiplexing (FDM)**: dove le frequenze del mezzo di trasmissione vengono suddivise in bande di frequenza, ognuna di esse riservata ad una singola comunicazione, la quale può utilizzare al massimo la banda ad essa riservata.
		  
		  ![[Frequency_division_mux.PNG]]
		  
		- **Time Division Multiplexing (TDM)**: dove il tempo viene suddiviso in slot, ognuno di essi riservato ad una singola comunicazione, la quale può utilizzare l’intera banda del mezzo per il breve lasso di tempo dedicato.
		  
		  ![[Time_division_mux.PNG]]
		  

> [!warning] Attenzione!
> Nonostante la commutazione di pacchetto permetta l’accesso di un numero maggiore di utenti e non necessiti di stabilire una configurazione del collegamento, la presenza di una possibile perdita di pacchetti rende tale tipo di commutazione prettamente ottimo per trasmissioni "bursty", ossia intermittenti e con lunghi periodi di inattività.

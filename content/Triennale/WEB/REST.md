---
date:
  - 2025-10-14
tags:
  - WEB
  - REST
---
## Intro


> [!info] Definizione: REST (Representational state transfer)
> REST è uno stile architetturale per progettare applicazioni e servizi web loosely coupled.
> Spesso utilizzato per sviluppare Web API su HTTP.

> [!warning] Attenzione:
> REST è uno stile di design, non definisce dettagli di implementazione, ma si concentra su componenti e vincoli.
> 
> Un servizio RESTful implementa tutti i principi e vincoli di REST.

## Componenti fondamentali

- **Risorsa**: astrazione fondamentale dei dati in REST. 
	- Qualsiasi cosa può essere una risorsa: documenti, immagini, collezioni di risorse, ecc.
	- Più risorse possono mappare allo stesso valore.
- **Rappresentazione della risorsa**: stato corrente o desiderato della risorsa, composto da **dati e metadata**.
	- Il formato dei dati è detto media type (o MIME type).
- Lo **stato corrente** viene usato nelle risposte, mentre lo stato desiderato viene inviato nelle richieste che modificano la risorsa.
- **Client e server** scambiano dati e interagiscono con le risorse tramite le loro rappresentazioni.

## Principi e vincoli
### Stateless

> [!hint] Idea:
> Ogni richiesta deve contenere tutte le informazioni necessarie: il server non mantiene stato.

> [!success] Vantaggi:
> 1. **Visibilità**: ogni richiesta è autonoma.
> 2. **Affidabilità**: recupero da errori parziali semplice.
> 3. **Scalabilità**: meno coordinazione tra componenti.
### Cache

> [!hint] Idea
> Tutte le risposte devono essere cacheable (implicitamente o esplicitamente).

> [!success] Vantaggi (evidenti)
> Riduce il numero di interazioni e aumenta performance e scalabilità.

### Separazione dei ruoli (Client-Server)

> [!hint] Idea:
> Architettura client-server: client e server hanno ruoli distinti.

> [!info] Obiettivo:
> Decoupling tra interfaccia utente e storage dati.

> [!success] Vantaggi: 
> **Portabilità e scalabilità**, client e server possono evolvere indipendentemente.

### Interfaccia uniforme 

> [!hint] Principio chiave di REST:
> Tutte le interazioni usano stessa struttura base (protocollo, messaggio, ecc.).

> [!success] Vantaggi:
> Riduce personalizzazioni, ma semplifica il design e decoupling tra definizione e implementazione.

> [!warning] Vincoli per una uniform interface:
> 1. Identificazione delle risorse
> 2. Manipolazione delle risorse tramite rappresentazioni
> 3. Messaggi auto-descrittivi
> 4. Hypermedia come motore dello stato dell’applicazione (il client ha bisogno solo dell’URL iniziale)

### Sistema a livelli (Layered System)

> [!hint] Idea:
> Architettura può avere intermediari che: isolano, modificano, ri-inoltrano o incapsulano messaggi REST.

> [!success] Vantaggi: 
> Riduce complessità, decoupling dei servizi.

> [!error] Svantaggi: 
> Può ridurre il throughput complessivo, quindi bisogna bilanciare numero di livelli e performance.

> [!example] Esempi: 
> Caching intermediario, policy enforcement, incapsulamento di servizi legacy.

### Codice su richiesta (Code-on-Demand)

> [!hint] Idea:
> Estende funzionalità del client scaricando ed eseguendo codice.

> [!success] Pro:
> Riduce complessità del client, aumenta flessibilità.

> [!error] Contro:
> Riduce visibilità del sistema.

> [!bug] Principio opzionale, spesso non implementato.



---
date:
  - 2025-10-11
tags:
  - WEB
  - API
---
## Definizione

> [!info] Definizione: API
> Un'API (**Application Programming Interface**) è la definizione delle interazioni consentite tra due parti di un software. L'API funge da contratto specificando come un pezzo di codice o un servizio può interagire con un altro. 

L'API definisce in dettaglio il "contratto" di interazione tra il **CONSUMER** (il [[HTTP#...anyway...|client]]) e il **PROVIDER** (il servizio). Essa specifica:
1. Le **richieste** possibili.
2. I **parametri** delle richieste.
3. I **valori di ritorno**.
4. Qualsiasi **forma di dato** richiesto ([[Json & Yaml#Json (JavaScript Object Notation)|JSON]],XML,[[Json & Yaml#Yaml (YAML Ain't Markup Language)|YAML]]).

## Benefici

Ci sono degli indiscutibili vantaggi nell'usare un'API:
- **Interfaccia Esplicita**: Definisce chiaramente le aspettative e le modalità di interazione.
- **Contratto Infrangibile**: Stabilisce un insieme di regole che entrambe le parti devono rispettare.
- **Information Hiding (Occultamento delle Informazioni)**: La logica interna del Provider (il back-end) rimane nascosta al Consumer. Il client deve conoscere solo l'interfaccia.

## Tipi di API

Esistono diverse categorie di API in base alla loro posizione e funzione:
- **API Locali**:
	- API per linguaggi di programmazione (libreria standard di python)
	- API dell'OS
	- ...
- **API Remote (Web API)**:
	- Interfacce di programmazione basate su protocolli di rete (tipicamente HTTP) come le API RESTful.

| Categoria         | Descrizione                                                | Restrizioni                                                                |
| ----------------- | ---------------------------------------------------------- | -------------------------------------------------------------------------- |
| **API Private**   | Destinate all'uso interno di un'aziona o un sistema chiuso | L'accesso è limitato ai componenti interni                                 |
| **API Pubbliche** | Disponibili per l'uso da parte del pubblico                | L'accesso può essere limitato solo ad alcuni utenti tramite **API Tokens** |

## Documentazione e Definizione

> [!hint] Per stabilire il contratto, l'API deve essere definita esplicitamente

La definizione può avvenire attraverso:
- **Documentazione** (testo,esempi,manuali)
- Tramite un **linguaggio di descrizione** standardizzato (permette generazione automatica di documentazione, codice client e validazione)

## OAS (OpenAPI Specification)

**OAS (OpenAPI Specification)** è il linguaggio di descrizione leader del settore per le API moderne basate su HTTP.
- **Indipendente dal fornitore (vendor neutral)**
- **Standard Industriale**

### Esempio di utilizzo

I file OpenAPI sono tipicamente scritti in formato **YAML** (data la sua leggibilità):

```yaml
openapi: 3.0.0
info:
	title: An example OpenAPI document
	description: |
		This API allows writing down marks on a Tic Tac Toe board and requesting 
		the state of the board of individual cells.
	version: 0.0.1
paths: () # Gli endpoin dell'API verrebbero definiti qui
```


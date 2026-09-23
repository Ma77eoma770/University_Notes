---
date:
  - 2025-10-10
tags:
  - HTTP
  - WEB
---
## Definizioni Iniziali
![[Web e Protocollo HTTP#^a5f4c8]]

![[Versioni di HTTP]]

## ...anyway...

Si regge tutto sul paradigma Client/Server in cui abbiamo:
- **User Agent (UA)**: ovvero qualsiasi programma client che avvia una richiesta (browser,app mobile, il frigo...).
- **Origin Server (O)**: ovvero un programma che può dare luogo a risposte autorevoli per una risorsa (sito web. telecamera per il traffico, etc..)

---

Una richiesta ha questa forma in cui viene specificato (in ordine):
1. **Linea di Richiesta**: metodo-HTTP, URI, versione-protocollo
2. **Campi di Intestazione della Richiesta**: self-explanatory
3. **Corpo del messaggio**: opzionale

```HTTP
GET /hello.txt HTTP/1.1
User-Agent: curl/7.64.1
Host: [www.example.com](https://www.example.com)
Accept-lenguage: en, it
```

---

Una risposta ha questa forma in cui viene specificato (in ordine):
1. **Stato di Completamento**: riguardante la richiesta
2. **Contenuto**: opzionale

```HTTP
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain
Hello World! My content includes a trailing CRLF.
```

---

Tra UA e O ci possono essere degli intermediari come Proxy, gateway, tunnel, ...
Oppure anche una cache che si comporta esattamente come una cache si comporta (nice)

## Metodi HTTP

Un elenco contiene (circa):
- GET
- HEAD
- POST
- PUT
- DELETE
- CONNECT
- OPTIONS
- TRACE
- PATCH

Esistono delle proprietà:
1. **Safe**: Un metodo che non ha effetti collaterali sulla risorsa (**sola lettura**). Può tuttavia cambiare lo stato del server i altri modi (log) GET, HEAD, OPTIONS, TRACE.
2. **Idempotent**: Richieste identiche multiple con un metodo idempotente hanno lo stesso effetto di una singola richiesta. PUT, DELETE.
3. **Cacheable**: Metodi che possono consentire a una cache di archiviare e usare una risposta. GET, HEAD, POST (in certi casi).

### PUT

**Crea una nuova risorsa** (specificata nella richiesta). **Sostituisce una risorsa** (quando l'URI esiste, la sovrascrive con la rappresentazione nel payload PUT).

```HTTP
PUT /course-descriptions/web-and-software-architecture
```

**IDEMPOTENTE**

> [!hint] Aggiorna una risorsa, se non esiste la crea
### GET

Richiede una rappresentazione dello stato di una risorsa

```HTTP
GET /course-descriptions/web-and-software-architecture
```

**SAFE, CACHEABLE** 

> [!hint] Display di una risorsa
### POST

Crea o modifica un subordinato della risorsa indicata nell'URI. L'URI identifica
la risorsa che gestirà la richiesta.

```HTTP
POST /announcements/
```

**CACHEABLE**

> [!hint] Invia dati all' O per creare una risorsa, se già esiste la modifica

### DELETE

Richiede di rimuovere la risorsa target e la sua funzionalità attuale.

```HTTP
DELETE /courses/web-and-software-architecture
```

**IDEMPOTENT**

> [!hint] Elimina una risorsa

---
## Messaggi di Stato

![[Messaggi di richiesta e risposta#^0e53d8]]

| Codice di stato     | Descrizione   |
| ------------------- | ------------- |
| 1xx (Informational) | Hold on       |
| 2xx (Successful)    | Here you go   |
| 3xx (Redirection)   | Go away       |
| 4xx (Client Error)  | You fucked up |
| 5xx (Server Error)  | I fucked up   |

---

## cURL

| Metodo        | Scopo                                                             | Sintassi cURL                                                                                                                      |
| ------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| GET (default) | Scarica il contenuto della risorsa                                | curl https://swapi.dev/api/people/1/                                                                                               |
| HEAD          | Richiede solo le intestazioni (Header), non il corpo.             | curl -I https://swapi.dev/api/people/1/                                                                                            |
| POST          | Invia dati per creare una nuova risorsa                           | curl -X POST -H "Content-Type:<br>application/json" -d '{"title": "Test"}'<br>https://jsonplaceholder.typicode.com/posts           |
| PUT           | Invia dati per sostituire una risorsa esistente (**idempotente**) | curl -X PUT -H "Content-Type:<br>application/json" -d '{"id": 1,"title":"New Title"}' https://jsonplaceholder.typicode.com/posts/1 |
| DELETE        | Elimina la risorsa                                                | curl -X DELETE https://jsonplaceholder.typicode.com/posts/1                                                                        |
| HEADERS       | Visualizza tutte le intestazioni (opzione per GET)                | curl -i https://swapi.dev/api/people/1/                                                                                            |

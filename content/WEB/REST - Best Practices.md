---
date:
  - 2025-10-21
tags:
  - WEB
  - REST
---
## Nomi vs. Verbi 

Negli URI è buona pratica usare sostantivi, perché rappresentano risorse, e le risorse sono oggetti.
Evitare quindi di usare verbi nell’URI: l’azione da eseguire si indica tramite il metodo HTTP (come GET, POST, PUT, DELETE, ecc.).
In questo modo, si potrà eseguire azioni diverse sullo stesso URI, semplicemente cambiando il metodo HTTP.
In generale, usare nomi al singolare per indicare una singola risorsa (es. `http://example.com/users/admin`) e nomi al plurale per una collezione (es. `http://example.com/users/`).

## Gerarchia degli URI

È buona norma usare la barra “/” per rappresentare la struttura gerarchica delle risorse, e aggiungere la barra finale se stai indicando una collezione.

> [!info] Nota:
> Questo rende l’API più leggibile e gestibile man mano che cresce.

> [!example]
> Esempio: `http://example.com/users/{userid}/devices/{deviceid}`

## Codice HTTP corretto

Ogni errore dovrebbe restituire un codice di stato HTTP appropriato.
Ad esempio, se mancano parametri obbligatori o sono errati, usa 400 Bad Request.
Se si vuole dare più informazioni, si può aggiungere un JSON con i dettagli dell’errore.

## Query per filtrare e paginare

Quando si deve filtrare o suddividere i risultati, di usano le query (la parte dopo il “?” nell’URL).
No filtri, né nel corpo né nel percorso dell’URI.

> [!example] Esempio:
> `http://example.com/managed-devices/?region=USA`

## No corpo in GET e DELETE

Anche se lo standard HTTP lo permette in certi casi, in REST non si fa.
Aggiungere un corpo a GET o DELETE può creare comportamenti imprevisti, quindi è meglio evitarlo.

## Come gestire file binari (come immagini)

Non inserire file binari direttamente dentro JSON: rallenta tutto.
Se l’API gestisce solo un file (come una foto), invialo nel corpo e specifica il MIME type (image/png, image/jpeg, ecc.).

Se invece devi inviare anche altri dati, puoi:
- Per le **richieste**:
	1. Usare multipart/form-data per inviare immagine e dati insieme.
	2. Inviare prima l’immagine tramite un’API dedicata (es. /images/) e poi passare il link all’immagine nel JSON.

- Per le **risposte** (JSON con i dati più):
	1. Un URL completo all’immagine, oppure
	2. Un ID che permetta di scaricarla tramite un’altra API.

## No corpo per i filtri

Quando devi filtrare una collezione, usa la query string (es. ?latitude=...).
Non mettere mai i filtri nel corpo della richiesta o nel percorso.

> [!example] Esempio:
>  ```yaml
>  /fountains/:
> 	  get:
> 	    parameters:
> 	      - name: latitude
> 	        in: query
> 	        required: false
>    ```


## Collezione vs. Elemento

Se ad esempio in un’app come Twitter vuoi far sì che Alice silenzi Bob, non usare un endpoint tipo `/users/{userid}/mute`.

> [!hint] Idea
> Non ha senso creare una “risorsa mute” per Bob, perché Bob non è silenziato da tutti, ma solo da Alice.

> [!success] Il modo corretto è:
> `/users/{userid}/muted/{mutedid}`

In questo modo:

1. Un utente può silenziare un altro solo una volta;
2. Per annullare, basta un DELETE sullo stesso percorso;
3. Ogni utente ha la sua lista di utenti silenziati.

## No multipart/form-data nelle risposte

Multipart/form-data serve per inviare file e dati insieme, ma non va usato nelle risposte.
Se devi restituire più contenuti, usa JSON con riferimenti o link ai file.

## PUT vs. POST per idempotenza

Se vuoi che una chiamata ripetuta produca sempre lo stesso risultato, usa PUT, non POST.
Ad esempio, per aggiornare una risorsa o per creare una nuova risorsa con un identificatore preciso.

> [!hint] Esempio
> Cambiare il nome utente? Meglio con PUT, non POST.


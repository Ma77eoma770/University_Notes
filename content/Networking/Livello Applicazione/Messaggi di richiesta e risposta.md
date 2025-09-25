---
date:
  - 2025-03-30
tags:
  - Richiesta
  - Risposta
  - HTTP
  - Livello_applicazione
---

I messaggi HTTP di richiesta e risposta vengono formattati un formato leggibile dall’uomo (in particolare, in codice ASCII).
## Richiesta

Ogni messaggio di **richiesta HTTP** viene strutturato nel seguente modo: 
1. **Una riga di richiesta**, composta dal metodo utilizzato, il path richiesto e la versione di HTTP utilizzata, seguiti da un carattere di ritorno a capo, ossia **\r**, ed un carattere di avanzamento di riga, ossia **\n** I metodi principali inseribili all’interno della riga di richiesta sono: 
	- **Metodo GET**: utilizzato per l’invio di dati al server, i quali vengono inseriti all’interno dell’URL a seguito di un carattere ’?’ (es: www.mysite.com/search?user=myuser) 
	- **Metodo POST**: utilizzato per l’invio di dati al server, i quali vengono aggiunti all’interno del body del messaggio (rimanendo quindi parzialmente offuscati all’utente) 
	- **Metodo HEAD**: utilizzato per richiedere solo l’header di risposta che verrebbe restituito dalla destinazione a seguito di una richiesta GET 
	- **Metodo PUT**: utilizzato per caricare un nuovo file o sostituirne uno esistente all’interno della destinazione (non più utilizzato poiché estremamente insicuro) 
2. **Un header (o intestazione)**: composto da varie linee contenenti informazioni utili alla connessione. Alcuni esempi di campi inseribili all’interno di un header di richiesta sono: ^4a8b71

|   Campo Header    |                              Descrizione                              |
| :---------------: | :-------------------------------------------------------------------: |
|    User-agent     |                 Indica il programma client utilizzato                 |
|      Accept       | Indica il formato dei contenuti che il client e in grado di accettare |
|  Accept-charset   |       Famiglia di caratteri che il client e in grado di gestire       |
|  Accept-encoding  |               Schema di codifica supportato dal client                |
|  Accept-language  |                    Linguaggio preferito dal client                    |
|   Authorization   |              Indica le credenziali possedute dal client               |
|       Host        |                   Host e numero di porta del client                   |
|       Date        |                       Data e ora del messaggio                        |
|      Upgrade      |           Specifica il protocollo di comuncazione preferita           |
|      Cookie       |                     Comunica un cookie al server                      |
| If-Modified-Since |    Invia il documento solo se e piu recente della data specificata    |
|      Max-Age      |          Specifica il tempo di vita di un cookie in secondi           |

^4a6505

3. **Un body (o contenuto)**: ossia il vero contenuto del messaggio da inviare (solitamente vuoto a meno dell’uso del metodo POST)

![[Struttura_richiesta_http.PNG]]

> [!example] Esempio:
> ```
> GET /index.html HTTP/1.1\r\n 
> Host: www-net.cs.umass.edu\r\n 
> User-Agent: Firefox/3.6.10\r\n 
> Accept: text/html,application/xhtml+xml\r\n 
> Accept-Language: en-us,en;q=0.5\r\n 
> Accept-Encoding: gzip,deflate\r\n 
> Accept-Charset: ISO-8859-1,utf-8;q=0.7\r\n 
> Keep-Alive: 115\r\n 
> Connection: keep-alive\r\n 
> \r\n
## Risposta

Ogni messaggio di **risposta HTTP** viene strutturato nel seguente modo: 

1. Una **riga di stato**: composta dalla versione di HTTP utilizzata, un **codice di status** e una **frase di status** che descrive in breve il codice di status.
   I codici di status si dividono in 5 categorie: 
	   - Codici 1xx, indicanti che la risposta ricevuta contiene solamente informazioni (es: 100 Continue indica che il server è pronto a ricevere la richiesta del client).
	   - Codici 2xx, indicanti che la richiesta effettuata è andata a buon fine (es: 200 OK indica che la richiesta ha avuto successo e l’oggetto richiesto è stato trovato, 204 No Content indica che la richiesta ha avuto successo ma l’oggetto richiesto non contiene nulla al suo interno).
	   - Codici 3xx, indicanti che è stato effettuato un reindirizzamento a seguito della richiesta effettuata (es: 301 Moved Permanently indica che l’oggetto richiesto possiede un path diverso da quello richiesto, reindirizzando automaticamente tutte le richieste successive del client).
	   - Codici 4xx, indicanti un errore nella richiesta del client (es: 403 Forbidden indica che il client non possiede i requisiti per accedere all’oggetto richiesto, 404 Not Found indica che l’oggetto richiesto non esiste ).
	   - Codici 5xx, indicanti un errore per cui il server non è riuscito a completare la richiesta (es: 500 Internal Server Error indica un errore sconosciuto all’interno del server, 503 Service Unavailable indica che il server è attualmente non disponibile)
2. Un header (o intestazione), composto da varie linee contenenti informazioni utili alla risposta. Alcuni esempi di campi inseribili all’interno di un header di risposta sono:

|   Campo Header   |                        Descrizione                        |
| :--------------: | :-------------------------------------------------------: |
|       Date       |                    Data e ora attuale                     |
|     Upgrade      |    Specifica il protocollo di comunicazione preferito     |
|      Server      |           Indica il programma server utilizzato           |
|    Set-Cookie    |   Il server richiede al client di memorizzare un cookie   |
| Content-Encoding |              Specifica lo schema di codifica              |
| Content-Language |       Specifica la lingua utilizzata nel documento        |
|  Content-Length  |             Indica la lunghezza del documento             |
|   Content-Type   |           Specifica la tipologia del documento            |
|     Location     | Chiede al client di inviare la richiesta ad un altro sito |
|  Last-modified   |  Fornisce data e ora dell'ultima modifica del documento   |
3. Un **body (o contenuto)**: ossia il vero contenuto del messaggio da restituire (in particolare, l’oggetto richiesto).

> [!example] Esempio:
> ```
> HTTP/1.1 200 OK\r\n 
> Date: Sun, 26 Sep 2010 20:09:20 GMT\r\n 
> Server: Apache/2.0.52 (CentOS)\r\n 
> Last-Modified: Tue, 30 Oct 2007 17:00:02 GMT\r\n 
> Accept-Ranges: bytes\r\n 
> Content-Length: 2652\r\n 
> Keep-Alive: timeout=10, max=100\r\n 
> Connection: Keep-Alive\r\n 
> Content-Type: text/html; charset=ISO-8859-1\r\n 
> \r\n 
> [document content...]
> [...]
> ```


---
date:
  - 2025-04-06
tags:
  - DNS
  - Gerarchia_DNS
  - Livello_applicazione
  - Resource_Record
---
Poiché il mapping DNS è distribuito su svariati server, dove in particolare nessuno di essi mantiene il mapping di tutti gli IP possibili (un IP corrisponde a 32 bit, dunque $2^{32}$ IP possibili), il database tramite cui viene realizzato il servizio DNS è gerarchico, seguendo la struttura di un albero:
- **Root Server**:
	- Radice dell’albero
	- Viene interrogato da qualsiasi server DNS che non sia in grado di risolvere il nome di un server TLD (ossia restituire l’IP legato ad esso) 
- **Server Top-Level Domain (TLD)**: 
	- Viene interrogato per risolvere il nome di un server DNS autoritativo 
	- Responsabili di domini come .com, .org, .net, ... e tutti i domini nazionali di primo livello, ossia .it, .uk, .fr, ... 
- **Server autoritativi (o di competenza)**:
	- Viene interrogato per risolvere il nome di un host pubblicamente accessibile, solitamente all’interno di un organizzazione 
	- Ogni organizzazione con host pubblicamente accessibili deve fornire i record DNS di pubblico dominio che mappano i nomi di tali host ai loro indirizzi IP 
- **Server DNS locali (o default name server)**: 
	- Non appartengono alla gerarchia. Ogni ISP ne è dotato 
	- Possiedono una cache locale delle recenti coppie di mappatura nome-indirizzo (potrebbero non essere aggiornate) 
	- Funge da proxy iniziale tra il client e il root server: se il nome non è nella cache del server locale, la richiesta viene inoltrata al root server

![[DNS_tree.PNG|720]]


> [!example]- Esempio
> 1. Il client vuole ottenere l’indirizzo IP dell’host www.amazon.com 
> 2. Viene contattato il server DNS locale dell’ISP di riferimento. Se il nome non viene risolto, si procede col passo successivo. 
> 3. Viene contattato il root server per trovare l’indirizzo IP del server TLD .com 
> 4. Viene contattato il server TLD .com per trovare l’indirizzo IP del server autoritativo amazon.com 
> 5. Viene contattato il server autoritativo amazon.com per trovare l’indirizzo IP dell’host www.amazon.com

Ogni volta che un server DNS viene a conoscenza di una mappatura, essa viene memorizzata all’interno della cache, utilizzando tali record per rispondere a query future. I record presenti nella cache vengono cancellati allo scadere di un TTL (Time-to-live) o a seguito di un comando manuale. Solitamente, all’interno della cache dei server DNS locali sono presenti i server TLD più comuni, implicando che il root server venga interrogato raramente. Tuttavia, è necessario notare che i record nella cache potrebbero non essere aggiornati: se viene cambiato l’indirizzo IP associato ad un nome presente nella cache, esso potrebbe non essere noto all’interno di Internet fino alla scadenza di tutti i TTL di tutti i server, poiché quest’ultimi risolverebbero la richiesta restituendo l’indirizzo IP precedente.

La risoluzione dei nomi, ossia la restituzione dell’indirizzo IP ad esso legato, può avvenire in due modalità: 
- Risoluzione a query iterativa, dove il server contattato dal client risponde con il nome del prossimo server da contattare, il quale (probabilmente) sarà in grado di risolvere il nome 
  ![[Risoluzione_query_iterativa.PNG|600]]
- Risoluzione a query ricorsiva, dove l’onere della risoluzione del nome viene affidato al server contattato, ricorsivamente (succede molto raramente nella realta)
  ![[Risoluzione_query_ricordsiva.PNG|600]]

Ogni mappatura nome-indirizzo viene inserita all’interno di un resource record (RR), il quale assume la struttura (name, value, type, ttl), dove a seconda del valore del campo type si ha che:  ^5f0907
- **type = A**: indica che il campo name contiene il nome di un host interno ad un dominio (hostname) e il campo value contiene l’indirizzo IP di tale host.
  (es: name = relay1.bar.foo.com, value = 45.37.93.126)
- **type = NS**: indica che il campo name contiene il nome di un dominio e il campo value contiene l’hostname del server autoritativo associato a tale dominio.
  (es: name = foo.com, value = dns.foo.com)
- **type = CNAME**: indica che il campo name contiene un alias del nome canonico e il campo value contiene il nome canonico stesso.
  (es: name = www.ibm.com, value = servereast.backup2.ibm.com) 
- **type = MX**: indica che il campo name contiene il nome di un mail server interno ad un dominio e il campo value contiene l’hostname di tale mail server 

> [!example] Esempio
> 1. Un server autoritativo per un hostname contiene un record di tipo A per l’hostname stesso, ad esempio (corsi.di.uniroma1.it, 131.111.45.68, A) 
> 2. Un server non autoritativo per un dato hostname contiene un record di tipo NS per il dominio che include l’hostname e un record di tipo A che fornisce l’indirizzo IP del server DNS nel campo value del record NS. 
>    
>    Ad esempio, un server TLD .it che non è autoritativo per l’host corsi.di.uniroma1.it, contiene i due record (uniroma1.it, dns.uniroma1.it, NS) (dns.uniroma1.it, 128.119.40.111, A)
---
date:
  - 2025-04-06
tags:
  - Richiesta
  - Risposta
  - DNS
  - Protocollo
  - Livello_applicazione
---
## Definizione

> [!info] Definizione: Protocollo DNS
> Il protocollo DNS è un protocollo a livello di applicazione utilizzato per la risoluzione di hostname e nomi di dominio. La sua porta di riferimento comune all’interno dei socket è la **porta 53**. 
> Al fine di rendere il trasferimento il più rapido possibile, il protocollo DNS utilizza il protocollo UDP, richiedendo l’invio di un singolo messaggio, evitando necessità della creazione del collegamento. Se un messaggio non giunge a destinazione dopo un determinato timeout, esso viene semplicemente rinviato. 
> Inoltre, il protocollo DNS è un protocollo stateless, (semplicemente poiché non è necessario salvare alcuno stato)

([[Messaggi di richiesta e risposta]])
La richieste e le risposte DNS assumono la stessa struttura: 
1. **Un header** lungo 32 bit, composto da due campi da 16 bit: 
	1. **Identification**: contenente informazioni del richiedente 
	2. **Flags**: contenente flag di stato indicanti se il messaggio sia di richiesta o risposta, se la risoluzione ricorsiva sia preferita o disponibile e se la risposta sia di un server autoritativo
2. Un campo **questions** di dimensione variabile contenente le informazioni per una richiesta
3. Un campo **answers** di dimensione variabile contenente i [[Gerarchia server DNS#^5f0907|Resource Record (RR)]] da inviare come risposta 
4. Un campo **authority** di dimensione variabile contenente i RR autoritativi da inviare come risposta 
5. Un campo per le **informazioni aggiuntive**

![[Req_res_dns.PNG|400]]


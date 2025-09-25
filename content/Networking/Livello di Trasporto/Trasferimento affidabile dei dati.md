---
date:
  - 2025-04-06
tags:
  - Affidabilita
  - Trasferimento_file
  - RDT
  - Livello_Trasporto
---
Per realizzare un trasferimento affidabile dei dati, è necessario implementare un canale sicuro al cui interno non vengano perse o corrotte informazioni.

![[Reliable_chanel.PNG|500]]

Tuttavia, un canale fisico che possa svolgere tale funzione risulta essere irrealizzabile. Per tale motivo, la complessità del protocollo di trasferimento dati affidabile (RDT - Reliable Data Transfer) dipende fortemente dalle caratteristiche del canale inaffidabile utilizzato.

![[Unreliable_chanel.PNG|500]]

Inoltre, è necessario puntualizzare che il mittente e il destinatario non conoscono lo stato l’uno dell’altro (es: se la ricezione sia andata a buon fine), a meno che non gli venga comunicato tramite un ulteriore messaggio. 

Il protocollo RDT presenta delle **interfacce** per il suo utilizzo: 
- **rdt_sent(data)**: il quale viene chiamato dal livello di applicazione e il cui argomento corrisponde ai dati da inoltrare al destinatario 
- **udt_send()**: dove UDT è acronimo di Unreliable Data Transfer, il quale viene chiamato dal protocollo RDT sul mittente per trasferire il pacchetto sul canale inaffidabile 
- **rdt_rcv()**: il quale viene chiamato alla ricezione del pacchetto dal destinatario 
- **deliver_data()**: il quale viene chiamato dal protocollo RDT sul destinatario per inoltrare al livello di applicazione i dati ricevuti

![[unr_chanel_rdt.PNG|500]]

Poiché i dispositivi comunicanti non sono a conoscenza dello stato altrui, il protocollo RDT si basa su un trasferimento dei dati unidirezionale, dunque come se uno solo dei due sia il mittente ed uno solo sia il destinatario (sebbene in realtà sia bidirezionale). Per rappresentare le operazioni e le decisioni effettuate dal protocollo, utilizzeremo le macchine a stati finiti (FSM - Finite State Machine) e in particolare la seguente notazione:

![[Nom_FSM.PNG|500]]

## Protocollo RDT 1.0 e 2.0

![[Protocollo RDT 1.0 e 2.0]]

![[Protocollo RDT 2.1 e 2.2]]

![[Protocollo RDT 3.0]]
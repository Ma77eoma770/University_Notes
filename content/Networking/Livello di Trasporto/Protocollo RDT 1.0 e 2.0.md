---
date:
  - 2025-04-06
tags:
  - RDT
  - Protocollo
  - Livello_Trasporto
---
All’interno del protocollo **RDT 1.0**, viene assunto che il canale sottostante utilizzato per il trasferimento sia perfettamente affidabile, implicando che il mittente invii i dati nel canale e il ricevitore li legga direttamente, senza alcuna operazione aggiuntiva

![[RDT1.0.PNG]]

Nel protocollo RDT 2.0, invece, viene assunto che il canale sottostante possa **invertire alcuni bit nel pacchetto inviato**. Analogamente al protocollo UDP, viene utilizzato un **checksum** per rilevare la presenza di errori. Nel caso in cui venga rilevato uno di quest’ultimi, il destinatario comunicherà al mittente l’esito dell’operazione: 
- Acknowledgements (**ACK**), dove il destinatario dice esplicitamente al mittente che il pacchetto è stato ricevuto senza problemi .
- Negative acknowledgements (**NAK**), dove il destinatario dice esplicitamente al mittente che il pacchetto ricevuto presenta degli errori.
Successivamente all’invio di un pacchetto, il mittente rimane in attesa della risposta del destinatario (meccanismo **stop and wait**)

![[RDT.2.0_ACK_NAK.PNG]]

Se la risposta ricevuta è un ACK, il mittente torna il stato di attesa del prossimo pacchetto da parte del livello applicativo.

![[RDT2.0ACK.PNG]]

Se invece la risposta è un NAK, il mittente rinvia il pacchetto generante l’errore e rimane in attesa della risposta del destinatario, ripetendo nuovamente tale processo nel caso in cui si riceva nuovamente un NAK.

![[RDT2.0NACK.PNG]]

Tuttavia, la versione 2.0 del protocollo RDT presenta un **difetto fatale**: se la risposta ACK/NAK è corrotta, il mittente non è più a conoscenza di cosa sia accaduto al destinatario. Inoltre, non è sufficiente ritrasmettere il pacchetto per risolvere tale difetto, poiché il destinatario potrebbe ricevere due pacchetti duplicati ed inoltrarli al livello di applicazione. ^07ef55
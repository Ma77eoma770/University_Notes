---
date:
  - 2025-04-13
tags:
  - RDT
  - Livello_Trasporto
  - Protocollo
---

## Protocollo RDT 2.1

Per risolvere il difetto [[Protocollo RDT 1.0 e 2.0#^07ef55|fatale della versione 2.0]], il protocollo RDT 2.1:
- Viene controllato se la risposta ACK/NAK sia corrotta. Nel caso in cui lo sia, il pacchetto viene rinviato.
- Il destinatario non è a conoscenza della possibile corruzione del pacchetto ACK/-NAK
- Viene aggiunto un numero di sequenza al pacchetto inviato. In particolare, sono necessari i numeri di sequenza 0 ed 1 affinché il protocollo stop and wait possa funzionare correttamente:
	- Assieme alla risposta di ACK, il destinatario invia un numero di riscontro, il quale, per convenzione, indica sempre il numero di sequenza del prossimo pacchetto atteso dal destinatario.
	- Se il destinatario ha ricevuto correttamente il pacchetto 0, invia un riscontro con valore 1 (dunque il prossimo pacchetto atteso è il pacchetto 1)
	- Analogamente, se il destinatario ha ricevuto correttamente il pacchetto 1, invia un riscontro con valore 0 (dunque il prossimo pacchetto atteso è il pacchetto 0)
- Se il pacchetto ricevuto dal destinatario è un duplicato, esso viene automaticamente scartato senza essere inviato al livello di applicazione

Mittente:
![[Protocollo_RDT_2.1_2.2.png]]

Destinatario:

![[RDT_2.1_FSM_SEND.png]]

## Protocollo RDT 2.2

In aggiunta alle modifiche della versione 2.1, il protocollo RDT 2.2 elimina la necessità di una risposta NAK: il ricevitore invia come numero di riscontro il numero di sequenza dell’ultimo pacchetto correttamente. In tal modo, un ACK duplicato al mittente comporta la stessa azione di un NAK, ossia la ritrasmissione del pacchetto corrente.

![[RDT_2.2_FSM.png]]
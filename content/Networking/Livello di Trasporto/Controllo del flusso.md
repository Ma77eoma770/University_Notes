---
date:
  - 2025-04-21
tags:
  - TCP
  - Livello_Trasporto
  - FlowControll
---
Per poter funzionare correttamente, il protocollo TCP necessita di un **controllo del flusso**. Ad esempio, se la velocità con cui il livello di rete del destinatario fornisce i dati è maggiore rispetto a quella con cui il suo livello di applicazione rimuove i dati dal buffer del socket, il buffer andrà in **overflow**, implicando che i dati in eccesso vengano necessariamente 
**scartati**, risultando tuttavia come ricevuti correttamente dal destinatario.

![[Controllo_flusso_TCP.png]]

Di conseguenza, è necessario che il destinatario controlli il mittente, impedendo che quest’ultimo possa riempire il buffer del destinatario trasmettendo troppi dati velocemente. Per gestire il flusso, quindi, viene utilizzata un campo rwnd (receive window) all’interno del segmento TCP::
1. Il destinatario inserisce in rwnd il numero di byte che è disposto ad accettare (dunque lo spazio rimanente nel buffer del socket).
2. La dimensione del RcvBuffer impostata tramite le opzioni socket (predefinito a 4096 byte) o gestita automaticamente dal sistema operativo.
3. Il mittente limita la quantità di dati inviati senza ACK al valore di rwnd, garantendo che il buffer di ricezione non vada in overflow

![[Buffer_controllo_flusso_TCP.png]]
---
date:
  - 2025-04-13
tags:
  - Protocollo
  - GoBackN
  - SelectiveRepeat
  - Livello_Trasporto
---
## Protocollo Go-back-N

Per migliorare le prestazioni del protocollo RDT 3.0, viene utilizzato il pipelining, dove il mittente consente la presenza di molteplici trasferiti senza aver ricevuto un ACK precedente.
Per realizzare il pipelining, l’intervallo di numeri di sequenza deve essere aumentato,
poiché è necessario tener traccia di più pacchetti simultaneamente, richiedendo inoltra la
presenza di un buffer interno al mittente e al destinatario.
Poiche i pacchetti successivi al primo vengono inviati durante contemporaneamente al
RTT del primo pacchetto, è sufficiente considerare un solo RTT, incrementando notevol-
mente la percentuale di utilizzo del mittente:

> [!example] Esempio
> - Riprendendo i dati dell’esempio [[Protocollo RDT 3.0#^ac5bbc|precedente]], effettuando il pipelining con 3 pacchetti si ha che:
>   $U_{mit} = \frac{3 \cdot D_t}{RTT + D_t} = 3 \cdot \frac{8 \mu s}{30 ms + 8 \mu s} = 81 \cdot 10^{-5} = 0.081 \%$
>
>   ![[Esempio_pipelining_RDT.png]]

Una delle metodologie con cui viene implementato il pipelining è il Go-back-N:
- Il mittente ha una "finestra" di N pacchetti consecutivi trasmessi senza ACK (ACK cumulativo). La ricezione del pacchetto ACK(n) viene interpretato dal mittente come un ACK per ognuno dei singoli N pacchetti, implicando che alla sua ricezione la finestra venga spostata in avanti in modo che essa abbia il pacchetto N + 1 come primo pacchetto
  ![[Window_goback.png]]
  
- Viene mantenuto attivo un **timer** per il pacchetto della finestra inviato e senza ACK più vecchio. Una volta scaduto tale timeout, viene ritrasmesso il pacchetto e tutti i pacchetti con numero di sequenza maggiore presenti all’interno della finestra
- Il destinatario invia sempre **l’ACK con numero di sequenza maggiore** (in ordine) per i pacchetti attualmente ricevuti **correttamente**, implicando che non vi siano pacchetti con numero di sequenza minore mancanti.
  Tale procedura potrebbe generare ACK duplicati e richiede di ricordare solamente un **valore rcv_base** (a differenza della finestra del mittente), corrispondente al numero di sequenza del pacchetto di cui si è in attesa
- Se il destinatario riceve un pacchetto fuori ordine, può, a seconda dell’implementazione, scartare tale pacchetto (**politica don’t buffer**) o conservarlo (**politica buffer**), inviando in entrambi i casi un ACK con il più alto numero di sequenza che si trovi nell’ordine corretto, richiedendo quindi la trasmissione di tutti i pacchetti con numero di sequenza maggiore.
  
  ![[rcv_base_goback.png]]
  
  
> [!example] Esempio:
> - Consideriamo la seguente finestra di 7 pacchetti:
>   ![[Example_1_goback.png]]
> - Una volta ricevuto ACK(5)i pacchetti 4 e 5 vengono considerati come arrivati a destinazione, scorrendo la finestra in avanti
>   ![[Example_2_goback.png]]
>   ![[Example_3_goback.png]]
>   
>   Esempio (protocollo go-back-N con politica dont't buffer)
>   
>   ![[Example_4_goback.png]]

Poiché nel caso in cui un singolo pacchetto venga perso o corrotto è necessario rinviare tutti
i pacchetti successivi già inviati nella pipeline, il protocollo Go-back-N può **peggiorare la congestione della rete**.

## Protocollo Selective Repeat

Contrariamente, il **protocollo Selective Repeat** è in grado di gestire tale problematica:
- Oltre al mittente, anche il destinatario è **dotato di una finestra di N pacchetti**
- Il destinatario conferma **individualmente** tutti i pacchetti ricevuti correttamente, anche nel caso in cui essi siano fuori sequenza, **bufferizzandoli** per l’eventuale consegna in ordine al livello superiore
- Il mittente mantiene un **timer per ogni pacchetto** inviato senza ACK, rinviando ogni pacchetto individualmente alla scadenza del suo timeout
- La finestra del mittente scorre a partire dal **pacchetto più alto confermato in ordine** (senza pacchetti non confermati prima di esso). Alla ricezione dell’ACK di un pacchetto, dunque, se tale pacchetto era il più piccolo pacchetto non ancora confermato, la finestra avanza fino al prossimo pacchetto non confermato
  
  ![[Example_1_selectiverepeat.png]]

Esempio:

![[Example_2_selectiverepeat.png]]

Tuttavia, anche il protocollo **Selective Repeat** non è privo di problematiche. In particolare, se la dimensione della finestra è troppo piccola, si può andare in contro a casi sfavorevoli (**dilemma della finestra**). Ad esempio, con un range di numeri di sequenza pari a 0, 1, 2, 3 e una finestra di dimensione 3, si ha che:

![[Dilemma_della_finestra.png]]

> [!example] Esempio:
> - In una rete con un valore fisso m > 1 (numero di bit della sequenza), è possibile utilizzare entrambi i meccanismi Go-Back-N e Selective Repeat, si indichino i vantaggi e gli svantaggi dell’impiego di ciascuno di essi. Quali altre considerazioni si devono fare per decidere quale meccanismo utilizzare?
>1. **Go-back-N**
> - Ritrasmette tutti i frame inviati dopo il frame che si sospetta essere danneggiato o perso
> - Se il tasso di errore è alto, spreca molta larghezza di banda
> - Meno complicato
> - Window size $N - 1 = 2^m - 1$
> - Riordinamento non è richiesto né lato mittente né lato destinatario
> - Il destinatario non memorizza i frame ricevuti dopo il frame corrotto finché esso non viene ritrasmesso (dipende dall’implementazione)
> - Non è richiesta alcuna ricerca di frame né lato mittente né destinatario
>2. Selective Repeat
> - Ritrasmette solo i frame sospettati di essere persi o danneggiati
> - Comparativamente meno larghezza di banda viene sprecata nella ritrasmissione
> - Più complesso in quanto richiede l’applicazione di logica aggiuntiva, ordinamento e archiviazione, lato mittente e destinatario
> - Window size $\frac{N + 1}{2} = 2^{m - 1}$
> - Il destinatario deve essere in grado di ordinare in quanto deve mantenere la sequenza dei frame
> - Il destinatario memorizza i frame ricevuti dopo il frame danneggiato nel buffer finché il frame danneggiato non viene sostituito
> - Il mittente deve essere in grado di cercare e selezionare il frame richiesto
> 
> Un’idea aggiuntiva implementata nei trasferimenti **bidirezionali** (dunque dove entrambi i dispositivi sono sia mittente sia destinatario, coincidenti con la vita reale) è il **piggybacking**, dove nel momento in cui un pacchetto stia trasportando dati dal dispositivo A al dispositivo B, vengono trasportati anche i riscontri ricevuti da A inerenti ai pacchetti ricevuti da B, in modo che entrambi i dispositivi ne siano a conoscenza, gestendo efficientemente il rinvio dei pacchetti.
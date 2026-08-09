---
date: 2026-05-11
tags:
  - Sicurezza
  - Networking
---
# Wifi Vulnerabilities

## CIA

- **Confidenzialità**
- **Integrità**
	- L'attaccante non può ricevere qualcosa da un client modificarlo e reinviarlo prima che il segnale originale giunga al server.
	- Bensì un attacante può usare un forte segnale wifi (i ricevitori preferiscono segnali più forti).
- **Disponibilità**
	- Failure HW o SW con malware nell'infrastruttura.
	- Servizi lenti causati da eventi fisici (loss of packet with delay of retransmission) o direttamente un overload.
	- Rogue Network Connection (public hot spots): Nonostante il servizio sia disponibile ma potrebbe avere delle vulnerabilità/falle

## Unauthorized Wifi Access

Step per il protocollo wifi per l'accesso:
1. L'Access Point (AC) manda in broadcast una specie di invito (beacon).
2. Un NIC di un dispositivo manda la richiesta per autenticarsi che l'AC accetterà.
3. Il NIC richiede l'associazione che l'AC negozia e accetta.

> [!bug] Sono tutti e 3 vulnerabili

1. Tutti possono prendere e replicare ad un beacon in broadcast.
2. L'autenticazione non è rigorso, in una configurazione basica potrebbe accettare qualsiasi dispositivo senza autenticazione.
3. L'AC può accettare un'associazione con qualsiasi dispositivo.

Ci sono due modalità con cui il protocollo wifi opera:
1. Open Mode: Il client sceglie l'SSID (client non sempre visibile solo quando risponde).
2. Closed Mode: Il client diventa il beacon mandando continue serie di messaggi, in questo caso il client può emulare un vero AP con intento malevolo.

> [!bug] Anche qui entrambe vulnerabili

Un approccio migliore considera l'associazione del device all'AC tramite uno shared data balue che è legato ad un'unica associazione/sessione.

## WEP (Wired Equivalent Privacy)

Un metodo per avere privacy equivalente a quella delle reti wired nelle reti wireless. Un esempio di una contromisura (fallimentare) per dare agli user immunità dall'eavesdropping e impersonation attacks. WEP usa una chiave criptata. Per autenticare un client, l'AC:
1. Manda un numero casuale al client.
2. Il client cifra il RN con la shared key e la rimanda all'AC.
3. Da questo momento sono associati e possono dialogare usando la shared encryption key

Debolezze:
1. Chiave di crittografia a 64 o 128 bit, ma ogni chiave inizia con un'inizializzazione a 24 bit vettore (IV) - lunghezza effettiva della chiave a 40 o 104 bit 
	1. RIsulta in una lunga passphrase di 16 o 32 simboli.
	2. Vengono utilizzate passphrase indovinabili.
	3. La passphrase non cambia spesso.
	4. Un aggressore dedicato in grado di monitorare una grande quantità di traffico di rete wireless raccoglierà molti punti dati da cui dedurre una chiave.
2. Per il RN usa RC4 che genera una lunga sequenza di numero casuali chiamati key sequence derivati da un vettore di inizializzazione (IV) da 24 bit e una chiave da 40 bit
3. WEP combina la sequenza della chiave usando un exclusive-OR function con dei dati: se l'attaccante puo indovinare il valore decrittografato di un singolo frame crittografato, dandolo in pasto alla funzione exclusive-OR può rivelare quel segmento della sequenza chiave.
4. La stessa sequenza di key viene riutilizzata per tutti i messaggi, quindi il segmento si ripeterà nello stesso punto.
5. L'IV viene comunicato come testo in chiaro: un aggressore possa intercettarlo per un attacco di ricerca esaustiva delle chiavi.
6. Initialization Vector Collisions: Un ciclo a 24 bit in uno schema prevedibile finché non sono stati utilizzati tutti gli schemi a 24 bit – 16 milioni di iterazioni (testabili in pochi minuti). In pratica, alcuni valori del vettore di inizializzazione rimangono intrappolati nel ciclo e non cambiano mai, mentre altri non attraversano tutti i 16 milioni di pattern a 24 bit.
7. Faulty Integrity Check: Controllo di integrità debole utilizzato per dimostrare se un frame è arrivato intatti o meno, se i bit sono stati persi o modificati accidentalmente durante la trasmissione.
8.  No Authentication: Qualsiasi dispositivo in grado di nominare l'SSID corretto e presentare l'indirizzo MAC corretto è si presume sia legittimo.

## WPA (Wifi Protected Access)

- Non-Static Encryption Key: Utilizza un approccio di modifica delle chiavi, denominato Temporal Key Integrity Program (TKIP); la chiave di crittografia viene modificata automaticamente su ogni pacchetto. Stabilisce una nuova chiave per ogni sessione. Il punto di accesso (the authenticator) e il dispositivo di connessione (il supplicant) creare e scambiare chiavi per riservatezza e integrità che sono uniche per la sessione di associazione.
- Authentication: Utilizza l'Extensible Authentication Protocol (EAP) tramite il quale è possibile l'autenticazione fatto tramite password, token, certificato, ecc... 
- Strong Encryption: Aggiunge AES, sebbene anche RC4 sia ancora supportato per mantenere la compatibilità .
- Integrity Protection: Utilizza un controllo di integrità a 64 bit crittografato.

L'inizializzazione:
1. Un autenticazione attraverso  EAP.
2. Una 4-way handshake, per assicurarsi che il client sia in grado di generare chiavi crittografiche e per generare/installare chiavi per encryptuon e integrity su tutti e due gli estremi (client e server).
3. Una group key handshake per comunicazione multicast.

### Possible Attacks

1. **Man-in-the-Middle**: Superata nel WPA2. L'attacco funziona attraverso lo spoofing del MAC address dell'AC. Durante la sequenza di associazione tra i device e l'AC, il device presenta le sue credenziali per autenticarsi e l'AC manda un messaggio che gli conferma l'autenticazione. A quel punto il man in the middle cambia MAC address con quello dell'AC e manda una richiesta di disaccoppiamento al device (solitamente termina la comunicazione per entrambi per svariati motivi come completamento delle operazioni, overloading, etc.). Il dispositivo  cessa l'associazione e ricomincia il processo di associazione; nel frattempo, l'outsider malintenzionato ha cambiato l'indirizzo MAC con quello del  dispositivo dissociato e continua l'associazione con l'AC come se fosse l'utente originale.
2. **Incomplete Authentication**: Ad un certo punto il client (supplicant) è tenuto ad autenticarsi presso il punto di accesso, ma il client non ha alcuna base per garantire che l'AC è legittimo, cioè che una parte malintenzionata non sta inviando segnali che fingono di essere un AC. Pertanto, il supplicante può essere costretto a rivelare i dati di autenticazione a un terza parte non autorizzata.
3. **Exhaustive Key Search**: WPA usa una chiave a 256 bit che sembra abbastanza lunga per essere sicura. Questo però comporta che l'utente scelga chiavi abbastanza guessable, l'unico pro è che effettivamente essendo lento come algoritmo l'attaccante non ha troppi tentativi consecutivi per guessare le password. Un altro attacco sempre di questo tipo è "chopchop" ovvero si attacca all'integrità per scoprire la chiave; si taglia e sostituisce un byte di un blocco e vedere la modifica nel blocco integrità. Si ripete questo processo consentendo di dedurre la chiave di integrità. Patchata in WPA2.

> [!hint] WPA3
>  Implementa la stessa struttura, preclude i dictionary attacks chiavi migliori algoritmi migliori, concede di usare altri sistemi di autenticazione rispetto alle password.

# Denial of Service (DoS)

E' un tentativo di distruggere la disponibilità. Non è solo bianco o nero ma bensì spazia tra completa distruzione della disponibilità a ritardi importanti a piccole inconvenienze.

Cause:
1. **Volumetric**: Si crea un volume superiore alla capacità del sistema.
2. **Application**: Un applicazione che consuma tutto il sistema.
3. **Tagliare**: La comunicazione tra due punti del servizio.
4. **HW o SW failure**.
## Flooding (insufficient capacity) 

Il bersaglio di un attacco di inondazione può essere:
- Un'applicazione, come un sistema di gestione di database; 
- Un sistema operativo o uno dei suoi componenti, ad esempio un server di file o di stampa; 
- Un dispositivo di rete come un router;
- Una risorsa, come una tabella di allocazione della memoria o una pagina web.

I canali per flodding sono TCP, UDP or ICMP:
- **Ping of death**: L'attaccante non invia un singolo pacchetto gigante (perché verrebbe bloccato subito), ma invia una serie di frammenti malevoli: l'attaccante manipola gli offset (gli indirizzi di posizione) dei frammenti. L'ultimo frammento della serie dichiara di contenere dati che portano il totale del pacchetto oltre il limite massimo consentito. Quando il sistema operativo della vittima tenta di riassemblare questi pezzi in un unico "buffer" di memoria, si verifica un buffer overflow Poiché il sistema non sa gestire un pacchetto più grande di quanto previsto dal protocollo, va in errore. Questo causava solitamente il "Blue Screen of Death" (BSOD) su Windows o il kernel panic su sistemi Unix, bloccando o riavviando il computer.
- **Smurf**: 

## Blocked Access

Impedisce il funzionamento di un servizio:
- Sfrutta una vulnerabilità software in un'applicazione e causare il crash dell'applicazione
- Interferisce con i meccanismi di routing della rete, impedendo alle richieste di accesso di arrivare al server 
- Manipola i dati di controllo degli accessi, eliminare le autorizzazioni di accesso per la risorsa o disabilitare il meccanismo di controllo degli accessi in modo che nessuno possa essere approvato per l'accesso 
- Crittografia dei dati!

## Access Failures

Componenti non reattivi a causa di un guasto HW/SW
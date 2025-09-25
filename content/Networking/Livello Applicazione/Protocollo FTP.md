---
date:
  - 2025-04-06
tags:
  - FTP
  - Trasferimento_file
  - Protocollo
  - Livello_applicazione
---
## Definizioni


> [!info] Definizione: Protocollo FTP
> **Il protocollo FTP (File Transfer Protocol)** è un protocollo a livello di applicazione utilizzato per il trasferimento di file basato sul paradigma client-server. 
> Per gestire il trasferimento dei file, il protocollo FTP utilizza due connessioni TCP: 
> 1. Una connessione di controllo (porta 21), utilizzata per trasferire le informazioni per il controllo del trasferimento (es: nome utente, password, comandi per cambiare directory e per il trasferimento) .
> 2. Una connessione dati (porta 20), la quale viene aperta ogni qualvolta sia necessario trasferire un file, per poi chiuderla successivamente .
> 
> Inoltre, il protocollo FTP è un protocollo stateful, conservando la directory corrente e l’autenticazione della sessione precedente

Nel protocollo FTP, il client corrisponde al dispositivo avviante il trasferimento verso un dispositivo remoto, mentre il server corrisponde al dispositivo remoto stesso. 

Quando l’utente fornisce al proprio client il nome del server a cui connettersi tramite il comando **ftp «nome host»**, il processo client FTP stabilisce la connessione di controllo sulla porta 21. 

Successivamente, il client trasferisce nome utente e password sulla porta 21, autenticandosi. Una volta ottenuta l’autorizzazione dal server, il client può trasferire uno o più file memorizzati nel file system locale verso quello remoto (o viceversa), aprendo e chiudendo la connessione dati sulla porta 20 ad ogni trasferimento.

![[Schema_FTP.PNG|700]]

I principali comandi del protocollo FTP sono:

| Comando e Argomenti              | Descrizione                                     |
| -------------------------------- | ----------------------------------------------- |
| ABOR                             | Interrompe il comando precedente                |
| CDUP                             | Torna alla directory del livello precedente     |
| CWD <nome_directory>             | Cambia directory crrente                        |
| DELE <nome_file>                 | Elimina il file                                 |
| LIST <nome_directory>            | Elenca i file nella directory                   |
| MKD <nome_directory>             | Crea una directory                              |
| PASS <pass_word>                 | Invia la passord dell'utente                    |
| PASV                             | Il server scehie la porta della connessione     |
| PORT <port_a>                    | Il client sceglie la porta della connessione    |
| PWD                              | mostra nome directory corrente                  |
| QUIT                             | Termina la comunicazione                        |
| RETR <nomi_dei_file>             | Trasferisce uno o piu file dal server al client |
| RMD <nome_directory>             | Elimina la directory                            |
| RNTO <vecchio_nome> <nuovo_nome> | Rinomina il file specificato dal vecchio nome   |
| STOR <nomi_dei_file>             | Trasferisce uno o piu file dal client al server |
| USER <nome_utente>               | Invia il nome dell'utente                       |

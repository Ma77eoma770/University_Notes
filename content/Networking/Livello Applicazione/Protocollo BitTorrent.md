---
date:
  - 2025-04-06
tags:
  - BitTorrent
  - Protocollo
  - Trasferimento_file
  - Livello_applicazione
---
## Definizione

> [!info] Definizione: Protocollo BitTorrent
> Il **protocollo BitTorrent** è un protocollo a livello di applicazione utilizzato per il trasferimento di file basato sul paradigma [[Principi delle applicazioni di rete#^c13de3|peer-to-peer (P2P)]]. Nonostante non abbia una porta standard, solitamente vengono utilizzate le porte nel range 6881- 6889 assieme al protocollo TCP.
> 
> Ogni peer entra a far parte di un **torrent**, ossia un gruppo di peer scambianti frammenti di file tra loro, registrandosi su un **tracker**, ossia un dispositivo che tiene traccia dei peer partecipanti al torrent, per poi connettersi ad un sottoinsieme di peer "vicini". 
> 
> Durante il download di file, il peer svolge anche la funzione di uploader (**seeder**) di blocchi verso altri peer. Una volta ricevuto il file, il peer può scegliere se uscire dal torrent o rimanerne all’interno, continuando a svolgere la funzione di seeder. 

In un dato momento, peer diversi possiedono diversi sottoinsiemi di blocchi componenti un file. Per richiedere tali blocchi, un peer chiede periodicamente agli altri **l’elenco dei blocchi attualmente posseduti**. Successivamente, il peer richiede i blocchi mancanti, dando precedenza ai più rari.

Per favorire l’altruismo tra i peer e sfavorire la presenza di **leecher**, ossia dispositivi che egoisticamente escono dal torrent una volta scaricato un file, il protocollo BitTorrent usa un approccio tit-for-tat (traduzione più vicina: do ut des, "io ti do e tu mi dai" ): 
- Ogni peer seeder invia blocchi agli ulteriori quattro peer seeder che attualmente stanno uploadando i blocchi richiesti alla velocità maggiore
- Gli altri peer non appartenenti alla top 4 vengono "strozzati" (chocked) dal peer seeder, bloccando l’invio dei blocchi ad essi. 
- Ogni 10 secondi, tale top 4 viene rivalutata. Inoltre, ogni 30 secondi viene sbloccato casualmente un peer strozzato (optimistic un-chocking), il quale può entrare o meno a far parte della top 4.

Per via di tale approccio, il trasferimento di un file ad N dispositivi risulta più ottimale nel caso dell’applicazione del paradigma P2P.

![[P2P vs. Client.PNG|600]]
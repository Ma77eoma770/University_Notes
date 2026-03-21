---
date:
  - 2026-03-19
tags:
  - Tirocinio
  - Sicurezza
---

> [!question] Perchè Argon2Id?
> Per capirlo dobbiamo andae ad analizzare i suoi predecessori

Algoritmi come SHA-256 sono progettati per essere **veloci ed efficienti**. Richiedono solo potenza di calcolo (ALU - Arithmetic Logic Unit) e pochissima memoria.

> [!success] Quindi...
> Se un hacker ruba il database con gli hash delle password e usa una fattoria di schede video (GPU) o circuiti specializzati (ASIC), può provare miliardi di combinazioni al secondo.

Argon2 (vincitore della Password Hashing Competition nel 2015) cambia le regole del gioco. Non si basa solo sul tempo di calcolo (_time-hard_), ma esige un pedaggio in memoria RAM (_memory-hard_).

### Funzionamento

Ecco come funziona passo dopo passo, usando i parametri di un sistema CCV3 (64 MB di RAM, 2 iterazioni, 4 lane):

1. **La Scacchiera (Allocazione):** Appena inserisci la password, Argon2id alloca immediatamente una "scacchiera" vuota in memoria grande esattamente 64 MB, divisa in piccoli blocchi.
2. **Il Riempimento (Passaggio 1):** L'algoritmo prende la tua password, il "salt" (dati casuali) e inizia a riempire il primo blocco. Poi, per riempire il blocco successivo, usa un mix crittografico dei dati contenuti nel blocco precedente.
3. **Il Labirinto (Accesso Pseudo-Casuale):** Qui arriva la magia. Man mano che riempie la memoria, per calcolare il blocco $X$, l'algoritmo va a ripescare i dati dal blocco $Y$ e dal blocco $Z$, scelti in modo matematico ma "saltando" da una parte all'altra dei 64 MB.
4. **Le Iterazioni (Passaggio 2):** Con `iterations=2`. Questo significa che, una volta riempiti tutti i 64 MB, l'algoritmo ricomincia da capo, mescolando nuovamente l'intera scacchiera passandoci sopra per una seconda volta.


> [!hint] Infine...
> Alla fine di questo heavy-computing, l'algoritmo "spreme" l'intera matrice da 64 MB in una singola, purissima stringa da 32 byte.

### Il Collo di Bottiglia

Se un attaccante usa una GPU moderna per craccare la tua password, i suoi migliaia di core si scontreranno con un limite fisico insormontabile: il **bus della memoria**.

Le GPU non hanno 64 MB di memoria _veloce dedicata per ogni singolo core_. I core dovrebbero condividere l'accesso alla memoria centrale della scheda video, creando un ingorgo colossale. Costruire un hardware personalizzato (ASIC) che abbia migliaia di core, _ognuno_ accoppiato a 64 MB di memoria super-veloce indipendente, costerebbe milioni di dollari e fisicamente richiederebbe chip enormi. Argon2 trasforma la potenza di calcolo in uno svantaggio.

### Versioni

Argon2 ha tre varianti:

- **Argon2d (Data-dependent):** I "salti" nella memoria dipendono dai dati della password stessa. È il più resistente contro il brute-force delle GPU, ma è vulnerabile agli attacchi "side-channel" (un hacker sulla stessa macchina potrebbe dedurre la password misurando quanto tempo impiega il processore a pescare in memoria).

- **Argon2i (Data-independent):** I salti in memoria sono predeterminati. Sicurissimo contro i side-channel, ma leggermente meno forte contro le GPU.

- **Argon2id (Hybrid):** L'ibrido perfetto. Si comporta come "2i" nella prima metà del primo passaggio (quando il rischio di svelare la password tramite side-channel è più alto), e poi passa a "2d" per il resto del processo, garantendo la massima resistenza contro le GPU e gli ASIC.


> [!summary] Riassunto
> Da una password (anche facile da ricordare ma che da sola non servirebbe a cifrare) + salt si ottengono 32 byte da usare come password (o master key crittografica) sicurissima molto difficile da sorpassare.

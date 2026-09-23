---
date: 2026-04-15
tags:
  - Sicurezza
  - Internet
---
## Browser Attack

Compromettendo il browser compremettiamo grand parte di informazioni importanti che l'user maneggia (molto importante e centrale).

> [!warning] Perchè attaccare il browser? 
> Corrompere dati installare malware e vedere inormazioni sensibili (password, utenti, cookies di sessione etc etc)

> [!bug] Vari vettori:
> 1. Passare direttamente dall'OS per impedire controlli di sicurezza
> 2. Intercettare o modificare la comunicazione dal browser e per il browser (non solo quando i pacchetti escono ma in vari punti della comunicazione)
> 3. Modificare add-on plugin, alterare codice per renderlo malevolo
> 4. Modificare il codice di un sito web

Vari tipi di attacchi identificati:

### Man-in-the-browser: 

Codice malevolo inserito nel browser che può andare ad acquisire/modificare/alterare informazioni gestite dall'utente (credenziali, ...)

> [!example]- Silent Banker
> Un trojan add-on. Intercettava tutte le call che il broser fa (keystrokes, inviare dati, generare o importare chiavi crittografiche, collegare e scaricare dati dall'esterno).
> Non face nulla finchè non si accedeva al link delle banche (hardcoded 400 URL di banche), da cui quindi si attivava e trasmetteva al computer attaccante. Non altera le azioni dell'utente ("silent"). Poicè lavora nella fase di pre-encryption, se l'utente va a specificare in input un account su cui movimentare account esso veniva cambiato per trasferire denaro all'attaccante malevolo. Modificava anche le informazioni che arrivavano (ad esempio confermare che il denaro fosse arrivato al conto corretto e non a quello malevolo).

### Keystroke logger

Cattura i "battiti" sulla tastiera. Molto semplici ma comunque efficaci

### Page-in-the-Middle

Mentre l'user sta navigando il sito normale, l'attacco si concentra a reindirizzare l'utente in una pagina fittizia, creata appositamente e più vicina possibile all'originale (ad esempio un login). Non richiede di modificare il browser

### Program Download Substitution

L'attaccante mostra all'utente una apgina con un programma apparentemente innoquo, che si vorrebbe scaricare. Molto spesso si danno pieni poteri a questi programmi perchè installati e eseguiti da un utente con (abbastanza) privilegi. Ovviamente il programma è un trojan o malware, in ogni caso malevolo.
### User-in-the-middle

Mette un umano in mezzo a due processi automatici in modo da fare registrare migliaia di email account ???????
Per combatterlo vengono messi in atto
- CAPTCHAs / reCAPTCHA(v1): vecchio e debole facilmente bypassato da image recognition o agenti NLP.
- reCAPTCHA(v2): Aggiunge user behaviour pattern recognition per rilevare bot.
- Private Access Token (PAT) dovrebbero sostituire i CAPTCHA sui device Apple.


> [!warning] Perchè questi attacchi funzionano?

> [!bug] Da qualche parte l'autenticazione o l'dentificazione falliscono
### Punti vulnerabili nell'autenticazione

1. **L'usabilità** e **accuretazza** possono rendere il riconoscimento meno efficace e comunque non abbastanza stringenti.
2. **L'interazione computer-computer** in particolare per l'autenticazione tra computer ci si basa su quello che il computer "conosce" ovvero che è salvato da qualche parte, ma se è salvato può essere alterato.
3. **Codice malevolo** ben piazzato che ruba (eavesdropping) credenziali e poi le riusa per attaccare.
### Metodi sicuri per autenticazione e identificazione

1. **Shared Secret**: qualcosa che solo due entità che si vogliono autenticare sanno. (Diffie-Helman, signature)
2. **One Time Password (OTP)**
3. **Out-of-Band Communication**: Autenticazione a due fattori.
4. **Continuous Authentication**: La crittografia permette un'autenticazione continua ma può essere rotta (bruteforce) o nel momento in cui si scambiando le chiavi corrotta (session hijack), o anche pre o post encryption.
## Attacchi contro gli user

### Content misleading

-  Website defacement
- Fake Website
- Fake Code
- Malicious Web Content
- Web Bug
- Clickjacking
- Drive-by-download

> [!warning] Tutte richiedono un qualche tipo di accesso (parziale o totale) al sito e un modo in cui convincere lo user a cliccare.

> [!success] Soluzioni:
> **Integrity Checksum**: checksum di tutti i file e si fa un controllo periodico per vedere se qualcosa è stato alterato (TripWire)
> **Signed Code o Data**: assicura l'integrità server-side non user-side. Un certificato di terze parti o una firma digitale su codice/dati. Anche una company malevola può creare i propri certificati.

### Web bug

E' una immagine piccola 1x1 pixel che viene processata come tante immagini, parte del processamento serve per notificare il proprietario di questo bug che sta essendo rappresentato sullo schermo dello user. Riescono a tracciare il crowling dell'utente all'interno dei vari siti che visita (anche usati nelle mail).

### Clickjacking

Su una finestra con vari click (fittizzi) dietro (fisicamente usando una foto trasparente) si compiono azioni volontarie da parte dell'utente ma indotte in modo malevolo dall'attaccante.

### Drive-by Download

Oltre a quello che si scarica si scarica anche codice malevolo. Molto spesso l'utente non ha acconsentito nè sa che sta scaricando ciò che non vuole.

### Proteggersi da queste webpage

Ricade totalmente server-side:
1. Diversi livelli di autorizzazioni.
2. Assicurarsi in maniera continuativa che il codice sia pulito leggibile corretto e non alterato.

## Ottenere user o website data (scripting&injection)

### Cross-Site Scripting (XSS)

Si forza il server a eseguire (o meglio interpretare degli script malevoli). Essi vengono forniti attraverso richieste HTTP. Questi script girano interamente client-side (eseguire un'animazione). Di conseguenza si possono far eseguire script malevoli che hanno un contesto relativo al browser (tutto ciò che lui sa lo sa anche lo script).

Ancora più pericoloso è quando si riesce a caricare lo script sul server, ogni qual volta un client si connette a quella pagina su cui è caricato lo script, esso partira sul server.

### SQL injection

Sfruttano le vulnerabilità sia dell'applicazione web sia di SQL. L'obiettivo è scaricare in massa il db per possibili dati sensibili. Si possono anche però modificare dati. Essenzialmente tutto ciò che si può fare con una query sul db.
1. Si trova un sito vulnerabile (che non controlla, sanifica, gli input).
2. Il comando passa dalla web application al web server che lo rimanda al db server.
3. Il server ritornerà a catena una pagina con caricati i dati richiesti.

Tecniche:
- Terminare un campo con `--` il quale indica la terminazione della query.
- User input: in cui viene fatta una query ad ok per avere delle informazioni dal db
- Server variables: Mettere dati nell'header HTTP
- Second-Order Injection: Si possono sfruttare dati già nel sistema in modo tale che se modificati creano un attacco dall'interno del server.
- Cookies: Alterare i cookie per farsi rispondere dal server (che li sfrutta) in modo diverso.
- Physical user input: Applicazione dell'input dell'utente che costruisce un attacco al di fuori dell'ambito delle richieste web.

Categorie di attacchi:
- Inband
- Inferential
- Outband
### Inband

Usa lo stesso canale di comunicazione per scambiare dati e sql injection query e i dati sono rappresetnati direttamente sulla pagina web. Di questa categoria fanno parte:
1. **Tautology**: Injection di dati booleani (1=1) per rendere delle condizioni sempre vere.
2. **End-of-line Comment**: Strategia con `--` per ignorare il resto della query
3. **Piggy-backed queries**: Aggiunge alla query normale altre query non richieste e malevole protette dall'unica query leggittimata.

### Inferential

Non c'è effettivamente un trasferimento di dati ma l'attaccante è in grado di ricostruire informazioni mandando specifiche request e osservanto il comportamento del web/db server.
Includono:
- Illegal/illogical queries: Molto spesso in caso di configurazione non corretta si riescono a scoprire cose in più: versioni delle applicazioni (viene usato ancora in fasi preliminari).
- Blind SQL injection: Time based attack sostanzialmente in cui si osserva il server come e quando risponde.

### Out-of-band

I dati vengono recuperati utilizzando un canale diverso. Questo può essere utilizzato quando ci sono limitazioni sulle informazioni recupero, ma connettività in uscita dal database il server è rilassato.

### Dot-Dot-Slash

Se non configurato bene il server può dare accesso a chiunque ad alcune directory che dovrebbero essere riservate facendo escaping "../"

### Server-Side Include

Delle pagine possono invocare particolari funzioni automaticamente, ad esempio alcune pagine usano dei comandi per mandare email.  I comandi vengono inseriti in un campo che viene interpretato. Uno dei comandi di inclusione lato server è exec, per eseguire un file arbitrario sul server. Ad esempio, il lato server include comando consente di aprire una sessione telnet con il server.

```
<!--#exec cmd="/usr/bin/telnet &"-->
```

### Difesa da attacchi scripting/injection

1. Input sanitization (in tuti i possibili modi di codifica).
2. Access Control da parte del backend che può ricevere e eseguire questi script (o query) malevoli. Limitare anche l'output di questi ultimi (sia in caso di successo) che in caso di fallimento.

## App vulnerabilities

### App vs. website

App:
1. Sta sul dispositivo
2. Interagisce con tutte le feature hw e sw per un determinato dispositivo (android,osx)
3. GIra in background e si stoppa solo quando si spegne il telefono
4. Colleziona memorizza e trasmette dati che potresti non poter monitorare
5. Update solo quando è connesso

Website:
1. L'accesso richiede connettività
2. Gira su qualsiasi browser
3. Si ferma quando ti disconnetti dal sito e puoi eliminare la cronologia
4. Puoi eliminare "crumbs" che vengono lasciate dietro (cookies)
5. Possono aggiornare il proprio codice immediatamente e si diffonde a tutti gli utenti.

I pericoli riguardo al mobile troviamo che gli aspetti di confidenzialità (confidentiality) sono i predominanti in quanto si da tanta responsabilità e dati a questi dispositivi. Successivamente abbiamo la disponibilità (availability): i backup non sono di default, rompere il dispositivo è uno scenario molto concreto, perderlo ancora di più. Per quanto riguarda l'integrità (integrity) anche questa non è di default e ha bisogno di attenzione.

> [!question]- Perchè attacchi mobile?
> ![[Pasted image 20260420142812.png]]
> Molti dispositivi, molti utenti, molte app = tanta scelta

Molte considerazioni sul software/browser già fatte si applicano lo stesso. Inoltre rientano anche le Open Web App Security Project (OWASP):
1. Injection
2. Cross-Site Scripting
3. Faulty Authentication
4. Access Control Failures
5. Insecure Direct Object References
6. Security Logging and Monitoring
7. Cryptographic Failings
8. Software and Data Integrity Issues
9. Vulnerable or Outdated Components
10. Security Misconfiguration

### Injection

L'input-checking ricade sul programmatore dell'app e non c'è modo di sapere se è stato fatto correttamente.

### XSS

Si intende quando l'attacco attraversa dal sito fino al dispositivo. Molte app fanno girare script automatici a fin di bene ma non tutti sono buoni e queti ultimi non sono facilmente identificabili.
Questi script potrebbero accedere tutto quello che l'app può accedere con quei permessi.

### Faulty Authentication

Ogni app controlla l'accesso alle proprie risorse (codice, dati , websites, ...).
I dispositivi mobili inraprendono l'autenticazione solo quando il dispositivo è acceso e sbloccato. Le app devono configurarsi con altri computer per user e login in generale (possibile [[The internet (User Side)#User-in-the-middle]]).

I rischi/fallimenti per l'autenticazione sono: 
1. Bruteforcing
2. Shipping dell'app con autenticazione di default debole (admin admin)
3. Conservare le password in chiaro o criptati debolmente
4. Riuso degli identificatori di sessione o uso di identificatori facili da indovinare
5. Passaggio di un identificatore di sessione o altro autenticatore in chiaro in una stringa URL
6. Non usare il "least privilege" o una politica troppo permissiva (o il contrario)
7. Permettere all'utente di evadere l'AC (ad esempio modificando l'URL)
8. Permettere la privilege escalation
9. Consentire l'accesso diretto a un'applicazione nascosta tramite un'interfaccia di programmazione (API) o dati
10. Non revocare l'accesso al termine di una sessione o di un'altra interazione
11. L'app dovrebbe proteggere i dati dall'utente
12. Logging non gestito correttamente (molto spesso non si creano proprio dei log e quindi è difficile andare ad analizzare comportamenti anomali o intere vulnerabilità, nè generare allarmi in tempo e quindi avvertire di possibili falle)

### Cryptographic Failings

Anche se gli sviluppatori delle app non sviluppano effettivamente questa parte devono essera  consocenza dei rischi e comnque devono essere consapevoli di quello che fanno. Gli sviluppatori devono:
- Programmare le app in modo da generare, archiviare e utilizzare le chiavi crittografiche in modo sicuro.
- Impiegare funzioni hash crittografiche per rendere evidenti eventuali violazioni dell'integrità.

> [!bug] E' importante verificare che un' app non sia compromessa e/o modificata (codice malevolo)

Le applicazioni si basano su plug-in, librerie o moduli provenienti da altre fonti.
Una vulnerabilità in uno qualsiasi di questi elementi comporta una vulnerabilità nell'app. Quando un'app è composta da codice proveniente da diverse fonti, lo sviluppatore deve tenerle tutte aggiornate e modificate. Si considerino i tempi di attesa tra la scoperta di una falla in una libreria e il momento in cui una libreria aggiornata viene testata e integrata in un'app che l'utente installa. Fino a quando non viene installata una nuova versione, la vecchia app rimane vulnerabile. Un problema ancora più grave si verifica se un'app dipende da un software non più supportato.

### Security Misconfiguration

Alcuni problemi di configurazione a cui prestare attenzione:
- Mancata applicazione degli aggiornamenti correnti o utilizzo di versioni obsolete
- Mancata modifica dei valori predefiniti (soprattutto delle password) e mancata modifica regolare delle password critiche
- Esecuzione di servizi non necessari sul dispositivo

Le app moderne sono diventate strumenti **opachi e pericolosi**: richiedono permessi illimitati e spesso agiscono come **spyware**, raccogliendo dati inutili per favorire i fornitori anziché gli utenti.
Gli user preferiscono la rapidità e la facilità piuttosto che la sicurezza dell'app.

### Sviluppare app sicure

Bisogna seguire, come in tutti gli sviluppi software, la procedura corretta per lo sviluppo sciro, quindi:
1. **Sviluppatori competenti**: non c'è nessuna qualifica "buon sviluppatore sicuro", ci sono alcune certificazioni ma non sono comparabili. L'unico modo è sapere come è stata sviluppata un'app (workflow, testing, ...).
2. **Automated Development Controls**: Molte tecniche (Agile) si pongono di aumentare la velocità di sviluppo a discapito di documentazione managment e dettagli di design. Per produrre il software bisogna fare a prescindere delle considerazioni sulla sicurezza tenendo sempre a mente "cosa si potrebbe fare per aggirare l'applicazione e comprometterla". Il NIST (SP 800-53) stabilisce che la sicurezza non deve essere un'aggiunta finale, ma una componente integrata: i programmatori sono tenuti a inserire **"sensori" e "ganci" direttamente nel codice**. Questi strumenti permettono a test indipendenti e tecnologie **RASP** (Run-time Application Self-Protection) di monitorare l'app sia durante lo sviluppo che durante l'esecuzione, identificando falle e vulnerabilità in tempo reale per bloccare eventuali attacchi dall'interno.
3. **Code Analyzer Tools**: 
	1. **Static application security testing tools (SAST)**: Ispezione dentro al codice compilato (white box). Individua errori come: errori numerici, mancata convalida dell'input, condizioni di gara, attraversamenti di percorso, puntatori e riferimenti errati.
	2. **Dynamic application security testing tools (DAST)**: Ispezione a runtime (blackbox). Evidenzia i problemi relativi a interfacce, richieste e risposte di dati, scripting (ad esempio, JavaScript), iniezione di dati, sessioni, autenticazione e altro ancora.
	3. **Interactive Application Security Testing (IAST)**: Un ibrido tra i due sopra. Sono pensati per essere utilizzati in un ambiente di test di sviluppo, in modo da poter analizzare il codice durante la sua scrittura e poi durante la compilazione e l'esecuzione. L'analisi dell'app è facilitata da hook inseriti nell'app durante il suo sviluppo.
4. **Run-time Application Self-Protection (RASP)**: RASP monitora continuamente gli input (e il comportamento – nelle versioni più avanzate di RASP) delle applicazioni per rilevare e prevenire minacce note e sconosciute (zero-day) nel punto in cui si manifesta la vulnerabilità. Quando viene rilevata una minaccia, RASP ne impedisce lo sfruttamento e intraprende le azioni appropriate, come avvisare l'utente, terminare la sessione, chiudere l'applicazione o allertare un responsabile della sicurezza.

## Email and Message Attacks

Il pro principale è che si possono mandare tantissime mail e anche se il success rate è bassissimo il risultato è comunque rilevante. Ci sono varie tecniche.

### Pump & Dump

Tramite campagne di spam, si invitano gli utenti a comprare delle penny stock, ma l'unico che ci guadagna è il possessore dello stock.

### Spam

Le contromisure per lo spam sono di 3 tipi:
1. Filtro sul mail server
2. Azioni legali
3. Shutdown tecnico

Contromisure inutili:
1. Azioni legali sull'azione intrinseca di "spam", non crea troppi problemi e non danneggiano attivamente qualcuno o qualcosa.
2. Le email di provenienza: non sono effettivamente ben rintracciabili e facilmente falsificate o create ad ok.

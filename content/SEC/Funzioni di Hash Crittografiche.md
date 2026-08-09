---
date:
  - 2026-03-02
tags:
  - Crypto
  - Sicurezza
---
# Funzioni di Hash Crittografiche

> [!summary] Che cos'è una funzione Hash?
> È un'alternativa al MAC che **non richiede una chiave in input**. Accetta un messaggio $M$ di dimensione variabile e produce in output un *message digest* $H(M)$ di dimensione fissa. 

## Il processo di Padding e la Sicurezza

Per essere processato, il messaggio viene "riempito" (*padded*) affinché la sua lunghezza diventi un multiplo intero di una lunghezza fissa stabilita dall'algoritmo (ad esempio, blocchi da 1024 bit). 
**Misura di sicurezza cruciale:** Il campo di padding include sempre il valore della lunghezza del messaggio originale in bit. Questo aumenta drasticamente la difficoltà per un attaccante di produrre un messaggio alternativo malevolo che generi lo stesso identico valore di hash.

L'autenticazione tramite Hash può essere implementata in diversi modi:
* **Hash + Crittografia Simmetrica:** Il message digest viene cifrato usando una chiave segreta condivisa $E_K(H(M))$.
* **Hash + Crittografia a Chiave Pubblica:** Il digest viene cifrato con la chiave privata del mittente $E_{PR}(H(M))$. Questo fornisce una *firma digitale* oltre all'autenticazione, senza richiedere distribuzione di chiavi segrete.
* **Hash con Valore Segreto (Senza Cifratura):** Si calcola $MD_M = H(K || M || K)$, dove $K$ è un segreto condiviso. Il mittente invia il messaggio in chiaro concatenato a questo hash. È un metodo estremamente rapido perché elimina totalmente i costi hardware e software legati agli algoritmi di cifratura.

## Le 6 Proprietà di una funzione Hash Sicura
1. Può essere applicata a un blocco di dati di **qualsiasi dimensione**.
2. Produce un output a **lunghezza fissa**.
3. Il calcolo di $H(x)$ è **relativamente facile** per qualsiasi $x$, rendendo pratiche le implementazioni hardware e software.
4. **Resistenza alla preimmagine (One-way):** Dato un hash $h$, è computazionalmente infattibile risalire all'input $x$ tale che $H(x)=h$.
5. **Resistenza debole alle collisioni (Seconda preimmagine):** Dato un blocco $x$, è computazionalmente infattibile trovare un messaggio diverso $y \neq x$ che produca lo stesso hash ($H(y)=H(x)$).
6. **Resistenza forte alle collisioni:** È computazionalmente infattibile trovare una *qualsiasi* coppia di messaggi $(x, y)$ tale che $H(x)=H(y)$. 

## Attacchi basati sulle debolezze delle proprietà Hash
Se una funzione di hash non rispetta le proprietà sopraelencate, espone il sistema a vulnerabilità catastrofiche:
* **Attacco alla Preimmagine:** Se è facile invertire la funzione per trovare $x = H^{-1}(h)$, un attaccante che intercetta un messaggio $M$ e il suo hash autenticato $MD_M = H(K || M || K)$ può invertire l'hash per ottenere $K || M || K$. Conoscendo $M$, è banale estrarre la chiave segreta $K$.
* **Attacco alla Collisione Debole:** Permette la falsificazione di un hash cifrato. L'attaccante intercetta un messaggio con il suo hash cifrato, genera il corrispondente hash non cifrato, e crea un messaggio alternativo malevolo con lo stesso hash, sostituendolo senza invalidare la firma.
* **Attacco alla Collisione Forte:** Usato per truffe mirate. Esempio: Bob prepara due documenti con lo stesso hash (uno dove Alice gli paga una piccola cifra, uno dove paga una cifra enorme). Fa firmare ad Alice il primo. Poiché il secondo documento ha lo stesso hash, Bob applica la firma di Alice al secondo documento e dichiara che è autentico.

> [!note] Metodi di attacco e Altre applicazioni
> Come per la crittografia, gli hash si attaccano per **crittoanalisi** (sfruttando debolezze logiche dell'algoritmo) o **brute-force** (la cui difficoltà dipende *esclusivamente* dalla lunghezza dell'hash prodotto). Lo standard più usato è lo SHA.
> Oltre all'autenticazione, l'hash si usa per proteggere le **password** (il S.O. memorizza solo l'hash) e per l'**Intrusion detection** (si salva l'hash di ciascun file di sistema e lo si verifica regolarmente per rilevare alterazioni).
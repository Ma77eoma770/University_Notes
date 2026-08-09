---
date:
  - 2026-03-22
tags:
  - Tirocinio
  - Sicurezza
---
# Obiettivi

1. Rifinire primitive crittografiche [:LiBookmarkCheck:]
2. Risolvere il problema dei gruppi in O(1) invece di O(n)
3. Implementare un double ratchet (semplificato)
4. Implementare un principio di obfuscation con padding sui pacchetti con test sulla tracciabilità e il deep packet inspection
# Link Utili

1. [[XSalsa20-Poly1305]]
2. [[Fernet]]
3. [[Fernet vs. XSalsa20-Poly1305]]
4. [[HKDF-SHA256]]
5. [[Argon2Id]]
6. [[Curve25519]]
# Task 1

### Refactoring della Crittografia del Vault: 

Da [[Fernet]] (AES-CBC) a PyNaCl ([[XSalsa20-Poly1305]])

**Com'era**: Fernet come wrapper standardizzato per AES-128 in modalità CBC, con padding e autenticazione tramite HMAC-SHA256. L'implementazione dipendeva strettamente da chiavi a 32 byte codificate in Base64 URL-safe, internamente divise per cifratura e firma.

```python
# crypto_service_old.py
def cifra_vault(dizionario, master_key):
    json_data = json.dumps(dinizionario)
    f = Fernet(master_key)
    blob_cifrato = f.encrypt(json_data.encode())
    return blob_cifrato
```

**Com'è adesso (New File)**: Adozione del binding PyNaCl per l'utilizzo diretto di SecretBox, implementato tramite il cifrario di flusso [[XSalsa20-Poly1305#Salsa20|XSalsa20]] combinato con il MAC [[XSalsa20-Poly1305#Poly1305|Poly1305]].

```python
# crypto_service.py
def cifra_vault(dizionario: dict, master_key) -> str:
    json_data = json.dumps(dizionario).encode('utf-8')
    raw_key = base64.urlsafe_b64decode(master_key)
    box = nacl.secret.SecretBox(raw_key)
    nonce = nacl.utils.random(nacl.secret.SecretBox.NONCE_SIZE)
    encrypted = box.encrypt(json_data, nonce)
    return base64.b64encode(nonce + encrypted.ciphertext).decode('utf-8')
```

**Cos'è cambiato**: L'architettura abbandona un cifrario a blocchi obsoleto e dipendente da imbottitura (padding) in favore di un cifrario di flusso moderno. Invece di delegare interamente la generazione della "busta" formattata a Fernet, la nuova implementazione gestisce esplicitamente il nonce (un numero monouso di 24 byte) concatenandolo al testo cifrato generato da SecretBox.

**Risultato Ottenuto**: Un incremento prestazionale del ~40% nelle operazioni di I/O del database crittografato, essenziale in un contesto dove il vault viene continuamente decifrato, mutato (es. aggiunta di nuove chiavi) e ricifrato. A differenza di AES, che richiede il supporto hardware specifico (come le istruzioni AES-NI) per essere eseguito in constant-time ed evitare attacchi side-channel (cache-timing), XSalsa20 è stato progettato esplicitamente per le architetture software. Garantisce nativamente l'immunità agli attacchi timing anche su hardware modesto (come vecchi smartphone o sistemi embedded), senza dipendere dai set di istruzioni del processore.

**Fondamento Teorico**: AES è un cifrario a blocchi da 16 byte. Qualsiasi payload non divisibile per 16 richiede l'aggiunta di padding, generando un overhead operativo. XSalsa20 opera manipolando bit per bit, allineando l'output esattamente alla lunghezza dell'input. L'utilizzo di un nonce esteso a 192 bit (24 byte) abbassa le probabilità di collisione a soglie trascurabili, rendendo sicura la generazione tramite `os.urandom`.

### Sostituzione di Eseguibili CLI Esterni 

Da eseguibili CLI Esterni (Age) a Crittografia Busta Digitale ([[Busta Digitale (Digital Envelope)|Envelope]]) Nativa

**Com'era**: Invocazione via subprocess del tool a riga di comando age. I dati e le chiavi pubbliche venivano passati tramite I/O di standard input/output.

```python
# crypto_service_old.py
def cifra_con_age(plaintext: str | bytes, public_keys: list):
    args = ['age']
    for key in public_keys:
        args.extend(['-r', key])
    
    input_data = plaintext if isinstance(plaintext, bytes) else                                   plaintext.encode()
    result = subprocess.run(args, input=input_data, capture_output=True,                      check=True)
    return base64.b64encode(result.stdout).decode()
```

**Com'è adesso**: Implementazione avanzata di una "Digital Envelope" ibrida (asimmetrica/simmetrica) che garantisce la _Sender Forward Secrecy_ a livello di singolo messaggio. L'architettura si basa su una combinazione tra chiavi d'identità a lungo termine (conservate nel Vault) e chiavi effimere generate dinamicamente ad ogni invio.

Nello specifico, il flusso di cifratura (Envelope v3) avviene in queste fasi per ogni singolo messaggio:

1. **Generazione Effimera e MMK**: Il client mittente genera "al volo" una nuova coppia di chiavi X25519 "usa-e-getta" e crea una _Master Message Key_ (MMK) crittograficamente forte.
2. **Key Agreement ([[Curve25519|ECDH]])**: Sfruttando le proprietà di omomorfismo della Curva Ellittica Diffie-Hellman, il mittente calcola uno _Shared Secret_ moltiplicando la propria _Chiave Privata Effimera_ neo-generata con la _Chiave Pubblica a lungo termine_ del destinatario. L'assunto matematico di base di ECDH garantisce che il destinatario potrà ricavare lo stesso identico segreto invertendo i fattori (moltiplicando la sua Privata a lungo termine per l'Effimera appena ricevuta), il tutto senza che alcuna chiave privata transiti mai in rete.
3. **Derivazione Chiave (KDF) e Wrapping**: Il Segreto Condiviso appena calcolato viene passato a una funzione [[HKDF-SHA256|HKDF]] per derivare una chiave di cifratura robusta (DEK - _Data Encryption Key_). Questa DEK viene quindi utilizzata per "chiudere il lucchetto" attorno alla Master Message Key (MMK), cifrandola in modo sicuro tramite `NaCl SecretBox`.
4. **Cifratura del Payload e Terminazione**: La MMK in chiaro cifra il contenuto reale del messaggio ([[XSalsa20-Poly1305]]). Il payload cifrato, la MMK cifrata e la Chiave Pubblica Effimera vengono impacchettati nell'Envelope JSON (v3) e inviati. Immediatamente dopo, la Chiave Privata Effimera del mittente viene distrutta in modo permanente dalla memoria.

```python
# crypto_service.py
def _encrypt_mmk_for_recipients(mmk: bytes, ephemeral_priv: X25519PrivateKey, public_keys: list) -> list[str]:
    encrypted_deks = []
    
    for pk_b64 in public_keys:
        # Recupera la chiave pubblica statica (a lungo termine) del destinatario
        pub_key = X25519PublicKey.from_public_bytes(base64.b64decode(pk_b64))
        
        # KEY AGREEMENT: Privata Effimera * Pubblica Statica = Shared Secret
        shared_key = ephemeral_priv.exchange(pub_key)
        
        # HKDF: Deriva la Data Encryption Key (DEK) dal segreto condiviso
        derived_dek = HKDF(
            algorithm=hashes.SHA256(), 
            length=32, 
            salt=None,                            
            info=b"ChatControl Message DEK HKDF"
        ).derive(shared_key)
        
        # WRAPPING: Usa la DEK per cifrare la Master Message Key (MMK)
        dek_box = nacl.secret.SecretBox(derived_dek)
        nonce_dek = nacl.utils.random(nacl.secret.SecretBox.NONCE_SIZE)
        enc_mmk = dek_box.encrypt(mmk, nonce_dek)
        
        # Salva la MMK crittografata (lucchetto chiuso)
        encrypted_deks.append(base64.b64encode(nonce_dek +                                enc_mmk.ciphertext).decode('utf-8'))
        
    return encrypted_deks

```

**Cos'è cambiato**: Completa rimozione delle chiamate a processi di sistema in favore della libreria crittografica cryptography. Il sistema genera una singola Master Message Key (MMK) per il messaggio, per poi distribuire questa MMK in copie distinte, ognuna cifrata crittograficamente in modo univoco per la chiave pubblica di ogni specifico destinatario.

**Evidenze dai Benchmark**: Anche se isolato solo nelle macro-categorie, il pattern Memory a 1MB impiega 15.36ms per cifrare rispetto a soluzioni delegate che subiscono penalty di I/O legati ai processi del SO. L'eliminazione dei fork del sistema operativo rende le tempistiche puramente dipendenti dalla CPU in user-space.

**Risultato Ottenuto**: Completa agnosticità rispetto all'infrastruttura sottostante (nessuna necessità di avere binari installati tramite apt come age o age-keygen). Azzeramento dei point of failure legati alla creazione di shell temporanee e file system. La base di codice aumenta di complessità. Lo scambio di chiavi asimmetrico (ECDH) non tollera errori di codifica.

**Fondamento Teorico**: La crittografia asimmetrica è intrinsecamente lenta e inadatta a cifrare grandi moli di dati. Il pattern della Digital Envelope risolve il problema usando ECDH su Curve25519 per creare uno "Shared Secret" inaccessibile a terzi. Poiché lo Shared Secret matematicamente generato da una curva ellittica mantiene una struttura algebrica prevedibile, si applica l'HKDF-SHA256 (Hash-based Key Derivation Function) per "distruggere" tale struttura, "strizzando" il segreto e condensandone l'entropia per ottenere rumore bianco perfetto da passare al cifrario simmetrico.

### Introduzione di Crittografia Asincrona in Streaming (Chunking)

Da Cifratura a Blocchi a Cifratura in Streaming (forzata per Poly che altrimenti saturerebbe la ram)

**Com'era**: Lettura bloccante in memoria. Per tentare la decifratura, il wrapper leggeva iterativamente su un file di testo temporaneo, caricando l'intero input binario su una stringa.

```python
# crypto_service_old.py
def decifra_file_con_age(ciphertext, candidate_privates):
    raw_bytes = ciphertext.encode() if isinstance(ciphertext, str) else                          ciphertext
    # ... decodifica
    result = subprocess.run(['age', '-d', '-i', keyfile_path],                                input=input_bytes, capture_output=True, check=True)
    return result.stdout
```

**Com'è adesso**: Inserimento dei generatori (yield in Python) e delle funzioni asincrone per elaborare flussi di dati in streaming. Il payload viene partizionato e l'invio interviene per blocchi logici accompagnati dalla loro precisa lunghezza nell'intestazione binaria.

```python
# crypto_service.py
async def decifra_payload_stream(async_iterator, candidate_privates: list):
    # ... validazione header
    payload_box = nacl.secret.SecretBox(mmk)
    while True:
        chunk_len_bytes = await _read_exact(async_iterator, 4, buffer)
        if not chunk_len_bytes: break
        chunk_len = int.from_bytes(chunk_len_bytes, byteorder='big')
        encrypted_chunk = await _read_exact(async_iterator, chunk_len, buffer)
        yield payload_box.decrypt(encrypted_chunk)
```

**Cos'è cambiato**: Passaggio architetturale da un approccio in-memory monolitico (O(N) spaziale) a un approccio chunked (O(1) spaziale). Si sfrutta lo stream cipher XSalsa20 per autenticare e decrittare il flusso in tempo reale attraverso finestre di byte scorrevoli.

**Evidenze dai Benchmark**: Sui file di grandi dimensioni (25MB), l'approccio vecchio caricava interamente il buffer, portando la RAM a picchi di 230.56 MB. Con il nuovo codice, il picco di memoria RAM è appiattito a un consumo costante di 7.26 MB (sia in decrittazione che cifratura). I tempi di esecuzione per la crittografia crollano da 314.73 ms a soli 46.74 ms. 

> [!bug] n.b.
> Con un test più spartano abbiamo constatato come un file da circa 10 mb ci impieghi qui 10 secondi (limitato da telethon mb-per-chanel) mentre nel vecchio codice 1 minuto (limitato dalle primitive lente e specialmente da age)

**Risultato Ottenuto**: La piattaforma è ora virtualmente illimitata nella grandezza dei payload gestibili, proteggendo il backend da out-of-memory (OOM) o saturazione della RAM anche elaborando gigabyte di media in contemporanea. Alleggerimento della ram e in generale del sistema (specialmente per low-memory o embedded systems). Difficoltà di tracciamento o re-try. In un approccio streaming, se il trasferimento asincrono fallisce a metà (TCP Socket rotto), il file parzialmente scaricato non sarà integro, e si perde il lusso di poter validare crittograficamente un pacchetto per intero prima di passarlo al layer applicativo.

## Tests

--- TEST FERNET (AES-CBC) ---
Cifratura   : 4.36 ms | Picco RAM: 773.27 KB | Size Output: 118.16 KB
Decifratura : 1.72 ms | Picco RAM: 443.72 KB

--- TEST PYNACL (SecretBox XSalsa20) ---
Cifratura   : 3.26 ms | Picco RAM: 534.01 KB | Size Output: 118.13 KB
Decifratura : 1.53 ms | Picco RAM: 622.11 KB

---

--- [Test File from 1 MB] ---
[1MB] Testing Legacy V3 Memory Approach...
 -> V3 Encrypt:   7.18 ms | RAM Peak:   8.34 MB
 -> V3 Decrypt:   4.86 ms | RAM Peak:   9.23 MB
[1MB] Testing New V1 Stream Approach...
 -> V1 Encrypt:   1.74 ms | RAM Peak:   5.25 MB
 -> V1 Decrypt:   1.82 ms | RAM Peak:   3.38 MB

--- [Test File from 5 MB] ---
[5MB] Testing Legacy V3 Memory Approach...
 -> V3 Encrypt:  32.78 ms | RAM Peak:  41.67 MB
 -> V3 Decrypt:  26.03 ms | RAM Peak:  46.11 MB
[5MB] Testing New V1 Stream Approach...
 -> V1 Encrypt:   6.21 ms | RAM Peak:   7.25 MB
 -> V1 Decrypt:   7.07 ms | RAM Peak:   7.26 MB

--- [Test File from 25 MB] ---
[25MB] Testing Legacy V3 Memory Approach...
 -> V3 Encrypt: 180.21 ms | RAM Peak: 208.34 MB
 -> V3 Decrypt: 135.16 ms | RAM Peak: 230.56 MB
[25MB] Testing New V1 Stream Approach...
 -> V1 Encrypt:  31.07 ms | RAM Peak:   7.25 MB
 -> V1 Decrypt:  30.99 ms | RAM Peak:   7.26 MB
 
 ---

## Sviluppi Futuri e Roadmap Architetturale

### L'Architettura di Transizione Attuale

L'attuale implementazione della _Envelope Encryption_ asimmetrica-simmetrica (con _Sender Forward Secrecy_) è una soluzione architetturale non convenzionale. Questa "atipicità" è voluta: rappresenta la fase di transizione (il ponte logico) per traghettare la piattaforma dalle limitazioni legacy introdotte dal binario `age`, verso un sistema più complesso. Abbiamo solidificato le primitive crittografiche (Curve25519, HKDF, XSalsa20), svincolandole dai binari di sistema e preparandole a inserirsi nel prossimo step del Double Ratchet.

### Implementazione del Double Ratchet (1-a-1)

L'obiettivo primario del prossimo ciclo di sviluppo è colmare il divario architetturale dal modello attuale verso un vero e proprio **Double Ratchet**, sul modello del Signal Protocol. Per procedere l'infrastruttura dovrà:

- Integrare un protocollo di iniziazione **X3DH** (Extended Triple Diffie-Hellman), permettendo ai client di scambiarsi _PreKeys_ in modalità asincrona tramite il backend, senza la necessità che entrambi gli utenti siano online.
- Sostituire l'uso di chiavi effimere isolate per-messaggio con vere e proprie **Chain Keys** derivate continuamente (KDF Ratchet).
- In un vero e proprio Double Ratchet la difficoltà maggiore rimane quella di gestire out-of-order packet o più generalmente per il problema delle *skipped keys*. Abbiamo deciso che per ora non vogliamo affrontare questa parte per favorire, invece, altri rami di sviluppo.

### Il Paradigma dei Gruppi (Da O(N) a O(1))

Il protocollo Double Ratchet nativo è intrinsecamente progettato per comunicazioni _P2P (1-to-1)_. Attualmente, l'invio nei gruppi in ChatControl scala linearmente in **O(N)** (dove N è il numero dei partecipanti al gruppo per cui dobbiamo calcolare separatamente l'ECDH per cifrare la singola MMK). Estendere il Double Ratchet puro nei gruppi risulterebbe in un overhead quadratico ingestibile e tempi di esecuzione inaccettabili. Lo sviluppo futuro prevede di risolvere questo collo di bottiglia introducendo un approccio **Sender Keys**. Questo permetterà di crittare il payload per il gruppo in tempo costante **O(1)**, negoziando la chiave di root solo all'entrata o all'uscita di un componente, mantenendo al contempo le proprietà di _Post-Compromise Security_.

### 4. Traffic Obfuscation, Padding e Deep Packet Inspection (DPI)

L'ultimo obiettivo in roadmap esplora la protezione dal censimento del traffico di rete (Traffic Analysis) tramite algoritmi di _Padding_ sui pacchetti di rete. Sebbene i contenuti siano matematicamente inaccessibili, la dimensione e il timing dei pacchetti possono rivelare l'impronta di un'applicazione a Firewall governativi e sistemi di **Deep Packet Inspection (DPI)**. Nello scenario di ChatControl esistono due facce della medaglia riguardo questa funzionalità (dipendenti esclusivamente dal threat model che vogliamo considerare):

- **Scenario inutile:** L'applicazione attualmente viaggia trasportata dalle di primitive di rete di Telegram (utilizzando l'infrastruttura MTProto sottostante). Pertanto, un ISP che attua ispezione DPI vedrà solamente "Normale traffico Telegram". Aggiungere un livello di padding al nostro payload cifrato non muterebbe l'apparenza del pacchetto rispetto ai firewall ISP esterni, rappresentando un costo computazionale inutile.
- **Scenario possibile (un po' paranoico):** Il Padding diventa al contrario utile se il nostro threat-model considera Telegram stesso (e i server ospitanti) come l'avversario che esegue analisi DPI. Offuscare le dimensioni del payload previene attacchi di inferenza lato-server, dove Telegram — pur non potendo leggere i messaggi — potrebbe dedurre comportamenti, flussi media o l'identità dell'interlocutore (Fingerprinting) basandosi esclusivamente sullo studio statistico delle dimensioni dei byte (le lunghezze dei ciphertext di XSalsa20 sono identiche a quelle del plaintext introdotto).
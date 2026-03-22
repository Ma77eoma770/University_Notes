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
def cifra_vault(dinizionario, master_key):
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

**Evidenze dai Benchmark**: I dati confermano una netta superiorità della nuova libreria. La cifratura del Vault scende da 15.88 ms a 9.21 ms, mentre la decifratura si riduce quasi della metà (da 6.18 ms a 3.54 ms). Le dimensioni in output rimangono equivalenti (118.16 KB vs 118.13 KB), mentre i picchi di RAM sono comparabili ma leggermente ottimizzati in fase di scrittura (534.01 KB contro 773.31 KB).

**Risultato Ottenuto**: Un incremento prestazionale del ~40% nelle operazioni di I/O del database crittografato, essenziale in un contesto dove il vault viene continuamente decifrato, mutato (es. aggiunta di nuove chiavi) e ricifrato. Esecuzione in constant-time svincolata dalle istruzioni hardware del processore (come AES-NI), limitando drasticamente i vettori per attacchi timing.

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

**Com'è adesso**: Implementazione pura di una "Digital Envelope" asimmetrica/simmetrica. Generazione di una coppia di chiavi effimere X25519, scambio chiave ([[Curve25519|ECDH]]), derivazione della Master Message Key via [[HKDF-SHA256|HKDF]] e cifratura finale in formato JSON (v3).

```python
# crypto_service.py
def _encrypt_mmk_for_recipients(mmk: bytes, ephemeral_priv: X25519PrivateKey, public_keys: list) -> list[str]:
    encrypted_deks = []
    for pk_b64 in public_keys:
        pub_key = X25519PublicKey.from_public_bytes(base64.b64decode(pk_b64))
        shared_key = ephemeral_priv.exchange(pub_key)
        derived_dek = HKDF(algorithm=hashes.SHA256(), length=32, salt=None,                            info=b"ChatControl...").derive(shared_key)
        dek_box = nacl.secret.SecretBox(derived_dek)
        # ... cifratura MMK e append
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

**Fondamento Teorico**: Per confermare la validità del payload in Fernet o in sistemi a blocco puro in modalità CBC accoppiati ad un HMAC, è necessario processare iterativamente l'intero ciphertext, tenere il computo del tag in memoria, validarlo e solo dopo concedere la decifratura (Encrypt-then-MAC). Sfruttando XSalsa20 unito a Poly1305 in modalità stream, ogni "chunk" possiede implicitamente un proprio blocco matematico autenticato, autorizzando lo script a validare e riversare byte in uscita scartando dal buffer quelli già gestiti, azzerando l'accumulo di memoria RAM. (XSalsa20 streaming cypher mentre Poly1305 no).

![[ChatControl_Prima.png]]![[ChatControl_Dopo.png]]
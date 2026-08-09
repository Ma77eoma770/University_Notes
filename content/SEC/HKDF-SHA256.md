---
date:
  - 2026-03-19
tags:
  - Tirocinio
  - Sicurezza
---
> [!info] Intro
> Lo _Shared Secret_ che si genera con [[Curve25519]] è segretissimo, nessuno lo conosce. Ma ha un difetto tecnico: **non è "rumore bianco"**. Essendo il risultato di un'operazione su una curva ellittica (è letteralmente la coordinata X di un punto sul grafico), la sua distribuzione di bit ha dei lievi pattern matematici (struttura algebrica).
> 
> I cifrari moderni come [[XSalsa20-Poly1305|XSalsa20]] sono schizzinosi: esigono chiavi che siano **uniformemente casuali**, ovvero pura "entropia" senza alcuna traccia di pattern. Se gli dai in pasto una chiave con una struttura matematica prevedibile, indebolisci il cifrario.

> [!success] La Soluzione: HKDF-SHA256

### Funzionamento

HKDF sta per _HMAC-based Key Derivation Function_. Il suo scopo non è creare sicurezza dal nulla, ma prendere un materiale crittografico grezzo e "distillarlo" in chiavi perfette.

Lavora in due fasi distinte:
1. Pensa allo Shared Secret come a un succo di frutta con molta polpa. L'Extract usa l'algoritmo di hash HMAC-SHA256 (spesso combinato con un "Salt" casuale) per "strizzare" questo materiale grezzo. L'obiettivo è distruggere ogni traccia della struttura algebrica della curva ellittica, condensando tutta l'entropia (il caos puro) in una chiave cortissima e densissima chiamata **PRK (Pseudorandom Key)**. (Ora abbiamo il nostro rumore bianco puro, ma potrebbe non essere della lunghezza giusta e, soprattutto, non ha un'identità.)
2. HKDF prende la PRK appena generata e la rimescola di nuovo usando HMAC-SHA256, ma questa volta le "appiccica" un'etichetta, ovvero una **stringa di contesto** (Info). Il risultato finale di questa miscelazione è la **DEK (Data Encryption Key)** da 32 byte. Questa è la chiave suprema, perfetta e purissima, che verrà data in pasto a XSalsa20.
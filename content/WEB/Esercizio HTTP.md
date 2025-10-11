---
date:
  - 2025-10-11
tags:
  - WEB
  - HTTP
  - Esercizio
---
```python
import os
from itertools import islice
import requests
import json

from http_utils import download

# User-Agent personalizzato per simulare un browser reale (utile per evitare blocchi da parte del server)
CHROME_UA = "Mozilla/5.0 (Windows NT 11.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/134.0.6998.166 Safari/537.36"

# URL base della PokeAPI
API_BASE_URL = 'https://pokeapi.co/api/v2'
# Endpoint specifico per accedere alla lista dei Pokémon
POKEMON_ENDPOINT = f'{API_BASE_URL}/pokemon/'
# Parametri di ricerca: richiede tutti i Pokémon (fino a 100000)
POKEMON_SEARCH_PARAMS = '?limit=100000&offset=0'

# Percorsi per salvare i dati scaricati
CRY_SAVE_PATH = 'cries'             
POKE_DETAILS_SAVE_PATH = 'pokemons.jsonl'  

if __name__ == '__main__':
    os.makedirs(CRY_SAVE_PATH, exist_ok=True)

    # Costruisce l’URL completo per la richiesta API
    search_query = POKEMON_ENDPOINT + POKEMON_SEARCH_PARAMS
    print(f'asking for all pokemons: {search_query}')

    # Esegue la richiesta HTTP per ottenere la lista completa dei Pokémon
    all_pokemons = requests.get(search_query, headers={'User-Agent': CHROME_UA}).json()
    print(f'got {all_pokemons["count"]} pokemons')

    with open(POKE_DETAILS_SAVE_PATH, 'w') as f:
        for i, pokemon in enumerate(islice(all_pokemons['results'], 20)):
            print(f'{i}. found {pokemon["name"]}: getting details from {pokemon["url"]}')
            
            # Richiede i dettagli completi del Pokémon corrente
            pokemon_detail = requests.get(pokemon['url']).json()
            
            # Scrive i dettagli del Pokémon nel file JSONL (una riga per Pokémon)
            f.write(json.dumps(pokemon_detail) + '\n')

            # Se il Pokémon ha dei "cries" (versi audio), li scarica uno per uno
            for cry_key, cry in pokemon_detail.get('cries', dict()).items():
                filename = f"{CRY_SAVE_PATH}/{cry_key}_{cry.split('/')[-1]}"
                # Scarica il file audio utilizzando la funzione "download"
                download(cry, filename)

```

![[PokemonHTTPex_out.png]]

```python
import json
import requests
import shutil
from ratelimit import limits, sleep_and_retry  # decoratori per limitare la frequenza delle richieste HTTP

# Limite di chiamate per rate limiting
CALLS = 1          # numero massimo di chiamate per periodo
PERIOD = 1.5       # periodo in secondi (1 richiesta ogni 1.5 secondi)

def download(url, path, **kwargs):
    """
    Scarica un file da un URL e lo salva localmente nel percorso specificato.
    Usa lo streaming per non caricare tutto il file in RAM.
    """
    kwargs['stream'] = True  # abilita lo streaming dei dati
    r = requests.get(url, **kwargs)  # esegue la richiesta HTTP
    if r.status_code == 200:  # verifica che la richiesta sia andata a buon fine
        with open(path, 'wb') as f:  # apre il file in modalità binaria
            r.raw.decode_content = True  # decodifica correttamente il contenuto
            shutil.copyfileobj(r.raw, f)  # copia i dati dal contenuto della risposta al file locale

@sleep_and_retry
@limits(calls=CALLS, period=PERIOD)
def http_ratelimited(method, url, **kwargs):
    """
    Esegue una richiesta HTTP rispettando il rate limit impostato.
    - method: funzione HTTP (es. requests.get)
    - url: URL della richiesta
    - kwargs: altri parametri della richiesta
    """
    return method(url, **kwargs)  # restituisce la risposta HTTP

def pretty_print_json(data):
    """
    Stampa in modo leggibile un oggetto JSON (dizionario o lista).
    Se i dati sono vuoti, stampa un messaggio di avviso.
    """
    if data:
        print(json.dumps(data, indent=4, sort_keys=True))
    else:
        print("Nessun dato da visualizzare.")  # fallback se i dati sono vuoti

def print_all_headers(headers):
    """
    Stampa tutti gli header HTTP di una risposta in modo leggibile.
    """
    print("\n--- Headers ---")
    for key, value in headers.items():  # cicla su tutte le intestazioni
        print(f"  {key}: {value}")  # stampa chiave e valore
    print("--------------------------------------")

def get_without_redirect(url, **kwargs):
    """
    Esegue una richiesta GET senza seguire automaticamente i redirect.
    - url: URL della richiesta
    - kwargs: altri parametri della richiesta
    """
    kwargs['allow_redirects'] = False  # disabilita i redirect automatici
    return requests.get(url, **kwargs)  # restituisce la risposta HTTP

```
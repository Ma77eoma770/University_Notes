---
date:
  - 2025-10-14
tags:
  - WEB
  - API
---
Ipotizziamo di dover sviluppare un gioco "Hi-Lo":
- Io penso ad un numero segreto tra 1 e 100
- Tu provi ad indovinarlo con al più 10 tentativi
- Ad ogni tentativo, ti dirò se il tuo tentativo era troppo alto basso o è giusto.

Richiede le seguenti **funzionalità**:
1. **Avviare** una nuova partita.
2. **Accettare** un tentativo (fino a 10) e restituire "hi" o "lo" o "correct".
3. **Resettare** una partita, generando un nuovo numero segreto.
4. **Ottenere** la lista di ogni tentativo in una partita, con i relativi risultati.
5. **Ottenere** la lista di tutte le partite, con il risultato finale (vittoria/sconfitta) e il numero di tentativi

```yaml
openapi: 3.0.4
info:
  title: Hi-Lo The Game
  description: |-
    This is a game a game in which clients can:
      - bet on a number between 1 and 100
      - receive a response from the oracle:
        - hi if the bet is too high compared to the game specific number
        - lo if it is low
        - correct if it is the secret number
  version: 1.0.12
paths:
  /games:
    post:
      summary: create a new game
      description: |
        create a new game, instantiating a corresponding secret number associated with it. If successful returns the associated game id.
      responses:
        "200":
          description: request accepted; |
            game id in response payload
          content:
            application/json:
              schema:
                type: integer
    get:
      summary: retrieve all past and current games
      description: |
        obtain the list of all games, with the final result (win/lose) and the number of guesses
      responses:
        "200":
          description: |
            request accepted; game ids in response payload
          content:
            application/json:
              schema:
                type: array
                items: 
                  type: object
                  properties:
                    id:
                      type: number # Anche solo integer
                      format: integer
                      example: 2
                    outcome:
                      type: string
                      #enum: ["win", "lose", "playing"]
                      enum:
                        - win
                        - lose
                        - playing
                      example: "win"
                      description: |
                        Playing if in progress. win/lose if finished
                    guesses:
                      type: number
                      format: integer
                      description: |
                        The number of guesses till the outcome
                      example: 3
```

![[HiLo-game 1.png]]

![[HiLo-game 2.png]]

![[HiLo-game 3.png]]

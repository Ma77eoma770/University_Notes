---
date:
  - 2025-10-11
tags:
  - WEB
  - Json
  - Yaml
---
## Json (JavaScript Object Notation)

> [!success] Formato leggero per lo scambio di dati (e.g. dati inviati con richieste e risposte RESTful)

Viene usato anche per archiviare dati (.json). E' facile da leggere e scrivere per gli umani e facile da analizzare e generare per le macchine

Esistono solo due concetti:
- **Object (oggetto)**: Collezione non ordinata di coppie **nome : valore** racchiusa tra due {}
	- **nome**: stringa tra "", unica all'interno dell'oggetto (**chiave**)
	- **valore**: numero, stringa, booleano, null, array, object (**valore**)

```json
{
	"WASA": {
		"name": "Web and Software Architecture",
		"semester": 1
	}
}
```

- **Array (array)**: Elenco ordinato di valori, separati da virgole, racchiuso tra due []

```json
{
	"wasaWeekdays": ["tuesday","thursday"]
}
```

### Esempio Json

```json
{
	"anObject": {
		"aNumber": 42,
		"aString": "This is a string",
		"aBoolean": true,
		"nothing": null,
		"anArray": [
			1,
			{ "name": "value", "anotherName": 12},
			"something else"
		]
	}
}
```

## Yaml (YAML Ain't Markup Language)

Linguaggio di serializzazione dei dati pensato per gli umani:
1. File di configurazione
2. Archiviare dati (.yaml)
3. Scambiare dati.
### Esempio Yaml

```yaml
anObject:
	aNumber: 42
	aString: This is a string
	aBoolean: true
	nothing: null
	anArray:
		- 1
		- anotherObject:
			  someName: some value
			  someOtherName: 1234
		- something else
```

## YAML superset di JSON


> [!hint] I file JSON sono YAML validi

- {} accettati per le mappe (oggetti).
- [] accettati per le liste (array).
-  # Qualsiasi cosa dopo un simbolo cancelletto è un commento.
- I documenti possono opzionalmente iniziare con --- e finire con ...
- Un file può contenere più documenti.
- con la pipe (|) si possono spaziare più linee.

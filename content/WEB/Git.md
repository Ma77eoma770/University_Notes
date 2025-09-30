---
date:
  - 2025-09-30
tags:
  - WEB
  - Git
---

> [!hint] Key Idea
> Quando si lavora in gruppo o da soli vengono fatte più versioni di un solo documento/file. Il problema principale ricade sul fatto che serve un qualche tipo di versionamento e regole generali (come naming convention) per avere una raccolta sana di file.

![[git meme 1.0.png|500]]
## Perchè git?
Git (e github) è utile per:
- Tracciare e valutare modifiche.
- Comparare diverse versioni/implementazioni (e.g. diverse ottimizzazioni di un algoritmo).
- Cercare bug per tempo o per feature.
- Etichettatura delle versioni consistente.
- Tracciamento delle modifiche.
- Metadati (data, autori).
- Versioni parallele del codice (branches) con possibilità di merge.
- Sincronizzazione (tra computer e tempo).
## Nomenclatura
- **Repository**: Set di commit, branch e tag
	- Assumiamo che 1 progetto: 1 repo
- **Working Copy**: Set di file tracciati nella copia locale
- **Commit**: Un'istantanea del repository in un determinato momento.
- **Branch**: Una linea di sviluppo. E un insieme ordinato di commit.
- **Merge**: L'azione di fondere due o più branch.
- **Tag**: Un'etichetta personalizzata allegata a un commit.
- **Fork**: Un nuovo repository che è una copia di uno esistente.
- **Pull/Merge request**: Una richiesta di unire il codice da un fork o branch al repository/branch padre.

### Commit
Un'istantanea del repository in un determinato momento. Identificato dallo SHA1 del commit stesso ( a volte troncato). Campi:
1. Data+autore, data+committer
2. Commento **obbligatorio**
3. 0,1, o più genitori
4. Tree: hash d tutti (~) i file nel commit
   
![[git meme 1.1.png|500]]
### Staging

[[Immagine staging ppt]]
Il commit può contenere un sottoinsieme delle modifiche, anche un suo sottoinsieme. E necessario aggiungere alla staging area i cambiamenti

### Branch
E' una linea di sviluppo, ovvero un set ordinato di commit collegati in un DAG (Grafo acicliclo diretto) che punta sempre all'ultimo commit e inizia dal primo orfano (primo commit del repo). Il primo commit crea il primo branch. In questo modo ognuno puo lavorare sulla propria parte o un singolo puo lavorare su più "idee di implementazione".
[[mmagini ppt]]

### Head 
Posizione corrente nell storia di un branch = working copy.
[[mmagini ppt]]

Puo puntare ad un commit, branch o tag.
*Checkout* è la primitiva corrispondente che aggiorna la working copy. Se non punta ad un branch: detached head -> no commit.

### Tag
[[mmagini ppt]]
E un'etichetta per un commit (e.g. versioni software) sempre meglio non cancellarli

## Operazioni
Si possono fare varie operazioni con git:

## Merge
Fonde i cambiamenti tra due branch. Se si fa merge *verso* un branch la destinazione contiene tutti e due i cambiamenti mentre l'origine rimane immutata. Per fare mere ci sono tre strategie:
1. Fast Forward
2. Merge Commit
3. Rebase

### Fast Forward

Assumiamo di fare merge di B in A. Valido se il branch B è una continuazione di A.
[[mmagini ppt]]

### Merge Commit

Crea un nuovo commit con due genitori
[[mmagini ppt]]

### Rebase

Si tratta di un revisionismo storico ovvero modifica la storia in modo che sia lineare e applica fast-forward. Ricrea ogni commit non in comune tra A e B dopo l'ultimo in A. I commit originali nel branch, ora spostato, rimangono appesi (dangling)
[[mmagini ppt]]

## Comandi Essenziali

- git init: Inizia un repository.
- git status: Visualizza lo stato.
- git add: Aggiunge allo staging.
- git commit -m: Crea un commit.
- git log: Visualizza la storia


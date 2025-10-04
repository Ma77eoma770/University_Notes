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
- [[Commit]]: Un'istantanea del repository in un determinato momento.
- [[Branch]]: Una linea di sviluppo. E un insieme ordinato di commit.
- **Merge**: L'azione di fondere due o più branch.
- [[Tag]]: Un'etichetta personalizzata allegata a un commit.
- **Fork**: Un nuovo repository che è una copia di uno esistente.
- **Pull/Merge request**: Una richiesta di unire il codice da un fork o branch al repository/branch padre.
- [[Head]]: Posizione corrente in un branch.
- [[Staging]]: Fase intermedia in cui si decide cosa mettere nel commit.
## Merge

Quando due branch non sono sincronizzati entra in gioco il merge che fonde i cambiamenti tra due branch. Se si fa merge *verso* un branch la destinazione contiene tutti e due i cambiamenti mentre l'origine rimane immutata. Per fare mere ci sono tre strategie:
1. Fast Forward
2. Merge Commit
3. Rebase
### Fast Forward

Assumiamo di fare merge di B in A. Valido se il branch B è una continuazione di A.

![[Fast Forward Before.png|700]]
![[Pasted image 20250930194507.png]]
### Merge Commit

Crea un nuovo commit con due genitori
![[Pasted image 20250930194539.png]]
![[Pasted image 20250930194552.png]]
### Rebase

Si tratta di un revisionismo storico ovvero modifica la storia in modo che sia lineare e applica fast-forward. Ricrea ogni commit non in comune tra A e B dopo l'ultimo in A. I commit originali nel branch, ora spostato, rimangono appesi (dangling)
[[mmagini ppt]]

## Comandi Essenziali

- git init: Inizia un repository.
- git status: Visualizza lo stato.
- git add: Aggiunge allo staging.
- git commit -m: Crea un commit.
- git log: Visualizza la storia.
- git checkout: Cambi branch/commit.
- git branch: Fa vedere tutto ciò che riguarda i branch.
- git remote -v: ...
- prova 6

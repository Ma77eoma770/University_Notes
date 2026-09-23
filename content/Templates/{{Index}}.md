---
tags:
  - Index
Professor Name:
Class:
Site:
Professor Mail:
Difficulty:
A.T.:
---
## Lezioni

```dataview
TABLE WITHOUT ID
  file.link AS "Lezione / Argomento",
  dateformat(date, "dd/MM/yyyy") AS "Data",
  file.etags AS "Tag"
WHERE file.folder = this.file.folder AND file.name != this.file.name
SORT date ASC
```

# Scadenze

- [ ]
- [ ]
- [ ]
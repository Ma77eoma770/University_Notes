---
date:
  - 2025-10-09
tags:
  - AUT
  - Automi
  - NFA
---

> [!info] Definizione NFA generalizzato (GNFA):
> Un GNFA è semplicemente un NFA ma ora abbiamo la possibilità di mettere delle etichette agli archi che possono essere espressioni regolari. Inoltre assumiamo senza perdere di generalità che il GNFA sia in **forma canonica** 

> [!info] Definizione informale: forma canonica
> Nella forma canonica si ha solo un $q_{start}$ (iniziale) e un $q_{acc}$ (finale) che hanno rispettivamente solo archi uscenti e solo archi entranti. Inoltre sono presenti tutti i possibili archi, quelli che non esistono vengono segnati con $\emptyset$

Quindi per ora abbiamo:
- **Lo stato iniziale**: ha solo archi **uscenti** verso tutti gli altri stati.
- **Lo stato finale**: ha solo archi **entranti**
Esclusi quei due per ogni coppia di stati c'è un arco
In pratica: $G= (Q, \Sigma, \delta, q_{start}, q_{acc})$
$$
\begin{gather}
\delta : Q \setminus \{q_{acc}\} \times Q \setminus \{q_{start}\} \Rightarrow \mathcal{R}= re(\Sigma) \\ \mathcal{R} \equiv \text{insieme di espressioni regolari su } \Sigma
\end{gather}
$$

> [!example] Domanda: Perchè la forma canonica è senza perdita di generalità (wlog)?

> [!hint] Posso sempre aggiungere: $q_{start},q_{acc}$ e gli archi mancanti tra ogni coppia di stati



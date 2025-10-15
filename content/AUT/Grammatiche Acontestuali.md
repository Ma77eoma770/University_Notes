---
date:
  - 2025-10-15
tags:
  - AUT
  - Grammatica_Acontestuale
---
Introduciamo un modello di computazione più potente. Utile in diverse applicazioni (es. parser).
Vedremo che le grammatiche coincidono anche con un diverso tipo di automa.
Ad esempio sarà molto facile dare una grammatica che genera le stringhe $0^n1^n$ con $n \geq 0$. Prendiamolo come esempio:
$$
\begin{gather}
A \Rightarrow 0A1 \\
A \Rightarrow B \\
B \Rightarrow \#
\end{gather}
$$
Dove identifichiamo come:
- **Terminali**: 0, 1, #
- **Variabili**: A, B
- **Regole**: Ogni "riga di istruzione"

> [!hint] Ci sarà sempre una variabile iniziale da dove partire

La grammatica genera stringhe:
1. Scrivo variabile iniziale
2. Sostituisco variabili con altre espressioni seguendo le regole della grammatica
3. Ripeto fino a che non ho più variabili

> [!example] Esempio
> $A \overset{R_1}{\Rightarrow} 0A1 \overset{R_1}{\Rightarrow} 00A11 \overset{R_1}{\Rightarrow} 000A111 \overset{R_2}{\Rightarrow} 000B111 \overset{R_3}{\Rightarrow}000\#111$  

Per ogni **produzione** posso associare un **albero sintattico**

![[Albero sintattico.png]]

Prendiamo un'altra grammatica G:
$$
\begin{gather}
(R_1) \quad& E \Rightarrow E+E \\
(R_2) \quad& E \Rightarrow E*E \\
(R_3) \quad& E \Rightarrow (E) \\
(R_4) \quad& E \Rightarrow 0|1|2...|9
\end{gather}
$$

G, ad esempio, genera $(3+4)*4$ ma non $(3+4+)*4$
$$
E \overset{R_2}{\Rightarrow} E*E \overset{R_3}{\Rightarrow}(E)*E \overset{R_1}{\Rightarrow} (E+E)*E \overset{R_4^3}{\Rightarrow} (3+4)*4
$$

> [!info] Definizione: CFG (Context Free Grammar)
> Una CFG è una tupla $(V,\Sigma,R,S)$ dove:
> - V è un insieme finito di variabili
> - $\Sigma$ è un insieme finito di terminali
> - $(V \cap \Sigma) = \emptyset$
> - E è un insieme di regole
> - S è una variabile iniziale

Se $u,v,w \in \Sigma \cup V$ e $(A \Rightarrow w) \in R$ diremo che $uAv$ produce $uwv$ e lo scriviamo $uAv \Rightarrow uav$.
Diciamo anche che $u$ deriva $v$, $u \overset{*}{\Rightarrow} v$ se:
- $u=v$
- $\exists \ u_1,...,u_k \ k \geq 0 \ t.c. \ u \Rightarrow u_1 \Rightarrow u_2 \Rightarrow ... \Rightarrow u_k \Rightarrow v$ 

> [!hint] Questo ci permette di definire il linguaggio associato ad un CFG

Sia $G=(V,\Sigma,R,S)$, allora $L(G) = \{ w \in \Sigma^* \ : \ S \overset{*}{\Rightarrow}w\}$

> [!example] Esempio
> $G= (V= \{S\}, \Sigma = \{a,b\},R,S$
> $R: \ S \Rightarrow aSb|SS|\epsilon$

Iniziamo a vedere alcune tecniche per la costruzione di grammatiche:
1. Unione di grammatiche
2. Da DFA alle grammatiche
3. Sfruttare la ricorsione

...
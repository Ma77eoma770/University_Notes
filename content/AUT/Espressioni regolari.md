---
date:
  - 2025-10-08
tags:
  - AUT
  - Automi
---
## Intro

> [!info] Definizione: Espressioni regolari
> Sono come espressioni algebriche, ma definiscono linguaggi su un certo alfabeto

> [!example] Esempio: $(0 \cup 1)0^*$
> Sviluppando: $$\begin{gather} (0 \cup 1) &= \{ 0, \ 1\} \\ 0^* &= \{0\}^* \\ (0 \cup 1) 0^* &= \{0, \ 1\} \circ \{0\}^* \end{gather}$$

## Definizione Formale

Sia $\Sigma$ un alfabeto. Espressione regolare su $\Sigma$, denominata $re(\Sigma)$ è definita ricorsivamente:

**Caso base**: $$\begin{cases} \emptyset \in re(\Sigma) \\ \epsilon \in re(\Sigma) \\ a \in re(\Sigma) , \ a \in \Sigma \end{cases}$$

**Caso induttivo**: $$\begin{cases} R_1 \cup R_2 \quad &R_1,R_2 \in re(\Sigma) \\ R_1 \circ R_2 \quad &R_1,R_2 \in re(\Sigma) \\ (R_1)^* \quad &R_1 \in re(\Sigma) \end{cases}$$

Ogni espressione regolare ha associato un linguaggio $L(r) \ t.c. \ r \in re(\Sigma)$ 

**Caso base**:  $$\begin{cases} L(r) = \emptyset, \quad &r=\emptyset \\ L(r) = \{\epsilon\}', \quad &r= \epsilon \\ L(r) = \{a\}', \quad &r = a \end{cases}$$

**Caso Induttivo**: $$\begin{cases} L(r) = L(R_1) \cup L(R_2) \quad & se \ r=R_1 \cup R_2 \\ L(r) = L(R_1) \circ L(R_2) \quad & se \ r = R_1 \circ R_2 \\ L(r) = (L(R_1))^* \quad & se \ r = R_1^* \end{cases}$$
## Uguaglianza $REG \equiv L(re)$ 

Un linguaggio è regolare $\Leftrightarrow$ esiste un'espressione regolare che lo descrive:$$REG \equiv L(re)$$
...

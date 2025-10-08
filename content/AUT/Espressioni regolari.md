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

Esempio: $(0 \cup 1)0^*$ 

Sviluppando: 
 $$
 \begin {gather} (0 \cup 1) = \{ 0, \ 1\} \\ 0^* = \{0\}^* \\ (0 \cup 1) 0^* = \{0, \ 1\} \circ \{0\}^* \end {gather}
 $$
 
---
## Definizione Formale

Sia $\Sigma$ un alfabeto. Espressione regolare su $\Sigma$, denominata $re(\Sigma)$ è definita ricorsivamente:

**Caso base**: 
$$
\begin{cases} \emptyset \in re(\Sigma) \\ \epsilon \in re(\Sigma) \\ a \in re(\Sigma) , \ a \in \Sigma 
\end{cases}
$$

**Caso induttivo**: 
$$
\begin{cases} R_1 \cup R_2 \quad &R_1,R_2 \in re(\Sigma) \\ R_1 \circ R_2 \quad &R_1,R_2 \in re(\Sigma) \\ (R_1)^* \quad &R_1 \in re(\Sigma) 
\end{cases}
$$

Ogni espressione regolare ha associato un linguaggio $L(r) \ t.c. \ r \in re(\Sigma)$ 

**Caso base**:  
$$
\begin{cases} L(r) = \emptyset, \quad &r=\emptyset \\ L(r) = \{\epsilon\}', \quad &r= \epsilon \\ L(r) = \{a\}', \quad &r = a 
\end{cases}
$$

**Caso Induttivo**: 
$$
\begin{cases} L(r) = L(R_1) \cup L(R_2) \quad & se \ r=R_1 \cup R_2 \\ L(r) = L(R_1) \circ L(R_2) \quad & se \ r = R_1 \circ R_2 \\ L(r) = (L(R_1))^* \quad & se \ r = R_1^* \end{cases}
$$

---
## Uguaglianza $REG \equiv L(re)$ 

Un linguaggio è regolare $\Leftrightarrow$ esiste un'espressione regolare che lo descrive:$$REG \equiv L(re)$$

### Dimostrazione

Dimostriamo prima che $L(re) \subseteq REG$:

Data l'espressione regolare $r$, costruisco un NFA/DFA $N_r \ t.c. \ L(N_r) = L(r)$.
Nel farlo ci aiutiamo con la [[Espressioni regolari#Definizione Formale|definizione ricorsiva]] che abbiamo considerato.

**Caso Base**: 
- $r = a, \quad a \in \Sigma$
  $$
  \begin{gather} N_r = (\{q_1,q_2\},\Sigma,\delta,q_1,\{q_2\}) \\ \delta =\begin{cases} (q_1,a) = q_2 \\ (q,b)= \emptyset \quad se \ q \ne q_1, \ b \ne a \end{cases} 
  \end{gather}
  $$
![[casobase1.png|400]]

- $r = \epsilon$ 
  $$
  \begin{gather} N_r = (\{q_1\}, \Sigma, \delta,q_1,\{q_1\}) \\ \delta(q_1,b)= \emptyset \quad \forall b\in\Sigma 
  \end{gather}
  $$
![[casobase2.png|400]]

- $r = \emptyset$ 
  $$
  \begin{gather} N_r = (\{q_1\}, \Sigma, \delta,q_1,\{ \emptyset\}) \\ \delta(q_1,b)= \emptyset \quad \forall b
  \end{gather}
  $$
![[casobase3.png|400]]

**Caso induttivo**:
Se $R_1 \cup R_2 \ \text{per induzione}$:
$$
\begin{gather}
\exists M_1,M_2 \ t.c. L(M_1)=L(R_1), \ L(M_2) = L(R_2) \\
\Rightarrow \exists M \ t.c. \ L(M)= L(R_1) \cup L(R_2) \quad \text{per il teorema di chiusura di REG} 
\end{gather}
$$
Stesso discorso per le altre operazioni.

---

**Esempio**: $(ab \cup a)^*$, NFA corrispondente?
 
... Disegni

---

Ora dobbiamo dimostrare che $REG \subseteq L(re)$ ovvero partiamo da un NFA N per ottenere un $L \in REG$   ($L(N) = L$).
Per facilitare la conversione in un'espressione regolare introduciamo l'**NFA generalizzato (GNFA)** 

...  
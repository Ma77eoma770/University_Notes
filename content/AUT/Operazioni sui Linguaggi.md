---
date:
  - 2025-09-29
tags:
  - AUT
  - Linguaggi_Regolari
---
## Operazioni

Fissiamo $\Sigma = \{ 0,1\}$. Per $n \in \mathbb{N}, \ [u] = \{1,2,...,n\}$.
Siccome i [[linguaggi regolari]] sono un insieme di stringhe posso considerare su di essi operazioni.

- **Unione**: $L_1 \cup L_2 = \{ x \in \Sigma^* \ | \ x \in L_1 \ oppure \ x \in L_2 \}$
- **Intersezione**: $L_1 \cap L_2 = \{ x \in \Sigma^* \ | \ x \in L_1 \ e \ x \in L_2 \}$
- **Complemento**: $\bar{L_1} = \{ x \in \Sigma^* \ | \ x \notin L_1 \}$
- **Concatenazione**: Se abbiamo (per le stringhe)
$$ 
\begin{gather} 
x = a_1,...,a_n \ , \quad y=b_1,...,b_m \quad n,m \gt 0 \\ xy = a_1,...,a_n,b_1,...,b_m \in \Sigma^* \\ \epsilon x = x \epsilon = x \\ \begin{cases} x \epsilon = x \\ x(ya)=(xy)a \\ x,y \in \Sigma ^*, \ a \in \Sigma \end{cases} 
\end{gather}
$$ Posso concatenare anche linguaggi:  $L_1 \circ L_2 = \{xy \ : \ x \in L_1 \ e \ y \in L_2$ .

> [!hint] Sono tutte le possibili combinazione dei due insiemi prendendo uno dei due come "primo"

> [!example] Esempio 
> $\Sigma = \{a,b\}, \quad L_1=\{a,ab,ba\}, \quad L_2=\{ab,b\} \quad L_1 \circ L_2 = \{aab, ab, abab, abb, baab, bab \}$
 
> [!error] N.B.  $L_1 \circ L_2 \ne L_1 \cup L_2$

- **Potenza**: Per le stringhe: 
  $$
  \begin{gather}
  x^n=x...x \quad n \ volte \quad x \in \Sigma^* \\ x^0=\epsilon \\ x^{u+1}=x^ux \end{gather}
  $$
Lo stesso vale per i linguaggi 
$$
\begin{gather} 
L^0 = \{\epsilon\} \\ L^{u+1}=L^u \circ L 
\end{gather}
$$

> [!hint] Sono tutte le possibili combinazioni (con n passi dove n e l'esponente) combinando se stesso

> [!example] Esempio
> $L = \{a,ab,ba\} \quad L^2 = \{ aa,aab,aba,abab,abba,baa,baab.baba\}$

- **La Star (\*)**: $L^* = \bigcup_{u \ge 0} \ L^n = \{ \epsilon \} \cup L^1 \cup L^2 \cup \ ... \ \cup L^n$

> [!example] Esempio
> $L=\{a,b\} \quad L^*= \{ \epsilon,a,b,aa,ab,bb,ba,aaa,...\}$


## Proprieta 

Vogliamo studiare la proprieta di chiusura dei linguaggi regolari. Ovver: se $L_1, \ L_2 \in \ REG$, posso dire che $L_1 \cup L_2 \in \ REG$? $L_1 \cap L_2$? $\bar{L_1}$? $L_1^*$?


> [!NOTE] Teorema: REG chiusa per unione
> **Intuizione**: $L_1, L_2 \in \ REG \rightarrow \exists M_1,M_2 \in \ DFA \ t.c. \ L(M_1)=L_1, \ L(M_2)=L_2$ Devo definire M t.c. $L(M) = L_1 \cup L_2$.
> 
> **Problema**: Dato x candidato non posso andare prima a vedere se $M_1(x)$ accetta (l'automa avrebbe gia esaurito la stringa e non potrebbe verificarla per $M_2$).
> 
> **Idea**: Devo farla in parallelo su $M_1,M_2$ e accettare $\Leftrightarrow$ uno dei due automi accetta.

...
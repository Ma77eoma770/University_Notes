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
$$
 Posso concatenare anche linguaggi:  $L_1 \circ L_2 = \{xy \ : \ x \in L_1 \ e \ y \in L_2$ .

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


## Proprieta (Determinismo)

Vogliamo studiare la proprieta di chiusura dei linguaggi regolari. Ovver: se $L_1, \ L_2 \in \ REG$, posso dire che $L_1 \cup L_2 \in \ REG$? $L_1 \cap L_2$? $\bar{L_1}$? $L_1^*$?


> [!NOTE] Teorema: REG chiusa per unione
> **Intuizione**: $L_1, L_2 \in \ REG \rightarrow \exists M_1,M_2 \in \ DFA \ t.c. \ L(M_1)=L_1, \ L(M_2)=L_2$ Devo definire M t.c. $L(M) = L_1 \cup L_2$.
> 
> **Problema**: Dato x candidato non posso andare prima a vedere se $M_1(x)$ accetta (l'automa avrebbe gia esaurito la stringa e non potrebbe verificarla per $M_2$).
> 
> **Idea**: Devo farla in parallelo su $M_1,M_2$ e accettare $\Leftrightarrow$ uno dei due automi accetta.

Per l'unione e per l'intersezione possiamo ancora arrivarci con gli elementi che abbiamo ora, successivamente dobbiamo introdurre per facilita il [[Non determinismo]] con cui andremo a rifare la dimostrazione per unione e intersezioni in maniera molto piu chiara.

### Unione & Intersezione

Siano 
$$M_1 = (Q_1, \Sigma, \delta_1,q_{01}, F_1), \ M_2 = (Q_2, \Sigma, \delta_2, q_{02}, F_2) \ t.c.  \ L(M_1) = L_1 \ , \ L(M_2) = L_2$$ 
Devo costruire $M = (Q, \Sigma, \delta, q_0, F) \ t.c. \ L(M) = L$.
Avremo:
- $Q = \{(r_1,r_2) \ t.c. \ r_1 \in Q_1 , \ r_2 \in Q_2 \} = Q_1 \times Q_2$ 
- $\delta = Q \times \Sigma \Rightarrow Q$ $$\delta((r_1,r_2),a) = (\delta_1 (r_1,a) , \ \delta_2(r_2,a)) \in Q$$
- $F = \{ (r_1,r_2) \ | \ r_1 \in F_1 \lor r_2 \in F_2 \} = (F_1 \times Q_2) \cup (F_2 \times Q_1)$ 


> [!warning] La dimostrazione per l'intersezione e analoga l'unica cosa che cambia e invece dell'or logico e insiemistico alla fine ci sarebbero due simboli di intersezione quindi $F_1 \times F_2 = F$

### Concatenazione & Star

Per verificare la chiusura sulla concatenzione e sulla star consideriamo $L_1 \in REG, \ L_2 \in REG$ regolari da cui vorremmo dimostrare che anche $L_1 \circ L_2 \in REG$ e regolare.
Dato $x$ devo "capire" come spezzare $x$ in $x_1 \circ x_2 = x_1 x_2 \ t.c. \ x1 \in L_1 , \ x_2 \in L_2$.
Sembra complicato e per risolverlo agilmente introduciamo il concetto di [[Non determinismo| non determinismo]]

## Proprieta (Non Determinismo)

### Unione & Intersezione

![[Unione chiusa immagine.png]]

- $Q = Q_1 \cup Q_2 \cup \{q_0\}$
- $F=F_1 \cup F_2$
- $\forall q \in Q, \ a \in \Sigma_{\epsilon}$ 
- $\delta(q,a) \begin{cases} \delta_1(q,a),\ se \ q \in Q_1 \\ \delta_2(q,a), \ se \ q \in Q_2 \\ \{q_0^1,q_0^2\}, \ se \ q = q_0 \ a = \epsilon \\ \emptyset, \ se \ q= q_0 \ a \ne \epsilon \end{cases}$ 
### Concatenazione
Dato NFA $N_1,N_2$ per $L_1,L_2$ costruisco NFA per $L_1 \circ L_2$ 

![[Concatenazione chiusura immagine.png]]

$N=(Q,\Sigma,\delta, q_0, F)$
- $q_0=q_0^1$
- $Q = Q_1 \cup Q_2$
- $F = F_2$
- $\forall q \in Q, \ \forall a \in \Sigma_{\epsilon}$: $$\delta(q,a)= \begin{cases} \delta_1(q,a) \quad q \in Q_1,\ q \notin F_1 \\ \delta_1(q,a) \quad q \in F_1, \ a \ne \epsilon \\ \delta_1(q,a) \cup \{ q_0^2\} \quad q \in F_1,\ a= \epsilon \\ \delta_2(q,a) \quad q \in Q_2\end{cases}$$

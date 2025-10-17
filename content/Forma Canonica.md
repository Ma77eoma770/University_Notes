---
date:
  - 2025-10-17
tags:
  - AUT
  - Grammatica_Acontestuale
  - Forma_Canonica
---
## Definizione

Vediamo ora che le [[Grammatiche Acontestuali|CFG]] hanno una forma **canonica/normale**

> [!info] Def: (Forma Normale Chomsky)
> Una CFG è in forma normale se ogni regola è del tipo:
> $A \Rightarrow BC$; $A \Rightarrow a$

Con $A,B,C \in V; \ BC \ne S$ e $a\in\Sigma$
Inoltre è ammessa la regola $S \Rightarrow \epsilon$

> [!hint] Ogni CFG ammette una CFG equivalente in forma normale

## Dimostrazione

Questa dimostrazione si pone come step-by-step per ottenere da qualsiasi CFG un CFG in forma normale seguendo questi passaggi:
1. Aggiungo $S_0$ (Variabile iniziale) insieme alla regola $S_0 \Rightarrow S$
2. Elimino le $\epsilon$-regole ($A \Rightarrow \epsilon$) e per ogni occorrenza di A a destra di una regola aggiungo nuova regola con occorrenza cancellata. Ad esempio:
   $R \Rightarrow uAv$ diventa $R \Rightarrow uv$
   $R \Rightarrow uBvBw$ aggiungo $R \Rightarrow uvBw \quad R \Rightarrow uBvw \quad R \Rightarrow uvw$
3. Elimino le regole unitarie ($A \Rightarrow B$) e per ogni occorrenza di regole $B \Rightarrow u$ aggiungo $A \Rightarrow u$ (A meno che questa non sia stata già eliminata)
4. Gli unici due casi che rimangono sono:
	1. $A \Rightarrow u_1,u_2,...,u_k \quad k \geq 3$ ($A \Rightarrow BCD$) dove eliminiamo parzialmente la parte destra e creiamo $A_1$ come nuova variabile e due (o più) nuove regole che la completano:
	   $A \Rightarrow BCD \quad diventa \quad A \Rightarrow BA_1, \ A_1 \Rightarrow CD$
	2. Se invece abbiamo dei casi come $A \Rightarrow uB$ dove $A,B \in V, \ u \in \Sigma$, sostituiamo $u$ con un nuovo stato $U$ e aggiungo la regola $U \Rightarrow u$ 

## Running example

Partendo da:

$$
\begin{align}
&S \Rightarrow ASA | aB \\
&A \Rightarrow B|S \\
&B \Rightarrow b | \epsilon
\end{align}
$$

### Passo 1
Creo un nuovo $S_0$ che diventerà il nostro stato iniziale

$$
\begin{align}
&S_0 \Rightarrow S \\
&S \Rightarrow ASA | aB \\
&A \Rightarrow B|S \\
&B \Rightarrow b | \epsilon
\end{align}
$$

### Passo 2
Elimino le $\epsilon$-regole:
($B \Rightarrow \epsilon$)
$$
\begin{align}
&S_0 \Rightarrow S \\
&S \Rightarrow ASA|aB|a \\
&A \Rightarrow B|S|\epsilon \\
&B \Rightarrow b
\end{align}
$$
($A \Rightarrow \epsilon$)
$$
\begin{align}
&S_0 \Rightarrow S \\
&S \Rightarrow ASA|aB|a|AS|SA|S \\
&A \Rightarrow B|S \\
&B \Rightarrow b
\end{align}
$$
### Passo 3
Elimino le unitarie:
($S \Rightarrow S$ e $S_0 \Rightarrow S$)
$$
\begin{align}
S_0 &\Rightarrow ASA|aB|a|SA|AS \\
S &\Rightarrow ASA|aB|a|SA|AS \\
A &\Rightarrow B|S \\
B &\Rightarrow b
\end{align}
$$
($A \Rightarrow B$ e $A \Rightarrow S$)
$$
\begin{align}
S_0 &\Rightarrow ASA|aB|a|SA|AS \\
S &\Rightarrow ASA|aB|a|SA|AS \\
A &\Rightarrow b|ASA|aB|a|SA|AS \\
B &\Rightarrow b
\end{align}
$$

### Passo 4
Elimino i due casi rimanenti:
($S_0 \Rightarrow ASA,S_0 \Rightarrow aB, S \Rightarrow ASA,S \Rightarrow aB, A \Rightarrow ASA,A \Rightarrow aB$)
$$
\begin{align}
S_0 &\Rightarrow AA_1|UB|a|SA|AS \\
S &\Rightarrow AA_1|UB|a|SA|AS \\
A &\Rightarrow b|AA_1|UB|a|SA|AS \\
B &\Rightarrow b \\
A_1 &\Rightarrow SA \\
U &\Rightarrow a
\end{align}
$$


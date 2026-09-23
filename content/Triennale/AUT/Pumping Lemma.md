---
date:
  - 2025-10-15
tags:
  - AUT
  - Pumping_Lemma
---
> [!info] Definizione: Pumping Lemma 
> Sia L un linguaggio regolare, sia D l’automa tale che $L(D) = L$, si considera una stringa $w \in L(D)$, ed una sua decomposizione in 3 stringhe concatenate $w = xyz$. Esiste un intero $p \leq |w|$, denotato pumping tale che
> 1. $\forall i \geq 0, xy^iz \in L(D)$
> 2. $|y| \gt 0$
> 3. $|xy| \leq p$

**Dimostrazione** : Sia $D = (Q, \Sigma, \delta, q_{start} , F )$ un automa, e sia $p = |Q|$. Sia w una stringa su $\Sigma$ di $n \geq p$ caratteri definita $w = w_1 w_2 ... w_n$ . Sia ${r_1 , r_2, ..., r_{n+1}}$ la sequenza di stati che D computa su input $w$, ossia:
$$
\delta(r_i , w_i ) = r_{i+1}
$$
Tale sequenza è lunga $n + 1 \geq p + 1$ stati, fra i primi $p + 1$ elementi c’è necessariamente uno stato ripetuto, sia $r_j$ la prima occorrenza di tale stato, e sia $r_l$ la seconda.

Siccome la ripetizione avviene fra le prime $p + 1$ computazioni, si ha che $l \leq p + 1$. Si consideri la seguente scomposizione di $w$:
- $x = w_1 , w_2, ..., w_{j−1}$
- $y = w_j, w_{j+1}, ..., w_{l−1}$
- $z = w_l , w_{l+1},..., w_n$
1. $xy^iz \in L(D)$ perché $x$ parte da $r_1 = q_{start}$ e arriva a $r_j$ , $y^i$ parte da $r_j$ e ritorna su $r_l$ , che è lo stesso stato, e $z$ porta da $r_l$ allo stato finale di accettazione.
2. Essendo che $j \lt l$, allora la dimensione minima di $y$ è 1, in quanto il valore minimo che può assumere $l$ è $j + 1$, ne consegue che $|y| = l − 1 − (j − 1) = l − j \lt 0$.
3. $l \leq p + 1$ ovvero $l − 1 = |xy|  \leq p$.
I tre punti sono dimostrati.

> [!info] Proposizione:
> Se L è un linguaggio regolare, ed L′ un sottoinsieme di L, allora L′ non è necessariamente regolare.


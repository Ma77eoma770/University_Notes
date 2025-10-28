---
date:
  - 2025-10-23
tags:
  - AUT
  - Automi
  - PDA
---
## Intro


> [!info] Definizione informale:
> Un **Automa a Pila** (Push Down Automata PDA) è l'estensione di un DFA che riconosce un linguaggio regolare $PDA \equiv CFG$

PDA: NFA + un pila "LIFO" ovvero uno **stack**.

Ad ogni passo può operare sulla cima dello stack:
- Sostituzione di un simbolo
- Push di un simbolo
- Pop di un simbolo

...disegno...

Alfabeto di pila: $\Gamma_{\epsilon} = \Gamma \cup \{\epsilon\}$ può essere diverso da $\Sigma_{\epsilon}$.
La [[Linguaggi regolari#Funzione di Transizione e Configurazione|funzione di transizione]]:
- **Dominio**: $Q \times \Gamma_{\epsilon} \times \Sigma_{\epsilon}$ 
- **Immagine**: $Q \times \Gamma_{\epsilon}$ (Per il non determinismo sarebbe $\mathcal{P}(Q \times \Gamma_{\epsilon})$)

## Definizioni formali

> [!info] Definizione: PDA
> Tupla $(Q,\Sigma,\Gamma,\delta,q_0,F)$
> - $Q, \Sigma, q_0, F$ come DFA/NFA
> - $\Gamma$ è l'alfabeto finito dello stack
> - $\delta:Q \times \Sigma_{\epsilon} \times \Gamma_{\epsilon} \Rightarrow \mathcal{P}(Q \times \Gamma_{\epsilon})$


> [!example] Che succede in una transizione?

$$
\begin{gather}
(q,c) \in \delta(p,a,b) \\
p,q \in Q ; \quad a \in \Sigma_{\epsilon}, \quad b,c \in \Gamma_{\epsilon}
\end{gather}
$$

> [!hint] Qundi:
> - $a,b,c \ne \epsilon$ come prima $\Rightarrow$ PDA `replace(b,c)`
> - $c \ne \epsilon, b \ne \epsilon, a$ in lettura $\Rightarrow$ PDA `push(c)`
> - $c = \epsilon, b \ne \epsilon, a$ in lettura $\Rightarrow$ PDA `pop(b)`

Le **configurazioni**: Elementi di $Q \times \Sigma^* \times \Gamma ^*$

**Accettazione**: Un PDA M accetta $w=w_1,...,w_n \ t.c. \ w_i \in \Sigma \ se \ \exists r_0,...,r_m \in Q$ e stringhe $s_0,...,s_m \in \Gamma^* \ t.c.:$
- All'inizio, $r_0=q_0 \ e \ s_0= \epsilon$
- $\forall i = 0,...,m$
$$
(r_{i+1},a) \in \delta(r_i,w_{i+1},b)
$$
  dove $s_i=bt$ e $s_{i+1}=at \quad a,b \in \Gamma_{\epsilon};t \in \Gamma^*$  
- $r_m \in F$

## Configurazioni e relazioni


> [!hint] Posso mettere le configurazioni in relazione
> $(p,ax,by) \vdash_M (q,x,cy) \Leftrightarrow (q,c) \in \delta(p,a,b)$
> $a \in \Sigma \quad x \in \Sigma^* \quad b,c \in \Gamma \quad y \in \Gamma^* \quad p,q \in Q$ 

Si può dimostrare la chiusura simmetrica e transitiva di "$\vdash_M^*$", pertanto:
$$
L(M) = \{w \in \Sigma^*: (q_0,w,\epsilon) \vdash_M^* (q,\epsilon,y) \quad q \in F, y \in \Gamma^*\}
$$


> [!example] Nota
> PDA accetta indipendentemente dal contenuto della pila. wlog si può assumere che la pila deve essere vuota.

...esempio1...
...esempio2...

## $CFG \equiv PDA$


> [!hint] Teorema:
> Un linguaggio è acontestuale $\Leftrightarrow \exists$ PDA che lo riconosce

**Dimostrazione**: 2 direzioni
### $\Rightarrow$
 
> [!info] Lemma
> Se L è acontestuale, allora $\exists M \in PDA \ t.c. \ L=L(M)$

**Intuizione**: Il PDA deve riconoscere l'insieme delle stringhe generandole usando le regole della grammatica.
Sia G la grammatica con s variabile iniziale, R le regole:
$$
\begin{gather}
S \Rightarrow V_1aV_2 \\
V_1 \Rightarrow bV_2V_3 | cV_2V_4
\end{gather}
$$
...disegno..

**Ad alto livello**:
1. Inserisce $ nella pila
2. Ripete:
	1. Se in cima alla pila c'è una variabile A, seleziona non deterministicamente una delle regole di G di tipo $A \Rightarrow ...$ e sostituisce A in maniera coerente nella pila.
	2. Se c'è un terminale a, lo tiro fuori e provo a fare **matching** con il prossimo carattere in input. Se non **matcha** rifiuta quel ramo di computazione

**Dimostrazione**: 
Sia $M = (Q,\Sigma,\Gamma,\delta,q_0,F)$
Per semplificare la descrizione abbreviamo le transizioni in questo modo:

...disegno...

In generale permetto:
$$
\begin{gather}
(r,xyz) \in& \delta(q,a,s) \quad u=u_1,...,u_l \\
(q_1',ul) \in& \delta(q,a,s) \\
(q_2',u_{l-1}) \in& \delta(q_1',\epsilon,\epsilon) \\
\vdots \\
(r,u_1) \in& \delta(q_{l-1}',\epsilon,\epsilon)
\end{gather}
$$
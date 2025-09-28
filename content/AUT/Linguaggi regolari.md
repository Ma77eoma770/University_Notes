---
date:
  - 2025-09-27
tags:
  - AUT
  - Linguaggi_Regolari
  - Automi
---
## Intro

> [!info] Definizione: Automa a stati finiti (DFA)
> Il piu semplice modello di computazione. Scorre l'input bit a bit in modo sequenziale "scorrendolo"

> [!example] Esempio di un problema
> Si vuole modellare una porta automatica con sensore che si apre quando qualcuno si trova nelle vicinanze:
> 
| /      | Nessuno | Davanti | Dietro | Entrambi |
| ------ | ------- | ------- | ------ | -------- |
| Chiuso | Chiuso  | Aperto  | Aperto | Aperto   |
| Aperto | Chiuso  | Aperto  | Aperto | Aperto   |
>
> In questo caso un automa che rappresenta il seguente problema puo essere:
> 
> ![[Esempio automa intro.png]]

Un automa si presenta cosi:

![[Esempio automa 1.0.png]]

Dove:
q1,q2,q3: sono **STATI**
q2: e uno **STATO DI ACCETTAZIONE**
->: si chiamano **TRANSAZIONI**

In questo caso questo automa accetta ad esempio la stringa 11101

> [!info] Definizione: DFA
> Un DFA (Deterministic Finite Automa) e una 5-tupla, (Q, Σ, δ, q0 , F ) di cui:
> - Q: Insieme finito degli stati .
> - Σ: L'alfabeto che compone le stringhe in input.
> - δ: Funzione Q x Σ -> Q.
> - q0: Stato iniziale, q0 ∈ Q.
> - F: F ⊆ Q Stati finali (accettazione).

---
## Funzione di Transizione e Configurazione

Se M e un DFA l'insieme delle stringhe riconosciute da M si denota **L(M)** ovvero il linguaggio riconosciuto da M (Puo anche essere che L = Ø) 

Per definire formalmente un linguaggio di un automa e necessario introdurre la **funzione di transizione estesa**.
$$\delta^*: Q \times \Sigma^* \rightarrow Q $$
$$
\begin{cases}
\delta^*(q,\epsilon) & = \delta(q,\epsilon) \\
\delta^*(q,ax) & = \delta^*(\delta(q,a),x) \quad a \in \Sigma \quad x \in \Sigma^*
\end{cases}
$$

Un altro concetto utile e la **Configurazione** (coppia in Q x Σ*):
1. Lo Stato (corrente)
2. Cosa resta da leggere
Dato x ∈ Σ*, la configurazione iniziale e (q0,x)

**Passo Computazionale**: porta da una configurazione ad un'altra rispettando le δ.
**Relazione Binaria**: $(p,ax)\vdash_M (q,x) \Leftrightarrow \delta(p,a)=q$ dove p,q ∈ Q, a ∈ Σ, x ∈ Σ*.

Posso estendere $\vdash_{M^*}$ considerando la chiusura riflessiva e transitiva:
1. $(q,x) \vdash_{M^*}(q,x)$      (riflessivita)
2. $(q,aby)\vdash_M (p,by) \quad , \quad (p,by)\vdash_M (r,y) \Rightarrow (q,aby) \vdash_{M^*}(r,y)$   dove q,p,r ∈ Q; a,b ∈ Σ; y ∈ Σ*

---
## DSF (Linguaggio Accettato)

Diciamo che x ∈ Σ* e accettato da M(Q, Σ, δ, q0 , F ) se
$$
\delta^*(q_0,x)\in F \ oppure \ (q_0,x)\vdash_{M^*}(q,\epsilon) \quad q\in F
$$

Ricordiamo che ε e la stringa vuota. Da cui:
$$
L(M) = \{x \in \Sigma^* : \delta^* (q_0,x) \in F\}
$$

> [!example] Esempio:
> ![[Esempio automa 1.1.png]]
> $$
> (q_1, 011) \vdash_M(q_1,11) \vdash_M (q_2,1) \vdash_M (q_2,\epsilon)
> $$
> $$
> \delta^*(q_1,011)=(q_2,\epsilon) \quad q_2 \in F \quad w=011 \in L(M)
> $$

---
## Finally...

Ora possiamo dare la definizione formale dei Linguaggi Regolari:

> [!info] Definizione: Linguaggi Regolari
> $REG = \{L \subseteq \Sigma^* : \exists \ DFA \ M \ t.c. \ L(M) = L \}$

Vogliamo capire come progettare DFA per un dato linguaggio, facciamo un esempio:

$L = \{ x \in \{0,1\}^* \ |\ x=1y, \ y\in \{0,1\}^* \}$

![[Esempio automa 1.2.png]]

Dobbiamo eseguire una **prova di correttezza**:

DFA accetta $x \Leftrightarrow x \in L$ . Osserviamo che:

$$
\begin{align}
\delta^*(q_1,u) =q_1 \ \forall u\in\{0,1\}^* \\ 
\delta^*(q_2,u) =q_2 \ \forall u\in\{0,1\}^*
\end{align}
$$

Per induzione dimostriamo che $x \in L \Leftrightarrow$ DFA accetta x:

**Base:**  $|x| = 0 \quad se \ x = \epsilon, \ \delta^*(q_0,\epsilon) = \delta(q_0,\epsilon) = q_0 \in F$
**Induttivo**: Sia $n \geq 0, \ |w| \leq n$ supponiamo che:

$$
\delta^*(q_0,w)=
\begin{cases}
q_0 \ se \ w=\epsilon \\
q_1 \ se \ w \ inizia \ con \ 1 \\
q_2 \ se \ w \ inizia \ con \ 0
\end{cases}
$$

Prendo $x \ t.c. \ |x| = u + 1$ e lo penso $x=au$ con $a \in \{0,1\}, \ u \in \{0,1\}^n$
Da cui:
$$
\delta^*(q_0,x)=\delta^*(q_0,au)=\delta^*(\delta(q_0,a),u)
$$
Due casi:
$$
\begin{align}
\delta(q_0,a)=q_2 \quad se \ a=0 \\
\delta(q_0,a)=q_1 \quad se \ a=1
\end{align}
$$

> [!hint] Abbiamo gia dimostrato che se entro in q1,q2 ci rimango . Con quest'ultimo passaggio abbiamo dimostrato che avendo una stringa u (che rappresenta qualsiasi combinazione binaria di lunghezza n) e un ultimo carattere a, in ogni caso, entriamo in q1,q2





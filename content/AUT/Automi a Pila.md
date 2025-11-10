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

Definiamo il PDA:
- $Q= \{Q_{start}, q_{loop}, q_{acc}\} \cup Q'$ 
  $Q'$ sono gli stati ausiliari necessari a fare cio che abbiamo detto sopra
- $q_{start}$ : Stato iniziale
- $q_{acc}$ : Stato finale
Devo definire la $\delta$:
1. Inizializzazione: inserisco `s$` (s la stringa, $ come carattere finale da eliminare alla fine)nella pila, ovvero:
$$
\delta(q_{start},\epsilon,\epsilon) = \{q_{loop},s$\}  
$$
2. Nello stato $q_{loop}$:
	- Se la cima contiene variabile:
$$
\delta(q_{loop},\epsilon,A) = \{q_{loop},w: \forall A \Rightarrow w \ in \ G \}
$$
	- Se la cima contiene terminale $a \in \Sigma$:
$$
\delta(q_{loop},a,a)= \{(q_{loop},\epsilon\}
$$
3. Se la cima contiene $:
$$
\delta(q_{loop},\epsilon,$) = \{(q_{acc},\epsilon\}
$$
### $\Leftarrow$
Se L è riconosciuto da PDA M $\Rightarrow$ è acontestuale.

**Intuizione**: Devo tradurre il PDA in una grammatica equivalente. Senza perdita di generalità possiamo dire che il PDA:
1. Un solo stato accettante
2. Svuota lo stack prima di accettare
3. Ogni transizione fa o `pop()` o `push()`
**Osservazione**: La grammatica deve generare tutte le stringhe che portano M da $q_0$ a $q_{acc}$ con pila vuota.

**Idea**: $\forall p,q  \in Q$ definisco $Apq \in V$. Le regole? Codificano i modi in cui M può passare da p a q.
Ci sono due casi:
- $Apq \Rightarrow aArsb$
- $Apq \Rightarrow AprArq$

**Dimostrazione**: Formalizziamo questa cosa: $M=(Q,\Sigma,\Gamma,\delta, q_0,\{q_{acc}\})$ e assumo sia nella forma di sopra.
La grammatica: G ha $V=\{Apq:p,q \in Q\}$ ed $S=Aq_0q_{acc}$ 
Le regole:
- $\forall p,q,r,s \in Q,u \in \Gamma,a,v \in \Sigma_{\epsilon}$ 
  $se (r,u) \in \delta(p,a,\epsilon) \quad (q,\epsilon) \in \delta(s,b,u) \Rightarrow \quad Apq \Rightarrow aArsb$
- $\forall p,q \in Q, apq \Rightarrow AprArq$
- $\forall p \in Q, App \Rightarrow \epsilon$
**Aff. 1**: Se Apq genera x allora x porta M da p a q con pila vuota
**Aff. 2**: Se x porta M da p a q con pila vuota allora Apq genera x
$$
w \in L(M) \Leftrightarrow S \overset{*}{\Rightarrow}w
$$
M su input w passa da $q_0$ a $q_{acc}$ con pila vuota $\Leftrightarrow S = Aq_0q_{acc} \overset{*}{\Rightarrow} w$ 

**Dim Aff. 1** Per induzione se # passi per derivare x da Apq
	- **Base**: # 1 passo. L'unica regola possibile per derivare x in 1 passo è $App \Rightarrow \epsilon$. Ovviamente $\epsilon$ porta M da p a p con pila vuota.
	- **Passo Induttivo**: Sia vero per # passi $\le k, \ k \ge 1$. Assumo:
	  $Apq \overset{*}{\Rightarrow} x$ in k + 1 passi. La prima sostituzione è (i) $Apq \Rightarrow aA_{rs}b$ oppure (ii) $Apq \Rightarrow A_{pr}A_{rq}$
	  **(i)**: $x = ayb \ e \ A_{rs}\overset{*}{\Rightarrow}y$ in k passi; allora M può andare da r a s con pila vuota. Inoltre: $(r,u) \in \delta (p,a,\epsilon) \quad (q,\epsilon) \in \delta(s,b,u)$. M inizia in p con pila vuota legge a e inserisce u nella pila; passa da $r \Rightarrow s$ senza modificare la pila; poi fa `pop` di u e arriva in q con pila vuota.
	  **(ii)**: $x = yz \ con \ A_{pr} \overset{*}{\Rightarrow} y \quad Arq \overset{*}{\Rightarrow} z$. Inoltre queste derivazioni hanno lunghezza $\le$ k e quindi portano M da p a r e da r a q con pila vuota $\Rightarrow$ M su x va da p a q con pila vuota.  

**Dim Aff. 2** Per induzione sul numero di passi di M
	- **Base**: # 0 passi significa che x = $\epsilon$. La grammatica ha regola $App \Rightarrow \epsilon$
	- **Passo Induttivo**: Sia vero per x passi $\le k \ e \ k \ge 1$. Suppongo:
	  M su x passi da p a q con pila vuota in k + 1 passi. Devo mostrare $Apq \overset{*}{\Rightarrow} x$. Due casi: (i) La pila vuota all'inizio o alla fine; (ii) Si svuota in mezzo.
	  **(i)**: Nella prima mossa M fa `push` di $u \in T'$ e nell'ultima fa `pop`. Sia a il carattere letto nella prima mossa e b nell'ultima. Sia r lo stato dopo il primo stato e s il penultimo.
	  $(r,u) \in \delta (q,a,\epsilon) \quad (q,\epsilon) \in \delta(s,b,u)$.
	  Ma G(rammatica) contiene $Apq \Rightarrow aA_{rs}b$. Sia $x = ayb$; inoltre M va da r as s senza modificare la pila ovvero M su y può passare da r a s con pila vuota. Quanti passi da M su y? $k + 1 - 2 = k-1$. Pertanto $A_{rs}\overset{*}{\Rightarrow}y$ e dunque $Apq \overset{*}{\Rightarrow}x$
	  **(ii)**: Sia r lo stato in cui la pila si svuota. La computazione di M da p ad r e da r ad s ha al più k passi. Sia $x = yz$ dove y corrisponde a $p \Rightarrow r$ e z a quella $r \Rightarrow s$. Per l'ipotesi induttiva: $Apr \overset{*}{\Rightarrow}y \ e \ A_{rq}\overset{*}{\Rightarrow}z \Rightarrow App \overset{*}{\Rightarrow} x$ 
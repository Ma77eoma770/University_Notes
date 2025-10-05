---
date:
  - 2025-10-04
tags:
  - AUT
  - NFA
  - Automi
---
## Definizione
Viene definito un po' "magico": Finora la computazione e stata deterministica ovvero se $D \in DFA$ e $D$ legge $a \in \Sigma$ nello stato $q \in Q$ allora $D$ va in un unico altro stato $p \in Q$.
Nel caso non deterministico questo non vale, infatti posso andare in piu stati con stessa configurazione. Inoltre sono ammessi gli $\epsilon$ - archi ovvero degli archi che consentono di aprire rami di computazione senza leggere nulla. Vale inoltre la regola che $Accetto \Leftrightarrow \exists \ \text{almeno un ramo che accetta}$.

![[Determinismo vs. Non Determinismo.png]]

> [!info] Definizione: NFA
> Un NFA e $(Q, \Sigma, \delta, q_0, F)$ dove $Q, \Sigma, q_0, F$ come i DFA.
> $\delta : Q \times \Sigma_{\epsilon} \Rightarrow \mathcal{P}(Q) \Rightarrow \text{insieme delle parti di a: tutti i possibili sottoinsiemi di Q}$ 
> $\Sigma_{\epsilon} = \Sigma \cup \{ \epsilon \}$ (perche vengono considerati anche gli archi epsilon)

> [!hint] Possiamo considerare un NFA un automa che per la prima volta considera la possibilita di creare piu "thread" di computazione che si occupano di computare varie possibilita a partire dall'input

Prendiamo come esempio $L=\{ x \in \{ 0,1 \}^* \ : \ \text{x ha "1" sulla terz'ultima posizione} \}$:

![[NFA esercizio.png]]

x = 100 viene accettata
x = 1000 non viene accettata
x = 10110 ?

![[Svolgimento es NFA.png]]

## Configurazione e relazione estesa in NFA
Possiamo estendere anche il concetto di [[Linguaggi regolari#Funzione di Transizione e Configurazione|configurazione]]: per NFA N sara una coppia $(q,x) \in a \times \Sigma_{\epsilon}^*$ . Avremo:
$$
\begin{align}
(p,ax) \vdash_M (q,x) &\Leftrightarrow q \in \delta(p,a) \quad x \in \Sigma_{\epsilon}^* \quad a \in \Sigma_{\epsilon} \quad p,q \in Q \\
\delta(p,a) &\in \mathcal{P}(Q)
\end{align}
$$

Quando e che N accetta $w \in \Sigma_{\epsilon}^*?$ $\Leftrightarrow \exists q \in F \ t.c. \ (q_0,w) \vdash_N^* (q, \epsilon)$   dove $\vdash_N^*$: [[Linguaggi regolari#Funzione di Transizione e Configurazione| relazione estesa]].
$$
L(DFA) = REG \quad L(NFA)= \{L: \exists \text{NFA} \ N \ t.c. L(N) = L\}
$$

## Uguaglianza tra NFA e DFA

**Teorema**: REG = L(NFA) ovvero quello riconosciuto in un REG e riconosciuto anche in un NFA
**Dimostrazione**: Devo mostrare che $L(NFA) \subseteq L(DFA) \land L(DFA) \subseteq L(NFA)$.
1. Per $L(DFA) \subseteq L(NFA)$ se :$L \in L(DFA) \exists DFA \ D \ t.c. L(D)=L \ \text{ma allora} \ N=D \ \text{è} \ NFA t.c. L(N)=L$.
2. Per $L(NFA) \subseteq L(DFA)$ se: $L \in L(NFA) \ \exists \ NFA \ N=(Q_N, \Sigma_{\epsilon}, \delta_N, q_0^N, F_N) \ t.c. \ L(N)=L.$ 
   Devo costruire un $DFA \ D=(Q_D, \Sigma, \delta_D,q_0^D, F_D) \ t.c. \ L(D)=L$. 
   (Stessa idea della prova di chiusura per unione)
   - **Caso semplice**: No ε-archi. Allora avremo:
	   - $Q_D = \mathcal{P}(Q_N)$ (monitoriamo tutti i possibili cammini)
	   - $q_0^D = \{q_0^N\}$
	   - $F_D = \{ R \in Q_D \ | \ R \cap F_N \ne \emptyset \}$ dove R contiene uno stato finale di $Q_N$ 
	   - Sia $R \in Q_D \ e \ a \in \Sigma$: $\delta_D(R,a) = \bigcup_{r \in R}\delta_N(r,a)$ 
	   **Di fatto:** D sta emulando deterministicamente tutto il ramo di computazione non-deterministico di N
   - **Caso Generale**: Ci sono ε-archi. Devo tenere traccia degli stati raggiungibili usando ε-archi.
     Sia $R \in Q_D$ e $q_0^D = E(\{q_0^N \})$. Definisco $E(Q_D) = \{q \in Q_N \ | \ q \ \text{puo essere raggiunto da stati in R attraverso} \geq 0 \ \text{ε-archi} \}$  (ovvero lo gli stati $Q_N$ e i $Q_N$ raggiungibili con ε-archi)
     A questo punto: $\delta_D(R,a) = \bigcup_{r \in R} E(\delta_N(r,a))$ 

## Esempio NFA=DFA (kind of...)

Dato il seguente NFA, definire un DFA D t.c. L(D)=L(N):

![[Esercizio NFA=DFA.png|400]]

Definiamo gli stati $Q_D = \{ q_{\emptyset}, q_1, q_2, q_3, q_{\{ 1,2\}}, q_{\{ 1,3\}}, q_{\{ 2,3\}}, q_{\{ 1,2,3\}} \}$ 
$q_0^D= E(\{q_1\})= q_{\{1,3\}}$
$F_D = \{ q_{\{ 1\}}, q_{\{ 1,2\}}, q_{\{ 1,3\}}, q_{\{ 1,2,3\}} \}$
 $\delta_D =$
 - Nello stato $q_2 : \ \delta(q_2,a) = q{\{ 2,3 \}}, \delta(q_2,b)=q_3$
 - Nello stato $q_3 : \ \delta(q_3,a) = q{\{ 1,3 \}}, \delta(q_3,b)=q_{\emptyset}$
 - ...
Dopo aver semplificato otteniamo una cosa del genere:

![[Esercizio NFA=DFA finale|600]]
---
date:
  - 2025-10-04
tags:
  - AUT
  - NFA
  - Automi
---
Viene definito un po' "magico": Finora la computazione e stata deterministica ovvero se $D \in DFA$ e $D$ legge $a \in \Sigma$ nello stato $q \in Q$ allora $D$ va in un unico altro stato $p \in Q$.
Nel caso non deterministico questo non vale, infatti posso andare in piu stati con stessa configurazione. Inoltre sono ammessi gli $\epsilon$ - archi ovvero degli archi che consentono di aprire rami di computazione senza leggere nulla. Vale inoltre la regola che $Accetto \Leftrightarrow \exists \ \text{almeno un ramo che accetta}$.

![[Determinismo vs. Non Determinismo.png]]

> [!info] Definizione: NFA
> Un NFA e $(Q, \Sigma, \delta, q_0, F)$ dove $Q, \Sigma, q_0, F$ come i DFA.
> $\delta : Q \times \Sigma_{\epsilon} \Rightarrow \mathcal{P}(Q) \Rightarrow \text{insieme delle parti di a: tutti i possibili sottoinsiemi di Q}$ 
> $\Sigma_{\epsilon} = \Sigma \cup \{ \epsilon \}$ (perche vengono considerati anche gli archi epsilon)

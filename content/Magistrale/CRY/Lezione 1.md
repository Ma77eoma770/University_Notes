---
date: 2026-09-23
tags:
Class: Cryptography
---
# Unconditional Security

Claude Shannon is the creator of this scheme
[schema]

> [!info] Definition **Enc&Dec**
> $$\begin{aligned}Enc: \mathcal{K} \times \mathcal{M} \Rightarrow \mathcal{C} \\ Dec: \mathcal{K} \times \mathcal{C} \Rightarrow \mathcal{M} \end{aligned}$$

Also we can say: $\forall k \in \mathcal{K}, \forall m \in \mathcal{M}; Dec(k,Enc(k,m))=m$

> [!info] Definition **Perfect Secrecy**
> Let $M$ any distribution $\in \mathcal{M}$ and $K$ the **UNIFORM** distribution on $\mathcal{K}$.
> Then $C=Enc(K,M)$ is a distribution. We say that $\pi=(Enc,Dec)$ **PERFECTLY SECRET** if $$\begin{align} \forall M, \forall m \in \mathcal{M}, \forall c \in \mathcal{C} \\ Pr[M=m] = Pr[M=m | C=c] \end{align}$$

> [!warning] We'll show that is possible but it comes with high price 

gif spiderman


> [!hint] Theorem
> 1. Perfect Secrecy
> 2. $M,C$ are indipendent
> 3. $\begin{align} \forall m,m' \in \mathcal{M}, \forall c \in \mathcal{C} \\ Pr[Enc(K,m)=c]=Pr[Enc(K,m')=c] \end{align}$


> [!example] Proof
> 

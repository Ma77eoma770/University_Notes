---
date: 2026-09-25
tags:
  - Perfect_Secrecy
Class: Cryptography
---

> [!abstract] Overview
> These notes cover the foundational framework of **unconditional security** (also known as *information-theoretic security*), introduced by **Claude Shannon** (1949). We formally define secret-key encryption schemes, Shannon's notion of **Perfect Secrecy**, prove the equivalence of key characterizations, and analyze the **One-Time Pad (OTP)** along with the inherent theoretical limitations of perfectly secure systems.

---

## 1. Secret-Key Encryption Scheme

Consider two legitimate parties, **Alice** and **Bob**, who wish to communicate privately across an insecure public channel monitored by an adversary (eavesdropper). Prior to communication, Alice and Bob share a secret key $k \in \mathcal{K}$.

![[Drawing 2026-09-26 14.29.29.excalidraw 1.svg]]

### Formal Definition
A private-key encryption scheme $\Pi = (\mathrm{Enc}, \mathrm{Dec})$ is defined over three finite sets:
*   $\mathcal{M}$: Message space (plaintext space)
*   $\mathcal{K}$: Key space
*   $\mathcal{C}$: Ciphertext space

and consists of two efficient algorithms:

1.  **Encryption Algorithm ($\mathrm{Enc}$):**
    $$
    \mathrm{Enc}: \mathcal{K} \times \mathcal{M} \to \mathcal{C}
    $$
    Takes as input a secret key $k \in \mathcal{K}$ and a plaintext message $m \in \mathcal{M}$, and outputs a ciphertext $c \in \mathcal{C}$. We denote this as $c = \mathrm{Enc}(k, m)$ or $\mathrm{Enc}_k(m)$.

2.  **Decryption Algorithm ($\mathrm{Dec}$):**
    $$
    \mathrm{Dec}: \mathcal{K} \times \mathcal{C} \to \mathcal{M}
    $$
    Takes as input the shared key $k \in \mathcal{K}$ and a ciphertext $c \in \mathcal{C}$, and outputs a message $m \in \mathcal{M}$.

> [!note] Correctness Property
> A cipher $\Pi = (\mathrm{Enc}, \mathrm{Dec})$ is **correct** if decrypting a valid ciphertext with the corresponding key always recovers the original plaintext:
> $$
> \forall k \in \mathcal{K}, \; \forall m \in \mathcal{M}: \quad \mathrm{Dec}(k, \mathrm{Enc}(k, m)) = m
> $$

---

## 2. Perfect Secrecy (Shannon, 1949)

Let $M$ be a random variable representing the message, taking values in $\mathcal{M}$ according to an arbitrary a priori probability distribution $\Pr[M = m]$.
Let $K$ be a random variable representing the key, distributed **uniformly** over $\mathcal{K}$ and independent of $M$:
$$
\Pr[K = k] = \frac{1}{|\mathcal{K}|}, \quad \forall k \in \mathcal{K}
$$
The ciphertext $C = \mathrm{Enc}(K, M)$ is therefore a induced random variable taking values in $\mathcal{C}$.

> [!quote] Definition: Perfect Secrecy
> An encryption scheme $\Pi = (\mathrm{Enc}, \mathrm{Dec})$ is **perfectly secret** if, for every probability distribution over $\mathcal{M}$, every message $m \in \mathcal{M}$, and every ciphertext $c \in \mathcal{C}$ such that $\Pr[C = c] > 0$:
> $$
> \Pr[M = m \mid C = c] = \Pr[M = m]
> $$

> [!intuition] Intuitive Meaning
> Observing the ciphertext $c$ over the communication channel reveals zero additional information about the plaintext message $m$ beyond what was already known a priori.

---

## 3. Theorem: Equivalent Formulations of Perfect Secrecy

> [!theorem] Theorem (Equivalent Characterizations)
> Let $\Pi = (\mathrm{Enc}, \mathrm{Dec})$ be an encryption scheme. The following statements are logically equivalent:
> 1.  **Shannon's Definition (Posterior equals Prior):**
>     $$
>     \forall M, \; \forall m \in \mathcal{M}, \; \forall c \in \mathcal{C}: \quad \Pr[M = m \mid C = c] = \Pr[M = m]
>     $$
> 2.  **Statistical Independence:**
>     The random variables $M$ and $C$ are statistically independent:
>     $$
>     \Pr[M = m \wedge C = c] = \Pr[M = m] \cdot \Pr[C = c], \quad \forall m \in \mathcal{M}, \; \forall c \in \mathcal{C}
>     $$
> 3.  **Indistinguishability of Ciphertexts:**
>     For every pair of plaintexts $m, m' \in \mathcal{M}$ and every ciphertext $c \in \mathcal{C}$:
>     $$
>     \Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, m') = c]
>     $$

---

## 4. Formal Proofs

### Part 1: Proof $(1) \implies (2)$
By the definition of conditional probability:
$$
\Pr[M = m] = \Pr[M = m \mid C = c] = \frac{\Pr[M = m \wedge C = c]}{\Pr[C = c]}
$$
*   **$(1) \implies (2)$:** If $\Pr[M = m \mid C = c] = \Pr[M = m]$, multiplying both sides by $\Pr[C = c]$ yields:
    $$
    \Pr[M = m \wedge C = c] = \Pr[M = m] \cdot \Pr[C = c]
    $$
---
### Part 2: Proof $(2) \implies (3)$

Assume statement $(2)$ holds, meaning the random variables $M$ and $C$ are statistically independent:
$$
\Pr[M = m \wedge C = c] = \Pr[M = m] \cdot \Pr[C = c], \quad \forall m \in \mathcal{M}, \; \forall c \in \mathcal{C}
$$

By definition of conditional probability, for any message $m \in \mathcal{M}$ such that $\Pr[M = m] > 0$:
$$
\Pr[C = c \mid M = m] = \frac{\Pr[M = m \wedge C = c]}{\Pr[M = m]}
$$

Substituting the statistical independence identity from $(2)$ into the numerator:
$$
\Pr[C = c \mid M = m] = \frac{\Pr[M = m] \cdot \Pr[C = c]}{\Pr[M = m]} = \Pr[C = c]
$$

Since the key $K$ is sampled independently of the message $M$, conditioning the ciphertext on the message yielding $c$ is precisely the probability that the encryption algorithm with input $m$ evaluates to $c$:
$$
\Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, M) = c \mid M = m] = \Pr[C = c \mid M = m] = \Pr[C = c]
$$

Because $\Pr[C = c]$ is completely independent of the choice of $m$, the exact same argument applies to any arbitrary message $m' \in \mathcal{M}$:
$$
\Pr[\mathrm{Enc}(K, m') = c] = \Pr[C = c]
$$

Equating both expressions directly yields:
$$
\Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, m') = c], \quad \forall m, m' \in \mathcal{M}, \; \forall c \in \mathcal{C}
$$

---

### Part 3: Proof $(3) \implies (1)$

Assume condition $(3)$ holds:
$$
\Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, m') = c], \quad \forall m, m' \in \mathcal{M}
$$
Fix an arbitrary message $m \in \mathcal{M}$. Because the key $K$ is sampled independently of $M$, we have:
$$
\Pr[C = c \mid M = m] = \Pr[\mathrm{Enc}(K, M) = c \mid M = m] = \Pr[\mathrm{Enc}(K, m) = c]
$$

We compute the marginal probability $\Pr[C = c]$ by applying the **law of total probability** over the message space $\mathcal{M}$:
$$
\begin{aligned}
\Pr[C = c] &= \sum_{m' \in \mathcal{M}} \Pr[C = c \wedge M = m'] \\
&= \sum_{m' \in \mathcal{M}} \Pr[C = c \mid M = m'] \cdot \Pr[M = m'] \\
&= \sum_{m' \in \mathcal{M}} \Pr[\mathrm{Enc}(K, m') = c] \cdot \Pr[M = m']
\end{aligned}
$$

Using hypothesis $(3)$, $\Pr[\mathrm{Enc}(K, m') = c] = \Pr[\mathrm{Enc}(K, m) = c]$ is constant for all $m' \in \mathcal{M}$, allowing us to factor it out of the summation:
$$
\begin{aligned}
\Pr[C = c] &= \Pr[\mathrm{Enc}(K, m) = c] \cdot \underbrace{\sum_{m' \in \mathcal{M}} \Pr[M = m']}_{= 1} \\
&= \Pr[\mathrm{Enc}(K, m) = c]
\end{aligned}
$$
Hence:
$$
\Pr[C = c] = \Pr[C = c \mid M = m]
$$

Applying **Bayes' Theorem**:
$$
\Pr[M = m \mid C = c] = \frac{\Pr[C = c \mid M = m] \cdot \Pr[M = m]}{\Pr[C = c]}
$$
Substituting $\Pr[C = c \mid M = m] = \Pr[C = c]$:
$$
\Pr[M = m \mid C = c] = \frac{\Pr[C = c] \cdot \Pr[M = m]}{\Pr[C = c]} = \Pr[M = m]
$$
---

## 5. Canonical Application: One-Time Pad (Vernam Cipher)

The classic construction that achieves perfect secrecy is the **One-Time Pad (OTP)**.

### Scheme Specification
*   Let the message length be $\ell$ bits.
*   Spaces: $\mathcal{K} = \mathcal{M} = \mathcal{C} = \{0, 1\}^\ell$.
*   The key $K$ is chosen uniformly at random from $\{0, 1\}^\ell$:
    $$
    \forall k \in \{0, 1\}^\ell: \quad \Pr[K = k] = \frac{1}{2^\ell} = 2^{-\ell}
    $$
*   **Encryption:** Bitwise XOR ($\oplus$)
    $$
    \mathrm{Enc}(k, m) = k \oplus m
    $$
*   **Decryption:** Bitwise XOR ($\oplus$)
    $$
    \mathrm{Dec}(k, c) = k \oplus c
    $$

> [!check] Correctness Verification
> By associativity and the self-inverse property of bitwise XOR ($x \oplus x = 0^\ell$):
> $$
> \mathrm{Dec}(k, \mathrm{Enc}(k, m)) = k \oplus (k \oplus m) = (k \oplus k) \oplus m = 0^\ell \oplus m = m
> $$

### Proof of Perfect Secrecy for OTP

By property $(3)$, to prove that OTP is perfectly secret, we show that for any pair of messages $m, m' \in \mathcal{M}$ and any ciphertext $c \in \mathcal{C}$:
$$
\Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, m') = c]
$$

Let $m \in \mathcal{M}$ and $c \in \mathcal{C}$ be fixed. Conditioning on the event $M = m$:
$$
\begin{aligned}
\Pr[\mathrm{Enc}(K, M) = c] &= \Pr[\mathrm{Enc}(K, M) = c \mid M = m] \\
&= \Pr[K \oplus M = c \mid M = m] \\
&= \Pr[K \oplus m = c \mid M = m] \\
&= \Pr[K = c \oplus m]
\end{aligned}
$$

Since the key $K$ is uniformly distributed over $\{0, 1\}^\ell$ and chosen independently of the message:
$$
\Pr[K = c \oplus m] = 2^{-\ell}
$$

By following the exact same steps for any other message $m' \in \mathcal{M}$:
$$
\begin{aligned}
\Pr[\mathrm{Enc}(K, M) = c] &= \Pr[\mathrm{Enc}(K, M) = c \mid M = m'] \\
&= \Pr[K \oplus m' = c \mid M = m'] \\
&= \Pr[K = c \oplus m'] \\
&= 2^{-\ell}
\end{aligned}
$$

Thus:
$$
\Pr[\mathrm{Enc}(K, m) = c] = \Pr[\mathrm{Enc}(K, m') = c] = 2^{-\ell}
$$

---

## 6. Fundamental Limitations of Perfect Secrecy

> [!warning] The High Price of Unconditional Security
> Shannon proved that perfect secrecy comes with severe practical costs:
> 
> 1.  **Key Length Lower Bound ($|\mathcal{K}| \ge |\mathcal{M}|$):**
>     The secret key must be at least as long as the message being encrypted:
>     $$
>     \ell_k \ge \ell_m \quad \implies \quad |\mathcal{K}| \ge |\mathcal{M}|
>     $$
>     If $|\mathcal{K}| < |\mathcal{M}|$, the pigeonhole principle implies that for any ciphertext $c$, there exists at least one plaintext $m \in \mathcal{M}$ for which no key satisfies $\mathrm{Enc}(k, m) = c$, violating $\Pr[M = m \mid C = c] = \Pr[M = m]$.
> 
> 2.  **Strict One-Time Use:**
>     A key must never be reused. If the same key $k$ is used to encrypt two distinct messages $m_1$ and $m_2$:
>     $$
>     c_1 \oplus c_2 = (k \oplus m_1) \oplus (k \oplus m_2) = m_1 \oplus m_2
>     $$
>     This reveals the bitwise XOR difference of the two plaintexts, causing a total compromise of unconditional security.


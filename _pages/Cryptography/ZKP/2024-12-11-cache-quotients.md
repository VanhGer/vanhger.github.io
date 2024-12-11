---
date: 2024-12-11
tags:
  - zkp
  - lookup
thumbnail: /assets/img/thumbnail/post_31.png
title: Cache Quotients Lookup Argument
postnum: "31"
---
# Cache Quotients Lookup Argument

## Lookup Argument

### Definition

We have two lists of items: $f_1, f_2,\dots, f_n$ and $t_1, t_2,\dots, t_N$.  A lookup argument is an interactive proof protocol that proves each $f_i$ is equal to at least one $t_j$. We call the values $f_i$ the *sought* values, and $t_i$ the *table* or *index* values.

We use lookup arguments to improve the speed of the proving system in cases such as verifying that an element is within a specific range (range check) or ensuring that the result of an equation is deterministic, among other scenarios.

### Back to lookups

Suppose that $f_i$ and $t_i$ are all elements of some large finite field $\mathbb{F}_q$, and we perform algebra over this finite field. If you need to work with types other than field elements (such as pair or string), you can hash them into a field element and do the lookup.

We see that:
- $t_j$ are public and known to every verifier.
- $f_i$ are secret, only the prover knows them, and they must remain zero-knowledge.
- The table $t_1,\dots, t_N$ might be very long ($N$ is very big), and fixed.
- There may be relatively few $f_i$ values (with a small $n$).

## Cache Quotients

Cache quotients (CQ) lookup arguments rely on the following algebraic property:  
The lookup condition is satisfied if and only if there exist $m_j$ such that:
$$\sum_{i=1}^n \dfrac{1}{X + f_i} = \sum_{j=1}^N \dfrac{m_j}{X + t_j}$$
In practice, we often choose $m_j$ to represent the number of times $t_j$ appears among the $f_i$ values.

### Polynomial Commitment for Cq

First, Cq uses the KZG commitment scheme. For those unfamiliar with it, please refer to this [document](https://docs.sotazk.org/terms/polynomial-commitment/100_kate_commitment/?h=kat).

Cq enables the prover to prove the lookup claim to the verifier with the following properties:
- The proof will consist of $O(1)$ group elements.
- Both the verifier and prover run the precomputation process with $O(NlogN)$ runtime, where runtime refers to the time required for curve operations.
- The proving process takes $O(nlogn)$ runtime.
- The verifying process takes $O(1)$ runtime (about 4 pairings), which can be reduced to 1 pairing using a batch commitment technique.

We define three polynomials $F, M, T$ are polynomials such that:
- $F(X) \in \mathbb{F}_n[X]$ and $F(\omega^i)  = f_i$ where $\omega$ is the $n$-th root of unity.
- $M(X), T(X) \in \mathbb{F}_N[X]$ and $M(\zeta_j) = m_j, T(\zeta_j) = t_j$ where $\zeta$ is the $N$-th root of unity.

**Basic Flow of CQ**:

1. The prover sends commitments to $F(X)$ and $M(X)$. The verifier then sends back a random challenge $\beta$.
2. Prover denotes and commits 2 polynomials:
	- $B(X)$ s.t $B(\omega^i) = \dfrac{1}{\beta + f_i}$.
	- $A(X)$ s.t $A(\zeta^j) = \dfrac{m_j}{\beta + t_j}$
3. Prover sends the claimed value $s$ such that: $s = \sum_{i=1}^n \dfrac{1}{X + f_i} = \sum_{j=1}^N \dfrac{m_j}{X + t_j}$
4. Prover proves that $A, B$ are well-formed by proving:
	1. $B(\omega^i)(\beta + F(\omega^i)) = 1$ for $i \in [n]$
	2. $A(\zeta^j)(\beta + T(\zeta^j)) = M(\zeta^j)$ for $j \in [N]$
5. $P$ proves that the value $s$ equals to the sums shown above.

To handle the step 4, we use the **quotients** tricks, which will be explained in the next section. For step 5, we leverage the **Aurora** lemma:
<br>

> With $\omega$ is the $n$-th root of unity.
> If $B$ is a polynomial of degree less than $n$, then $\sum_{i=0}^nB(\omega^i) = nB(0)$

The prover opens the commitment of $B(X)$ at $0$, and showing that $nB(0) = s$ (similar to $A$).
<br>

## Protocol

We define $F_i = F(\omega^i)$ and $L_i(X)$ as the Lagrange basis polynomials. For more information about Lagrange interpolation, refer to [this](https://docs.sotazk.org/terms/lagrange_interpolation/?h=lagra). 

**Precompute**:

1. Generate $SRS$ for the KZG commitment.
2. Compute $[F(x)]_1$ (the commitment of $F(X)$ in $\mathbb{G}_1$).  ($O(n)$)
3. Compute $[Z_N(x)]_2$ (the commitment of the vanishing polynomial of the $N$-sized domain in $\mathbb{G}_2$).  ($O(1)$)
4. Compute $T(X)$ and $[T(x)]_2$. $(O(NlogN))$
5. For $i \in [N]$, compute:   $O(NlogN)$
	a. $q_i = [Q_i(x)]_1$ such that $$L_i(X)\cdot T(X) = T_i\cdot L_i(X) + Z_N(X) \cdot Q_i(X)$$ 
	b. $[L_i(x)]_1$
	c. $[\dfrac{L_i(x) - L_i(0)}{x}]_1$

This precomputation step takes $O(N\log N)$. The algorithm for this will be described later in the document.

**Proving & Verifying** (3 rounds)

**Round1**:
1. Prover computes $[M(x)]_1$, sends $[m(x)]_1$ tp the verifier. $O(n)$

**Round2**:
1. Verifier sends a random challenge $\beta$
2. Prover defines $A_i = \dfrac{m_i}{\beta + t_i} \forall i \in [N]$ and sends $[A(x)]_1$. $O(n)$
3. Prover sends $[Q_A(x)]_1$ where$$A(X)(\beta + T(X)) = M(X) + Q_A(X)Z_N(X)$$ in $O(n)$.
4. Define $B_i = \dfrac{1}{\beta + f_i}$ and $B(X)$. ($O(n)$)

We know in advance that we will open $B(X)$ at $0$ (due to **Aurora** lemma above), so we compute the quotient polynomial and commit it directly:

5. Prover computes $B_0(X) = \dfrac{B(X) - B(0)}{X}$ and sends $[B_0(x)]_1$. ($O(n)$)
6. Prover sends $[Q_B(x)]_1$ where $$B(X)(\beta + F(X)) = 1 + Q_B(X)Z_n(X)$$ in $O(nlogn)$
7. Prover sends $[P(x)]_1$ where $P(X) = B_0(X) · X^{N −1−(n−2)}$. $O(n)$.
8. Verifier uses [pairing](https://docs.sotazk.org/terms/pairings_or_bilinear_map/?h=pairing) to check:
	a. $A(x)$ is well-formed: $$e([A(x)]_1,[T(x)]_2) = e([Q_A(x)]_1, [Z_N(x)]_2) · e([M(x)]_1 − β · [A(x)]_1, [1]_2)$$
	b. $B_0(X)$ has the correct degree: $$e([B_0(x)]_1, [x^{N-1-(n-2)}]_2) = e([P(x)]_1, [1]_2)$$

To check that $B(X)$ is well-formed, verifier opens it at a random challenge $\gamma$. 
**Round 3**:
1. Verifier sends $\gamma$.
2. Prover sends $b_{0, \gamma} = B_0(\gamma), f_\gamma = F(\gamma)$. ($O(n)$)
3. Prover sends $a_0 = A(0)$. ($O(n)$, using the Aurora lemma)
4. Verifier sets $b_0 = (N\cdot a_0)/ n$ and computes $Z_n(\gamma) = \gamma^n - 1$, $b_\gamma = b_{0, \gamma}\cdot \gamma + b_0$ and $Q_{b, \gamma} = \dfrac{b_\gamma \cdot (f_\gamma + \beta) - 1}{Z_n(\gamma)}$ 
 5. Verifier sends a random $\eta$ for batched KZG check. Both parties compute: $v =  b_{0,γ} + η · f_γ + η^2 · Q_{b,γ}$
 6. Prover commits to $[h(x)]_1$ where $h(X) = \dfrac{B_0(X) + η · F (X) + η^2 · Q_B(X) − v}{X − γ}$ ($O(n)$)
7. Verifier computes $c = [B_0(x)]_1 + η · [f(x)]_1 + η^2 · [Q_b(x)]_1$ and verifies the opening of $B(x)$ in $\gamma$:  $e(c − [v]_1 + γ · [h(x)]_1 , [1]_2) = e([h(x)]_1 , [x]_2)$.
8. To perform a KZG check for $a_0$:
	a. Prover opens $A(X)$ at $0$ by sending $[A_0(x)]_1$ where $A_0(X) = \dfrac{A(X) - a_0}{X}$.  $(O(n))$
	b. Verifier checks $e([A(x)]_1 − [a_0]_1 , [1]_2) = e([A_0(x)]_1, [x]_2)$


## Algorithm

Below is a detailed explanation of how the precomputation steps can be executed efficiently, along with the corresponding runtime analysis:

<br>
### Precompute:

The SRS setup takes $O(N)$, where $N$ is the size of the SRS. Prover computes $F(X)$ in $O(nlogn)$, using [FFT](https://docs.sotazk.org/terms/fast_fourier_transforms/?h=fft#ffts-and-binary-fields).

Both parties compute $Z_N(X) = X^N - 1$ and commit it in $\mathbb{G_2}$ in $O(1)$. At step 4, they compute $T(X)$ in $O(NlogN)$ using FFT and commit it in $\mathbb{G}_2$. 

At step $5a$, we see that:
$$L_i(X) = \dfrac{\prod_{j \in [N], j != i} X - \zeta^j}{\prod \zeta^i - \zeta^j} = \dfrac{\prod X - \zeta^j}{\prod(\zeta^i - \zeta^j) (X - \zeta^i)} = \dfrac{Z_N(X)}{Z'_N(\zeta^i)(X - \zeta^i)}$$

Substituting $L_i(X)$ into the equation $5a$:

$L_i(X)\cdot T(X) = T_i\cdot L_i(X) + Z_N(X) \cdot Q_i(X)$

$\iff Q_i(X) = \dfrac{T(X) - T_i}{Z'_N(\zeta^i)(X - \zeta^i)} = Z'_N(\zeta^i)^{-1}K_i(X)$ where $K_i(X) := \dfrac{T(X) - T_i}{X - \zeta^i}$

$\iff$ $[Q_i(X)]_1 = Z'_N(\zeta^i)^{-1} \cdot [K_i(X)]_1$

Here, $[K_i(X)]_1$ represents the openings of $T(X)$over the $N$-point group. This can be computed in $O(NlogN)$ using [Feist and Khovratovich algorithm](https://eprint.iacr.org/2023/033.pdf). Details of the algorithm will be included in the **Appendix** section of this post.

Next, we have: $Z'_N(X)^{-1} = (NX^{N-1})^{-1} = X / N \mod Z_N(X)$
$\Rightarrow [Q_i(X)]_1 = [K_i(X)]_1 * (\zeta_i/N)$.
So computing $[Q_i(X)]_1 \forall i \in [N]$ requires $O(NlogN)$ curve operations.

At step $5b$ and $5c$, the commitments for Lagrange basis polynomials $[L_i(x)]_1$ can be computed efficiently using the algorithm in Section 3.3 of [BGG17](https://eprint.iacr.org/2017/602.pdf). Details of this algorithm will also be provided in the Appendix.
<br>

### Round1
To compute $[M(x)]_1$, we observe that $M(X) = \sum M_i L_i(X) \iff [M(x)]_1 = \sum M_i \cdot [L_i(X)]_1$.
Since there are at most $n$ terms of $M_i$ that are non-zero, we can compute the left-hand side of the equation above in $O(n)$.
<br>
### Round2
Similar to Round 1, at step 2, we have $[A(x)]_1 = \sum (\frac{M_i}{T_i + \beta})\cdot [L_i(X)]_1$, and we can compute this in $O(n)$.

At step 3, we need to transform the equation. Starting with $A(X) = \sum_jA_j\cdot L_j(X)$ and substituting into the left-hand side of the equation at step 3, we get:

$A(X)(\beta + T(X)) = \sum A_j\cdot L_j(X)(\beta + T(X)) = \sum A_j\cdot L_j(X)T(X) + A_jL_j(X)\beta$
$= \sum A_jT_jL_j(X) + Z_N(X)\cdot Q_j(X) + A_jL_j(X)\beta$
$= \sum A_j\cdot T_jL_j(X) + Z_N(X)\sum A_jQ_j(X) + \sum A_jL_j(X)\beta$
$=\sum \dfrac{M_j}{T_j + \beta}(T_j + \beta)L_j(X) + Z_N(X)\sum A_jQ_j(X)$
$=\sum M_j L_j(X) + Z_N(X)\sum A_jQ_j(X)$

Since $M(X) = \sum M_i L_i(X)$, it follows that 
$$Q_A(X)  = \sum A_jQ_j(X) \iff [Q_A(x)]_1 = \sum A_j \cdot [Q_j(x)]_1$$. 
Thus, we can compute this in $O(n)$.

At step 6, we transform:
$$B(X)(\beta + F(X)) = 1 + Q_B(X)Z_n(X) \iff Q_B(X) = \dfrac{B(X)(\beta + F(X)) - 1}{Z_n(X)}$$.
We use FFT to compute: $B(X)(\beta + F(X))$ in $O(nlogn)$ (see the algorithm [here](https://www.geeksforgeeks.org/fast-fourier-transformation-poynomial-multiplication/)), allowing us to compute $[Q_B(x)]_1$ in $O(nlogn)$.

At step $8$, we compute $[P(x)]_1$ by shifting the SRS by $N-1-(n-2)$ and committing in $O(n)$.

<br>
### Round 3

At step 2, the prover evaluates these values in $n$-degree polynomials, so it takes $O(n)$. At step 3, instead of computing $a_0$ directly (which might take $O(N)$ for evaluation), the prover computes $b_0$ in $O(n)$, and uses **Aurora** lemma to compute $b_0$ in $O(1)$.

At step 6, the prover computes $h(x)$ in $O(n)$ because $F(X), B_0(X), Q_B(X)$ are $n$-degree polynomials. Finally, at step $8a$, the prover computes $[A_0(x)]_1$ as follows:

$A_0(X) = \dfrac{A(X) - a_0}{X} \iff A_0(X) = \sum A_i \dfrac{L_i(x) - L_i(0)}{x}$
$\Rightarrow [A_0(x)]]_1 = \sum A_i \cdot [\dfrac{L_i(x) - L_i(0)}{x}]_1$
Since there are at most $n$ terms $A_i != 0$, we can compute $[A_0(x)]_1$ in $O(n)$.

## Batch KZG verify

In the protocol session above, the verifier checks the lookup conditions by performing 4 pairings:
1. $e([A(x)]_1,[T(x)]_2) = e([Q_A(x)]_1, [Z_N(x)]_2) · e([M(x)]_1 − β · [A(x)]_1, [1]_2)$
2. $e([B_0(x)]_1, [x^{N-1-(n-2)}]_2) = e([P(x)]_1, [1]_2)$
3. $e(c − [v]_1 + γ · [h(x)]_1 , [1]_2) = e([h(x)]_1 , [x]_2)$
4. $e([A(x)]_1  − [a_0]_1 , [1]_2) = e([A_0(x)]_1, [x]_2)$

The verifier can combine all these checks into a single pairing using a **random linear combination (RLC)**.

**Understanding RLC in Pairings**
Suppose we have a random number $\mu$ and the following 3 pairings:
1.  $e([a]_1, [1]_2) = e([b]_1, [x]_2)$
2.  $e([c]_1, [1]_2) = e([d]_1, [x]_2)$
3.  $e([m]_1, [1]_2) = e([n]_1, [y]_2)$

We can combine these as follows:

1. Combine pairings $1$ and $2$:  $e([a]_1 + \mu [c]_1, [1]_2) = e([b]_1 + \mu [d]_1, [x]_2)$
2. Combine the result with $(3)$: $e([a]_1 + \mu[c]_1 + \mu^2[m]_1, [1]_2) = e([b]_1 + \mu[d]_1, [x]_2) \cdot e(\mu^2 [n]_1, [y]_2)$

**Application to Our 4 Pairings**

We apply RLC to the 4 pairings as follows:
1. Combine (3) and (4) to form pairing (5).
2. Combine pairing (5) with (2) to form pairing (6).
3. Combine pairing (6) with (1) to produce the final single pairing.

Before combining, note that pairing (1) is transformed as follows:
$e([A(x)]_1,[T(x)]_2) \cdot e(-[Q_A(x)]_1, [Z_N(x)]_2)= e([M(x)]_1 − β · [A(x)]_1, [1]_2)$

For further details, refer to the [code]().

## Appendix

### Feist and Khovratovich algorithm

Let $f_i$ represent the coefficients of the polynomial $f(X)$ of degree $d$, and let $\lbrace\xi_i\rbrace_{1 \le i \le N}$ be the opening points. The KZG proofs for evaluating $f$ at $\lbrace\xi_i\rbrace$ can be obtained:

- In $O((N+d)log(N+d))$ group operations if $\lbrace\xi_i\rbrace$ are $N$-th roots of unity.
- In $O(Nlog^2n + dlogd)$ group operations in other case.

In the case of CQ, the $\lbrace\xi_i\rbrace$ are the $N$-th roots of unity.

Let $f(\xi_i) = z_i$ and assume the trusted setup SRS is $\lbrace[s^i]\rbrace$. The KZG proofs for $z_i$ are obtained by evaluating 
$h(X) \in \mathbb{G}^{d-1}[X]$ at $\lbrace\xi_1, \xi_2,\dots \rbrace$, where $h(X) = h_1 + h_2X+\dots + h_dX^{d-1}$ 
and: 
$h_i = (f_d[s^{d-i}] + f_{d-1}[s^{d-i-1}] + \dots + f_{i+1}[s] + f_i)$

**Proof**:

The opening at $k$ is:
$\pi [f(\xi_k) = z_k] = [\dfrac{f(X) - z_k}{x - \xi_k}]$.
Expanding, we have:
$$\pi_k =[\dfrac{f_dX^d + f_{d-1}X^{d-1}+\dots f_1X + f_0 - zk}{X - \xi_k}]$$
$$\iff \pi = [\dfrac{f_dX^{d-1}(X - \xi_k) + (f_{d-1} + f_d\xi_k)X^{d-2}(X - \xi_k)+\dots}{X - \xi_k}]$$
$$\iff \pi_k = f_d[s^{d-1}] + (f_{d-1} + \xi_k)f_{d-2}[s^{d-2}] + \dots + (f_1 + \xi_kf_2+\dots+\xi_k^{d-1}f_d)$$
(we ignore $f_0$, ​as it cancels out when computing the fraction).

Regrouping the terms, we have:

<figure class="post-image" style="text-align: center;">
    <img itemprop="image" src="/assets/img/post_img/post31/regroup.png" />
</figure>

Next, we denote $h_i = (f_d[s^{d-i}] + f_{d-1}[s^{d-i-1}]+ \dots + f_{i+1}[s] + f_i)$
Thus, $\pi_k = h_1 + h_2\xi_k + \dots + h_d\xi_k^{d-1}$ , which is equivalent to evaluating $h(X)$ at $\xi_k$.
<br>
**Compute $h_i$**

The $h_i$values can be computed efficiently from $f_i$.

<figure class="post-image" style="text-align: center;">
    <img itemprop="image" src="/assets/img/post_img/post31/h_matrix.png" />
</figure>

The matrix $F$ is the Toeplitz matrix. The computation costs $O(dlogd)$ operations:
- compute $\mathbf{y} = FFT_{2d}(\hat s)$ where  $\hat s = ([s^{d-1}],\dots, [s], 1, [0],\dots, [0])$
- compute $\mathbf{v} = FFT_{2d}(\hat c)$ where $\hat c = (0, \dots, 0, f_1, \dots, f_d)$
- compute $\mathbf{u} = \mathbf{y} \circ \mathbf{v} \circ (1, \nu, \dots, \nu^{2d-1})$ where $\nu$ is 2d-root of unity.
- $\hat h = iFFT_{2d}(\mathbf{u})$,
- Takes first $d$ elements of $\hat h$ as $h$

<br>
### Fast computation of Lagrange basis commitments

We denote the structured reference string $SRS$ in the form: $\lbrace \tau \cdot g_1, \tau^2 \cdot g_1, \dots \rbrace$
We claim that: $$L_i(X) := c_N \cdot \sum_{j=0}^{N-1}(X/\zeta^i)^j$$ 
 where $c_N = 1/N$ and $\zeta$ is the $N$-th root of unity.

**Proof**:
 - Substituting $X = \zeta^{i'}$, we have a sum over all roots of unity order $N$, which equals to $0$
- Substituting $X = \zeta^i$, we have a sum of $N$ divided by $N$ and equal to 1.

Define the polynomial: $$P(Y) := \sum_{j=0}^{N-1}(\tau \cdot Y)^j$$.
Using this, we observe that:
$L_i(\tau) = \dfrac{1}{N}P(\zeta^{-i}) = \dfrac{1}{N}P(\zeta^{N-i})$

The commitment of $L_i(X)$ is: 
$$[L_i(X)]_1 = L_i(\tau)\cdot g_1 = \frac{1}{N}P(\zeta^{-i})\cdot g_1 = \sum_{j=0}^{N-1}\tau^j\cdot g_1 \cdot (\zeta^{-i})^j$$
$= \sum_{j=0}^{N-1}[srs_j]\cdot (\zeta^{-i})^j$

Instead of using $\zeta^{-i}$ we can compute with $\zeta^i$ then reordering the result (reverse all the results, instead of the first one):

$\sum_{j=0}^{N-1}[srs_j]\cdot (\zeta^i)^j = [srs_0]\cdot (\zeta^i)^0 + [srs_1] \cdot (\zeta^i)^1 + \dots$   

Using the coefficients $[srs_i]$, we can compute $N$ evaluations of this polynomial in $O(NlogN)$ curve operations via FFT. Thus, we can compute $[L_i(X)]_1 \forall i \in [N]$ efficiently in $O(NlogN)$.

At step $5b$ in round 2, we need to compute:
$[\dfrac{L_i(x) - L_i(0)}{x}]_1$. 

Using the expression for $L_i(x)$, we transform it as follows:
$[\dfrac{L_i(x) - L_i(0)}{x}]_1 = \zeta^{-i}\cdot[L_i(x)]_1 - (1/N)\cdot[x^{N-1}]_1$. 
The computation cost is $O(N)$ as it involves linear operations on precomputed values.

## Implementation

My implementation is [here]().
## Reference:
- [CQ lookup argument](https://eprint.iacr.org/2022/1763.pdf).
- [Feist and Khovratovich Algorithm](https://eprint.iacr.org/2017/602.pdf).
- [Session 3.3 of BGG17](https://eprint.iacr.org/2017/602.pdf).

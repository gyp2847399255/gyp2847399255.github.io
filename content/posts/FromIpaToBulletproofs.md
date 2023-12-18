---
title: "From IPA (inner product argument) to Bulletproofs"
date: 2023-12-18T20:09:09+08:00
draft: false
math: true
---
# Inner product argument (IPA)

## Construction of IPA

Public parameter: $\mathbf{g}, \mathbf{h} \in \mathbb{G}^n$

Prover input: $\mathbf{a}, \mathbf{b} \in \mathbb{F}_p^n$

Verifier input: $C_a = \mathbf{g}^{\mathbf{a}}$, $C_b = \mathbf{h} ^ {\mathbf{b}}$, $c \in \mathbb{F}_p$

To prove $\langle \mathbf{a}, \mathbf{b} \rangle = c$, the main idea is using a random challenge to fold the vector of length $n$ into a vector of length $n/2$, and recursively make inner product argument for vectors of length $n/2$. Until the length is $1$, the prover can directly send the single element to verifier.

The vector $\mathbf{a}$ can be divided into $\mathbf{a}_L || \mathbf{a}_R$, where both $\mathbf{a}_L$ and $\mathbf{a}_R$ are of length $n / 2$. So can $\mathbf{b}$ be divided into $\mathbf{b}_L || \mathbf{b}_R$.

When combining short vectors with a random challenge $x \in \mathbb{F}$ such that
$$
\begin{aligned}
    \mathbf{a'} &= \mathbf{a}_L + x \cdot \mathbf{a}_R \\\\
    \mathbf{b'} &= \mathbf{b}_L + x^{-1} \cdot \mathbf{b}_R
\end{aligned}
$$
we can find $\langle \mathbf{a'}, \mathbf{b'} \rangle = \langle \mathbf{a}_L, \mathbf{b}_L  \rangle + \langle \mathbf{a}_R, \mathbf{b}_R  \rangle + x^{-1} \cdot \langle \mathbf{a}_L, \mathbf{b}_R  \rangle + x \cdot \langle \mathbf{a}_R, \mathbf{b}_L  \rangle = \langle \mathbf{a}, \mathbf{b} \rangle + x^{-1} \cdot \langle \mathbf{a}_L, \mathbf{b}_R  \rangle + x \cdot \langle \mathbf{a}_R, \mathbf{b}_L  \rangle$.

Prover can send $\langle \mathbf{a}_L, \mathbf{b}_R  \rangle$, $\langle \mathbf{a}_R, \mathbf{b}_L  \rangle$ before receiving the challenge. Now, there is a problem to get commitment of $\mathbf{g'}^{\mathbf{a'}}$, $\mathbf{h'}^{\mathbf{b'}}$.

Since verifer has $\mathbf{g}_L^{\mathbf{a}_L} \cdot \mathbf{g}_R ^ {\mathbf{a}_R}$ at the beginning, prover can send $\mathbf{g}_L^{\mathbf{a}_R}$ and $\mathbf{g}_R^{\mathbf{a}_L}$, so that verifier can construct 
$$
\begin{align*}
\mathbf{g'}^{\mathbf{a'}} &= \mathbf{g'}^{\mathbf{a}_L + x \cdot \mathbf{a}_R} \\ 
&= \mathbf{g}_L ^{\mathbf{a}_L + x \cdot \mathbf{a}_R} \cdot (\mathbf{g}_R^{x^{-1}})^{\mathbf{a}_L + x \cdot \mathbf{a}_R} \\
&= (\mathbf{g}_L^{\mathbf{a}_R})^x (\mathbf{g}_L^{\mathbf{a}_L} \cdot \mathbf{g}_R ^ {\mathbf{a}_R}) (\mathbf{g}_R^{\mathbf{a}_L})^{x^{-1}}
\end{align*}
$$
with random challenge $x$. The construction of $\mathbf{h'}^{\mathbf{b'}}$ is almost the same.

Here we show the construction of $\mathbf{g'}^{\mathbf{a'}}$ is natural. Given vector $\mathbf{v} = (\mathbf{g}_L^{\mathbf{a}_L}, \mathbf{g}_L^{\mathbf{a}_R}, \mathbf{g}_R^{\mathbf{a}_L}, \mathbf{g}_R^{\mathbf{a}_R})$, the original $C_a$ can be regarded as inner product $(1, 0, 0, 1)$ with $\mathbf{v}$, details neglected. Our objectivity is constructing inner product $\mu (1, x, 0, 0) + \lambda (0, 0, 1, x)$ with $\mathbf{v}$, so choosing $\mathbf{g}_L^{\mathbf{a}_R}$, which is $(0, 1, 0, 0)$ and $\mathbf{g}_R^{\mathbf{a}_L}$, which is $(0, 0, 1, 0)$ is a really natural thought.
$$
\left(
\begin{matrix}
    1 & 0 & 0 & 1 \\\\
    0 & 1 & 0 & 0 \\\\
    0 & 0 & 1 & 0
\end{matrix}
\right)
\cdot
\left(
\begin{matrix}
    1 \\\\
    x \\\\
    x^{-1}
\end{matrix}
\right) =
\left(
\begin{matrix}
    1 \\\\
    x \\\\
    x^{-1} \\\\
    1
\end{matrix}
\right)
$$

## Realistic IPA

In practice, the commited vector also has a random mask, and the inner product result may also be a secret. That is, Given commitments $C_a = u^{\rho_a} \mathbf{g}^{\mathbf{a}}$, $C_b = u^{\rho_b} \mathbf{h}^{\mathbf{b}}$, $C_0 = u^{\rho_0} g^{c}$, verifer need to be convinced that $\langle a, b \rangle = c$.

Prover can only change to send Pedersen commitments of $\langle \mathbf{a}_L, \mathbf{b}_R \rangle$, $\langle \mathbf{a}_R, \mathbf{b}_L \rangle$, instead of send them directly.

We also remark a trivial construction of IPA of a committed vector with a public vector can be derived, since verifier can construct commitment from public values on his own.

## Add zero-knowledge

Previous construction doesn't hold zero knowledge property. For example, an adversary knows the committed vector $C_a$ is either $\mathbf{a}_1$ or $\mathbf{a}_2$. He can simulate the folding procedure of two vectors with challenges each round, and checking the final folded result to discern $\mathbf{a}_1$ or $\mathbf{a}_2$.

We directly discuss the hardest problem: prove inner product result of two commited vectors $\mathbf{a}$, $\mathbf{b}$ equals a committed value $c_0$. To obtain a zero knowledge IPA, prover can use random vectors to mask secret vectors.

Before the first round, prover randomly choose $\mathbf{s}, \mathbf{t} \in \mathbb{F}_p^n, \rho_s, \rho_t \in \mathbb{F}_p$ and commit $C_s = Com(\mathbf{s}; \rho_s) = u^{\rho_s}\mathbf{g}^{\mathbf{s}}$, $C_t = Com(\mathbf{t}; \rho_t) = u^{\rho_t}\mathbf{h}^{\mathbf{t}}$ to verifier.

Define $l(X) = \mathbf{a} + \mathbf{s} \cdot X$，$r(X) = \mathbf{b} + \mathbf{t} \cdot X$. Define $\langle l(X), r(X) \rangle = \langle \mathbf{a}, \mathbf{b} \rangle + (\langle \mathbf{s}, \mathbf{b} \rangle + \langle \mathbf{a}, \mathbf{t} \rangle) X + \langle \mathbf{s}, \mathbf{t} \rangle X^2 = c_0 + c_1 X + c_2 X^2$. For random $x$, prover can commit $c_1, c_2$ and prove $\langle l(x), r(x) \rangle = c_0 + c_1 x + c_2 x^2$.

More precisely, besides commitment $C_s$ and $C_t$, prover also commit $C_1 = Com(c_1; \rho_1) = u^{\rho_1} g^{c_1}$ and $C_2 = Com(c_2; \rho_2) = u^{\rho_2} g^{c_2}$. After that, verifier sends random challenge $x$ and prover proves $\langle \mathbf{a} + x\mathbf{s}, \mathbf{b} + x\mathbf{t}\rangle = c_0 + c_1 x + c_2 x^2$ through IPA scheme. Due to homomorphism of Pedersen commitment, previous commitments ensure verifier can compute commitments of random linear combined vectors and inner product result.

## Proof size

Each round, prover send pedersen commitment of $\langle \mathbf{a}_L, \mathbf{b}_R \rangle$, $\langle \mathbf{a}_R, \mathbf{b}_L \rangle$, and $\mathbf{g}_L^{\mathbf{a}_R}$, $\mathbf{g}_R^{\mathbf{a}_L}$, $\mathbf{h}_L^{\mathbf{b}_R}$, $\mathbf{h}_R^{\mathbf{b}_L}$. So the proof size is $6\log n + O(1)$.

Prover complexity is obvious $O(n)$ and verifier complexity is also $O(n)$, since verifier have to compute $\mathbf{g'}$ and $\mathbf{h'}$ each round, which incurs linear complexity.

# Range proof 

Zero knowledge IPA proposed above can be used to construct range proof, which plays an important role in confidential transactions (CT).

Take an example in Bitcoin. Alice wants to transfer 5 bucks to Bob, and she holds a UXTO, whose amout of money is committed use Pedersen commitment, denoted as $C = u^{\rho}g^m$. Subtract 5 bucks, her new UTXO's commitment should be $C' = C / g^{5} = u^{\rho}g^{m - 5}$. However, if $m < 5$, the transaction should not be allowed, but Alice don't want to leak her UTXO's money amount. Proving money amout represented by $C'$ belongs to $[0, N]$ is range proof. Notice that $N$ should smaller half of $|\mathbb{F}_p|$.

To prove $u^{\rho} g^m$ represents a $m$ falls in $[0, 2^n - 1]$, prover first decomposes $m$ into a bit vector $\mathbf{a} \in \mathbb{F}_p^n$ such that $\langle \mathbf{a}, \mathbf{2}^n \rangle = m$, $\mathbf{a} \circ (\mathbf{a} - \mathbf{1}^n) = \mathbf{0}^n$, where $\mathbf{k}^n = (1, k, k^2, \cdots, k^{n-1})$.

The hadamard product $\mathbf{a} \circ (\mathbf{a} - \mathbf{1}^n) = \mathbf{0}^n$ can be transformed into $\langle \mathbf{a} \circ (\mathbf{a} - \mathbf{1}^n), \mathbf{y}^n \rangle = \langle \mathbf{a}, (\mathbf{a} - \mathbf{1}^n) \circ \mathbf{y}^n \rangle = 0$.

Given the commitment of $\mathbf{g}^{\mathbf{a}}$, the commitment of $(\mathbf{a}) \circ \mathbf{y}^n$ can be derived from setting $\mathbf{h} = \mathbf{g}^{(\mathbf{1/y})^n}$. Then 
$$
\begin{align*}
Com(\mathbf{a} \circ \mathbf{y}^n) &= \mathbf{h}^{\mathbf{a} \circ \mathbf{y}^n} \\\\
    &= \mathbf{g}^{(\mathbf{1/y})^n \circ \mathbf{a} \circ \mathbf{y}^n} \\\\
    &= \mathbf{g}^{\mathbf{a}} = Com(\mathbf{a})
\end{align*}
$$

After sending commitment $C_a = u^{\rho_a}\mathbf{g}^\mathbf{a}$ and receiving random challenges $y, z \in \mathbb{F}_p$, prover use zkIPA to prove $\langle \mathbf{a}, \mathbf{2}^n + z \cdot (\mathbf{a} - \mathbf{1}^n) \rangle = m$.

The total proof size is also $6\log n + O(1)$.

# Bulletproofs

Bulletproofs mainly aims to deal with range proof with a smaller proof size. The main idea is commit $\mathbf{a}$ and $\mathbf{a} - \mathbf{1}^n$ simultaneously.

## Inner product argument

Different from IPA defined previously, Bulletproofs IPA commits two vector together instead of independently in the statement. Specifically, it proves the following relation:
$$
(\mathbf{g}, \mathbf{h} \in \mathbb{G}^n, u, P \in \mathbb{G}; \mathbf{a}, \mathbf{b} \in \mathbb{F}_p^n)
$$
such that $P = \mathbf{g}^{\mathbf{a}} \mathbf{h}^{\mathbf{b}} \cdot u^{\langle \mathbf{a}, \mathbf{b} \rangle}$, where $\mathbf{g}, \mathbf{h}, u, P$ are public instance, $\mathbf{a}, \mathbf{b}$ are witness.


Similar with previous IPA, it folds vectors with random challenge into half in each round. Although previous scheme also applies, we will follow the folding result in Bulletproofs paper, 
$$
\begin{align*}
\mathbf{a'} &= x \mathbf{a}_L + x^{-1} \mathbf{a}_R \\\\
\mathbf{b'} &= x^{-1}  \mathbf{b}_L + x \mathbf{b}_R
\end{align*}
$$
Define $H(\mathbf{v}_1, \mathbf{v}_2, \mathbf{v}_3, \mathbf{v}_4, v) = \mathbf{g}_L^{\mathbf{v}_1} \cdot \mathbf{g}_R^{\mathbf{v}_2} \cdot \mathbf{h}_L^{\mathbf{v}_3} \cdot \mathbf{h}_R^{\mathbf{v}_4} \cdot u^v$
This time, prover firsly commit $L, R$ to verifier, where
$$
\begin{matrix*}
    L = H(& \mathbf{0} & \mathbf{a}_L & \mathbf{b}_R & \mathbf{0} & \langle \mathbf{a}_L, \mathbf{b}_R \rangle &) \\\\
    R = H(& \mathbf{a}_R & \mathbf{0} & \mathbf{0} & \mathbf{b}_L  & \langle \mathbf{a}_R, \mathbf{b}_L \rangle &) \\\\
    P = H(& \mathbf{a}_L & \mathbf{a}_R & \mathbf{b}_L & \mathbf{b}_R  & \langle \mathbf{a}, \mathbf{b} \rangle &)
\end{matrix*}
$$

Verifier can compute 
$$
L^{x^2} \cdot P \cdot R^{x^{-2}} = H(x^{-1} \mathbf{a'}, x \mathbf{a'}, x \mathbf{b'}, x^{-1}\mathbf{b'}, \langle \mathbf{a'}, \mathbf{b'} \rangle) = \left(\mathbf{g}_L^{x^{-1}} \circ \mathbf{g}_R^{x}\right) ^ {\mathbf{a'}} \cdot \left(\mathbf{h}_L^{x} \circ \mathbf{h}_R^{x^{-1}}\right) ^ {\mathbf{b'}} \cdot u^{\langle \mathbf{a'}, \mathbf{b'} \rangle}
$$
Thus, the witness is halved. After $\log n$ rounds, it will be reduced to only $2$ element. Proof size is only 2 group element each round, so total proof size is $2\log n + O(1)$, only 1/3 campared to previous IPA.

A proof of relation below can be constructed utilizing this inner product argument.

$$
(\mathbf{g}, \mathbf{h} \in \mathbb{G}^n, P \in \mathbb{G}, c \in \mathbb{F}_p; \mathbf{a}, \mathbf{b} \in \mathbb{F}_p^n)
$$
such that $P = \mathbf{g}^{\mathbf{a}} \mathbf{h}^{\mathbf{b}}$ and $c = \langle \mathbf{a}, \mathbf{b} \rangle$.


![algorithm](/FromIpaToBulletproofs-1.png)


## Range proof

In Bulletproofs range proof, prover commit bit decomposition of secret value $\mathbf{a}_L$ and $\mathbf{a}_R = \mathbf{a}_L - \mathbf{1}^n$ together as $A = r^{\alpha} \mathbf{g}^{\mathbf{a}_L} \mathbf{h}^{\mathbf{a}_R}$. The secret value is committed as $V = r^{\gamma} g^v$.

We need to prove three equations below
$$
\langle \mathbf{a}_L, \mathbf{2}^n\rangle = v, \qquad \langle \mathbf{a}_L,  \mathbf{a}_R \circ \mathbf{y}^n \rangle = 0, \qquad \mathbf{a}_L = \mathbf{a}_R + \mathbf{1}^n
$$

Only the second equation is easy to prove through Bulletproofs IPA directly, by setting $\mathbf{h'} = \mathbf{h}^{(\mathbf{1/y})^n}$. The first and third isn't quite simple.

However, due to $\langle \mathbf{a}_L,  \mathbf{a}_R \rangle = 0$, the first equation can be transformed into $\langle \mathbf{a}_L,  \mathbf{a}_R + z \cdot  \mathbf{2}^n \rangle = z \cdot v$ for random $z$, which can prove straightforwardly. And the third equation can be transformed into $\langle \mathbf{a}_L - \mathbf{a}_R - \mathbf{1}^n, \mathbf{y}^n \rangle = 0$ for random $y$, and it can be then transformed into 
$$
\begin{align*}
\langle \mathbf{a}_L - \mathbf{1}^n,  \mathbf{y}^n \circ (\mathbf{a}_R + \mathbf{1}^n) \rangle &= \langle \mathbf{a}_L,  \mathbf{y}^n \circ \mathbf{a}_R \rangle + \langle \mathbf{a}_L, \mathbf{y}^n\rangle - \langle \mathbf{a}_R, \mathbf{y}^n\rangle - \langle \mathbf{1}^n, \mathbf{y}^n\rangle \\\\
 &= \langle \mathbf{a}_L, \mathbf{y}^n\rangle - \langle \mathbf{a}_R, \mathbf{y}^n\rangle - \langle \mathbf{1}^n, \mathbf{y}^n\rangle = \langle \mathbf{a}_L - \mathbf{a}_R - \mathbf{1}^n, \mathbf{y}^n \rangle = 0
\end{align*}
$$

Up till now, we show three equations in range proof can be proved independently. As usual, they can be prove together through random linear combination. So the total prove size is only $2\log n + O(1)$.

We can see the bulletproofs now isn't zero-knowledge. This problem can be easily dealt with by utilizating techniques in construction of zero knowlodge IPA. Detailed protocol can be seen in the whole paper.

Prover can aggregate multiple range proofs for $m$ committed values. It's straightforward that we can use a vector of length $m \cdot n$ to binary represent all $m$ values. Proof size of aggregating range proof is $\log (m \cdot n) + O(1)$.


# Remarks

## IPA of two commited vectors

Can we construct an IPA of two commited vectors through Bulletproofs IPA? It can reduce proof size of IPA to only $2\log n + O(1)$. Here's my failure attempt below.

Given $C_a = \mathbf{g}^{\mathbf{a}}, C_b = \mathbf{h}^{\mathbf{b}}, c = \langle \mathbf{a}, \mathbf{b} \rangle$，verifier sends challenge $x$，then using Bulletproofs IPA prove knowledge of $\mathbf{a'}, \mathbf{b'}$ such that $u^{x}, P = C_a C_b^x u^{x^2 \cdot c}$. Completeness holds since $\mathbf{a'} = \mathbf{a}$, $\mathbf{b'} = x\mathbf{b}$ satisfy the relation.

However, soundness doesn't hold. A simple counterexample is prover set $C_a = \mathbf{h}^{\mathbf{b}}$, $C_b = \mathbf{g}^{\mathbf{a}}$ and $c = \langle \mathbf{a}, \mathbf{b} \rangle$. We can find $\mathbf{a'} = x\mathbf{a}$, $\mathbf{b'} = \mathbf{b}$ satisfy the Bulletproofs IPA relation, but prover doesn't possess a knowledge of an vector $\mathbf{v}$ such that $\mathbf{g}^{\mathbf{v}} = C_a$.
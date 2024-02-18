# One-to-Many Multivariate Polynomial Commitment from Fast Reed-Solomon IOPs and Its Application to Asynchronous Verifiable Secret Sharing

## Introduction and Motivation

A polynomial commitment scheme (PCS) enables a prover to commit to a polynomial $f$ defined over a field $\mathbb{F}$ with degree bound $d$ and variable number $\mu$. When given a point $\vec{x} \in \mathbb{F}^{\mu}$, the prover can convince a verifier via an evaluation proof that the committed polynomial $f$ satisfies $f(\vec{x}) = y$ for a public $y \in \mathbb{F}$.

PCSs from **Fast Reed-Solomon IOP of proximity** (FRI) stand out among succinct and efficient ones for their efficient execution and plausible post-quantum security, gaining popularity in zk-rollup, families of post-quantum signatures like Picnic, and zero-knowledge machine learning.

The first PCS from FRI is tailored for univariate polynomials and cannot naturally extend to multivariate without additive homomorphism. Prover complexity of previous state-of-the-art FRI-based multivarirate PCS is $O(\mu \cdot d^\mu \log d)$, surpassing $O(d^\mu)$ FRI-PC (for $\mu = 1$). Therefore, we hope to build an FRI-based multivariate polynomial commitment with a linear prover complexity.

A PCS with allows a prover to open multiple distinct evaluations of a single committed polynomial, where each evaluation corresponds to a unique verifier, with a lower prover complexity than repeating single proof. A natural application of one-to-many proofs is verifiable secret sharing (VSS) with low dealer complexity.

Asynchronous VSS (AVSS) does not depend on any timing assumptions
and is more robust against denial-of-service and performance attacks. Many AVSS schemes from bivariate PCS use PCS directly, leading to $O(n^3)$ dealer complexity for proving $n^2$ point evaluations on an $O(n)$-degree polynomial, each taking $O(n)$ complexity. Here, we hope push the frontier of AVSS with the advancements in one-to-many multivariate polynomial commitment.

## PolyFRIM

## PolyAVSS

## Evaluation

### Software

### Hardware

## Summary
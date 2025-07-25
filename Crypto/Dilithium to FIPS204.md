# Understanding Dilithium: From Concept to FIPS 204


The Dilithium digital signature scheme—now standardized as ML-DSA in [FIPS 204](https://csrc.nist.gov/pubs/fips/204/final)—is one of the most promising post-quantum algorithms. This blog explores its development, how design choices shaped its security and efficiency, and how these were refined into the FIPS standard.

---

##  What is Dilithium?

Dilithium is a **quantum-safe digital signature** algorithm built on **module lattice** problems like Module-LWE and Module-SIS. Its design achieves:

- **Post-quantum security**
- **Compact keys and signatures**
- **Fast performance on various platforms**

It was selected by NIST and is now known formally as **ML-DSA (Module Lattice-based Digital Signature Algorithm)** in FIPS 204.

---

##  The Path to FIPS 204

### Step 1: Inspiration from Schnorr Signatures

Dilithium began with a structure resembling **Schnorr signatures**:

```
commitment (w) → challenge (c) → response (z)
```

But instead of working in a group like Schnorr, Dilithium operates over **module lattices** using polynomial rings. The transformation also introduced domain-specific challenges.

---

### Step 2: Toy Dilithium

The “toy” version of Dilithium followed the Schnorr logic but over the ring $R_q = \mathbb{Z}_q[x]/(x^n + 1)$. The steps were:

1. **Keygen**: Generate matrix $A$, secrets $s_1, s_2$, and compute $t = As_1 + s_2$.
2. **Sign**: Choose a short random $y$, compute $w = Ay$, derive challenge $c = H(M \parallel w)$, and output $z = y + c s_1$.
3. **Verify**: Use public key $A, t$, check whether recomputed commitment $w_1\prime=HighBits(Az-ct)$ matches challenge hash: $c = H(M \parallel w_1\prime)$.
Note:
  - $w_1\prime=Az-ct=A(y+cs_1)-c(As_1+s2)=Ay-cs_2=w-cs_2\ne w$, so $HighBits(w)\stackrel{?}{=}HightBits(Az-ct)=HighBits(w-cs_2)$ is used for verification and $cs_2$ need be "small".
  - In full version, $t$ is compressed ($t=t_1\cdot2^d+t_0$) and only $t_1$ is passed as part of the public key, so $w_1\prime=Az-ct_12^d=A(y+cs_1)-c(t-t_0)=A(y+cs_1)-c(As_1+s_2-t_0)=w-cs_2+ct_0$.
  - To ensure $HighBits(w_1\prime)=HighBits(Az-ct_12^d)=HighBits(w-cs_2+ct_0)$, $𝑤−𝑐𝑠_2$ shall be safely within an interval — not near a boundary — so adding $c𝑡_0\in[-\beta,\beta]$ won't push it over into a new HighBits value.

---

##  Key Problems and Solutions

###  Problem 1: Large Key Sizes

- **Issue**: $A$, $t$, and $s_1, s_2$ are large.
- **Solution**: Derive $A$ from a seed. Compress $t$ using `Power2Round` to store only high-order bits $t_1$, and regenerate $s_1, s_2$ from a secret seed.

```pgsql
Input:  r ∈ [0, q − 1], integer d
Output: (r1, r0) such that r = r1 * 2^d + r0,
        with r0 ∈ (−2^{d−1}, 2^{d−1}]
```
```python
r0 = r mod 2^d
r1 = (r - r0) // 2^d
```
---

###  Problem 2: Repeated Hashing of Message $M$

- **Issue**: Hashing $M$ repeatedly in each signing loop slows performance.
- **Solution**: Hash once to get $\mu = H(M)$, then compute challenges as $c = H(\mu \parallel w_1)$.
  - From the public key compute tag: $tr=𝐻(\rho\parallel 𝑡_1,2\lambda)$
    - $ρ$: seed used to expand matrix 
    - $t_1$: high bits of public value 
  - From the message: $𝜇=𝐻(tr\parallel 𝑀)$
  - Use $μ$ for challenge derivation: $\tilde 𝑐=𝐻(𝜇\parallel 𝑤_1)$

---

###  Problem 3: Too Many Random Bits for Signing

- **Issue**: Requires fresh randomness in each signature attempt.
- **Solution**: Use deterministic ExpandMask from a secret seed $\kappa$ and counter.
  - $(\rho, \rho\prime,K) =H(\xi,1024)$
  - $\rho\prime\prime=H(K\parallel rnd\parallel\mu,512)$
  - $y=ExpandMask(\rho\prime\prime,\kappa)$

---

###  Problem 4: Signature May Leak Info About $s_1$

- **Issue**: $z = y + c s_1$ can leak info if coefficients of $z$ are large.
- **Solution**:
  - Enforce bounds on $\parallel z\parallel_\infty < \gamma_1 - \beta$ and use rejection sampling to discard leaky signatures.
  - we want to compute $w$ but with $Az-ct$ we can only got $Az-ct=A(y+cs_1)-c(As_1+s2)=Ay-cs_2=w-cs_2$. So need check $\parallel r_0\parallel_\infty<\gamma_2-\beta$, where $r_0=LowBits(w-cs_2,2\gamma_2)$

---

###  Problem 5: Compression of $w$

- **Issue**: Verifier doesn’t have access to $w$, only $z, c$, and public key.
- **Solution**:
  - Use $w_1 = \text{HighBits}(w)$ and reconstruct it from compressed values and hints.
  - As what we can get is $Az-ct=A(y+cs_1)-c(As_1+s2)=Ay-cs_2=w-cs_2=w_1+w_0-cs_2$, so $w_0-cs_2$ shall be small. Since $\parallel cs_2 \parallel_\infty\le\tau\eta=\beta$, if $\parallel LowBits(w-cs_2,2\gamma_2)<\gamma_2-\beta$, then we have $HighBits()$

```pgsql
Input: r ∈ [0, q−1], α such that q−1 = m · α
Output: (r1, r0) such that r = r1 · α + r0
        with r0 ∈ (−α/2, α/2]
```
```python
r0 = r mod α
r1 = (r - r0) // α
```
---

###  Problem 6: Compression of $t$

- **Issue**: Only $t_1$ is stored in the public key; verifier lacks $t_0$.
- **Solution**: Compute $Az - ct_1 2^d$ and include “hint bits” to help reconstruct original values.

---

###  Problem 7: Hint Bits

- **Solution**: Use `MakeHint` and `UseHint` functions to encode the effect of the low bits so verifier can recover high bits of $w$ reliably.

---

##  FIPS-204 Final Scheme

Dilithium achieves compactness and security through:

- **HighBits/LowBits decomposition**
- **Signature rejection sampling**
- **Public key and signature compression**
- **Hint vector for accurate reconstruction**

Key security assumptions:

- **D-MLWE**: Learning With Errors over modules
- **MSIS**: Module Short Integer Solution
- Hash function modeled as a random oracle

---

##  Final Parameters (ML-DSA-87)

| Parameter      | Value             |
|----------------|------------------|
| $q$        | $2^{23} - 2^{13} + 1$ |
| $n$        | 256              |
| $(k, \ell)$ | (8,7)           |
| $\eta$     | 2                |
| $\gamma_1$ | $2^{19}$      |
| $d$        | 13               |
| $\beta$    | 120              |
| $\omega$   | 75               |

---

##  Sizes

| Item           | Size             |
|----------------|------------------|
| Public Key     | 2,592 bytes      |
| Private Key    | 4,896 bytes      |
| Signature      | 4,627 bytes      |

---

##  Final Thoughts

Dilithium is a clean, elegant lattice-based scheme that balances post-quantum security with real-world performance. The techniques like rejection sampling, deterministic signing, and efficient compression make it a model for future cryptographic standards.

---

**References**:

- [FIPS 204 – ML-DSA Specification](https://csrc.nist.gov/pubs/fips/204/final)
- [Dilithium at PQ-Crystals](https://pq-crystals.org/dilithium)

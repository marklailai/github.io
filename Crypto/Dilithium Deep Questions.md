# Questions to Think Deep in Dilithium

The Dilithium signature scheme, part of the CRYSTALS suite selected in the NIST post-quantum cryptography standardization process, is rich with elegant cryptographic design choices. While reading the specifications and studying the algorithm, certain technical decisions may prompt deeper questions. Below is a curated set of such questions, followed by intuitive and technical insights, aimed to provoke deeper understanding for practitioners and researchers.

---

## Why is Dilithium called a "Schnorr-like" signature?
Because the high-level structure mimics Schnorr: commit (sample y, compute w), challenge (hash message and commitment), response (z = y + cs1). However, Dilithium uses structured lattices (module-LWE, module-SIS) rather than discrete logs, and adds rounding and rejection sampling for lattice security.

---

## Why is the commitment w = A*y rounded to w1?
Without rounding, the verifier could potentially recover y from w = A*y since A is public. Rounding hides low bits of w to mask y, increasing the difficulty of recovering y while preserving enough correctness to enable verification.

---

## Is solving w = A*y easy if y is small?
If y is small and unprotected, solving for y in w = A*y is indeed easier than solving a general module-LWE problem. This is why rounding and rejection sampling are used to reduce leakage about y.

---

## Is solving t = A*s1 easy if s2 = 0?
Yes, t = A*s1 + s2 becomes a plain LWE instance if s2 = 0. The hardness of LWE requires that the error s2 be nonzero and small. Without s2, recovering s1 becomes much easier.

---

## But s1 is also small — doesn't that make t leak information?
Yes, which is why the public key t = A*s1 + s2 is rounded (t1 = HighBits(t)) before publishing. This rounding hides some information about s1 while allowing verification later. Without it, s1 could potentially be recovered through lattice techniques.

---

## If y and s1 have the same bounds, does solving w = A*y become as easy as solving t = A*s1?
From a mathematical viewpoint, yes — if the bounds are identical and there's no rounding or error term, solving either is equally hard. However, Dilithium designs the bounds and rounding carefully to avoid this equivalence and keep y ephemeral.

---

## Why is the response z = y + c*s1?
This is directly inspired by the Schnorr paradigm. The response encodes both the ephemeral y and the secret s1 influenced by the challenge c. It must be small enough to pass verification and rejection bounds.

---

## Why does key generation use rejection sampling?
Keygen ensures that s1 and s2 (the secret vectors) are within bounds so that later computations — especially signature responses — remain small and rejection rates are manageable. This also ensures that rounding and reconstruction during verification are valid.

---

## Why must c*s2 be small in verification?
During verification, the verifier computes A*z - c*t = A*y - c*s2. For correctness, this result must align with the original w1 (rounded A*y). So, c*s2 must be small enough not to affect the high bits and cause verification failure.

---

## Why does w use different rounding than t?
w (the commitment) uses rounding to hide y completely. t (the public key) uses rounding to hide s1 and s2 partially, but still allow t1 to support signature verification. The rounding strategy is tuned to the role each value plays in security and correctness.

---

## Why does Dilithium compute \mu = H(tr || M) before the loop?
\mu is the message digest bound to the key (tr). It’s used to compute the challenge deterministically. It must be fixed before the loop to ensure deterministic signatures and consistent rejection sampling across retries.

---

## Why not just hash the message directly for \mu?
Including `tr` (a hash of the public key) binds the message to the signer’s identity. This prevents signature substitution attacks across keys — a known issue in plain Schnorr when the public key is not part of the challenge hash.

---

## Why does Schnorr suffer from signature substitution across keys?
If c = H(M || w), then an attacker could reuse a signature under a different public key. Binding the key into the challenge (e.g., c = H(pk || M || w)) prevents this. Dilithium achieves this with \mu = H(tr || M).

---

## What does SampleInBall do?
It deterministically maps a hash digest to a sparse polynomial with \tau non-zero coefficients in \{-1, 1\}. It ensures that the challenge polynomial c is compact, sparse, and reproducible by the verifier, which helps maintain signature size and efficiency.

---

## SampleInBall Pseudocode
```python
def SampleInBall(tildec, n, tau):
    c = [0] * n
    used_indices = set()
    i = 0
    pos = 0
    while i < tau:
        if pos + 3 > len(tildec):
            tildec = SHAKE256(tildec, 64)
            pos = 0
        index = (tildec[pos] | (tildec[pos+1] << 8)) % n
        sign_bit = (tildec[pos+2] >> 0) & 0x01
        pos += 3
        if index in used_indices:
            continue
        used_indices.add(index)
        c[index] = 1 if sign_bit == 0 else -1
        i += 1
    return c
```

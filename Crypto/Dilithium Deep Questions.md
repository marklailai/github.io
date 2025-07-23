
# Questions to Think Deep in Dilithium

The Dilithium signature scheme, part of the CRYSTALS suite selected in the NIST post-quantum cryptography standardization process, is rich with elegant cryptographic design choices. While reading the specifications and studying the algorithm, certain technical decisions may prompt deeper questions. Below is a curated set of such questions, followed by intuitive and technical insights, aimed to provoke deeper understanding for practitioners and researchers.

---

## Why is Dilithium called a "Schnorr-like" signature?
Because the high-level structure mimics Schnorr: 
1. **Commitment**: Sample a short random vector $y$, compute \( w = A \cdot y \).
2. **Challenge**: Compute \( c = H(\mu, w_1) \).
3. **Response**: Reveal \( z = y + c \cdot s_1 \).

It differs from classic Schnorr in key ways: lattice-based operations replace modular exponentiation, and extra mechanisms (rounding, rejection sampling) are needed to preserve security and correctness under module-LWE/SIS assumptions.

---

## Why is the commitment \( w = A \cdot y \) rounded to \( w_1 \)?
The matrix \( A \) is public, so \( w = A \cdot y \) may leak information about \( y \). Rounding \( w \) to \( w_1 \) removes low bits and acts as a hiding function:
- It makes recovering \( y \) from \( w_1 \) hard,
- Yet keeps enough structure to enable correct verification later.

---

## Is solving \( w = A \cdot y \) easy if \( y \) is small?
Yes — smaller \( y \) makes this system more vulnerable, as it reduces the solution space. That’s why Dilithium uses both:
- **Rejection sampling** to ensure \( y \) is uniformly random within a bounded domain,
- **Rounding** to hide the output of \( A \cdot y \).

---

## Is solving \( t = A \cdot s_1 \) easy if \( s_2 = 0 \)?
Yes. If \( s_2 = 0 \), then \( t = A \cdot s_1 \), and with \( s_1 \) being small, this resembles a bounded-solution SIS problem — which is much easier than LWE. The presence of \( s_2 \) (noise term) is crucial to ensure hardness.

---

## Why isn’t \( s_1 \)’s small size still a problem?
It *would* be, if \( t = A \cdot s_1 + s_2 \) were published directly. Instead, \( t \) is rounded using a "high bits" operation before publication:
- This ensures that attackers only get coarse information,
- Prevents them from solving for \( s_1 \) or \( s_2 \) easily.

---

## If \( y \) and \( s_1 \) have the same bound, is solving \( w = A \cdot y \) as hard as \( t = A \cdot s_1 \)?
Mathematically, yes — if both are small and unrounded, solving either is about equally hard. However:
- \( y \) is ephemeral and never published directly,
- \( w \) is rounded to \( w_1 \),
- \( s_1 \) is protected by noise \( s_2 \) and public key rounding.

The scheme exploits these differences to balance security and efficiency.

---

## Why is the response \( z = y + c \cdot s_1 \)?
Inspired by Schnorr: instead of revealing \( y \) directly (which would leak info), we hide it with a multiple of \( s_1 \). The response \( z \) is:
- Verified by checking \( A \cdot z - c \cdot t pprox w_1 \),
- Small due to prior rejection sampling on \( y \) and \( s_1 \),
- Subject to rejection if it violates size bounds.

---

## Why does key generation use rejection sampling?
To ensure:
- \( s_1, s_2 \) are short enough to prevent leaking info through \( t = A \cdot s_1 + s_2 \),
- Later responses \( z = y + c \cdot s_1 \) stay within bounds,
- Verification rounding and bounds-checks work reliably.

Fast key generation variants are under research but must maintain these constraints.

---

## Why must \( c \cdot s_2 \) be small in verification?
Verifier computes:
\[ A \cdot z - c \cdot t = A \cdot y - c \cdot s_2 \]
This must still round to \( w_1 \). If \( c \cdot s_2 \) is too large, it alters the high bits and causes verification to fail. So \( s_2 \) is small and \( c \) is sparse.

---

## Why different rounding for \( t \) and \( w \)?
- \( t \) (public key): uses *high bits* to obscure \( s_1 \),\( s_2 \) but still allow recomputation of \( A \cdot z - c \cdot t \).
- \( w \): uses *centered* rounding to make \( w_1 \) lose more info, better hiding ephemeral \( y \).

The rounding methods reflect the role of each variable.

---

## Why compute \( \mu = H(tr \| M) \) before the signing loop?
\( \mu \) binds the signature to:
- The public key (via \( tr \), a hash of the matrix \( A \) and \( t_1 \)),
- The message \( M \).

This ensures deterministic challenge \( c \), consistent rejection sampling, and prevents attacks across keys.

---

## Why not just use \( H(M) \) for \( \mu \)?
Without \( tr \), an attacker might substitute the signature for a different public key. Including \( tr \) binds \( M \) to the original signer.

---

## Why does Schnorr suffer from signature substitution?
In classic Schnorr, \( c = H(M, w) \) could allow an attacker to:
1. Extract \( (z, c) \) from a signature under key \( pk_1 \),
2. Forge \( (z, c) \) under a new key \( pk_2 \).

Dilithium avoids this by hashing \( tr \| M \) before generating \( c \).

---

## What is `SampleInBall()`?
Generates a challenge polynomial \( c \in R_q \):
- Only \( 	au \) non-zero entries,
- Each non-zero is \( \pm 1 \),
- Deterministically derived from a hash digest.

This ensures compact signatures and reproducible verification.

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


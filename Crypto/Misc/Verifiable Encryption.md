# Verifiable Encryption Explained: Privacy with Proof

Verifiable encryption is a powerful cryptographic primitive that blends **encryption** with **zero-knowledge proofs**. It allows a sender to encrypt a message **and prove** that the encryption is well-formed and contains a specific value — without revealing the message itself.

This blog post explores what verifiable encryption is, how it works, and where it’s used. We'll also walk through an example using the ElGamal cryptosystem and zero-knowledge proofs.

---

## What Is Verifiable Encryption?

**Verifiable encryption** enables a sender (called the *prover*) to encrypt a message for a receiver (called the *verifier*) **and prove** that the ciphertext contains a certain value and was encrypted correctly — all without revealing the message.

>  The verifier **can** check:
> - The ciphertext encrypts **exactly** the claimed message.
> - The encryption is **correctly formed** under their public key.
> - The prover **knows** the plaintext and randomness.

> The verifier **cannot**:
> - Learn the plaintext value (yet).
> - Decrypt the message unless they hold the private key.

It’s essentially a **commitment** to a message that only the recipient can open — with mathematical proof of what’s inside.

---

## Why Use Verifiable Encryption?

Here are a few key use cases:

- **Digital Voting**
  - Votes are encrypted, and voters must prove their vote is **valid** (e.g., for an eligible candidate) without revealing whom they voted for.

- **Fair Exchange**
  - In digital contracts or protocols, parties exchange encrypted data along with proofs that the data is correct. This ensures fairness — no one can back out after seeing the other side.

- **Escrow & Time-locked Secrets**
  - A party can verifiably encrypt sensitive data and give it to an escrow. The receiver knows it’s correct, but the data remains hidden unless needed.

---

## Ingredients of Verifiable Encryption

To build verifiable encryption, you typically need:

1. **A Public-Key Encryption Scheme** (e.g., ElGamal, RSA, Paillier)
2. **A Zero-Knowledge Proof System** (e.g., Sigma protocols, zk-SNARKs)
3. **Message and Encryption Randomness** that are proved in zero-knowledge

Let’s walk through a simplified example.

### Verifiable Encryption with ElGamal + ZK Proof

We’ll use ElGamal encryption over a cyclic group $G$ of prime order $q$, with generator $g$.

#### Setup

- **Receiver’s public key**: $pk = g^x$, where $x \in \mathbb{Z}_q$ is the private key.
- **Message to encrypt**: $m \in \mathbb{Z}_q$
- **Randomness**: $r \in \mathbb{Z}_q$

#### Encrypting the Message

ElGamal ciphertext:
- $C_1 = g^r$
- $C_2 = pk^r \cdot m = g^{xr} \cdot m$

So the ciphertext is:  
$$C = (C_1, C_2) = (g^r, g^{xr} \cdot m)$$

#### Zero-Knowledge Proof

We want to prove knowledge of $r$ such that:
- $C_1 = g^r$
- $C_2 / m = pk^r$

That is, prove:
$$\log_g(C_1) = \log_{pk}(C_2 / m)$$

This is a **proof of equality of discrete logs**, which can be done using Sigma protocols (like Schnorr’s protocol) or turned non-interactive via Fiat-Shamir.


### Example Proof (Schnorr-style)

Prover wants to prove knowledge of $r$ such that:
- $C_1 = g^r$
- $C_2 = pk^r \cdot m$

#### Step 1: Prover computes

1. Choose random $w \in \mathbb{Z}_q$
2. Compute:
   - $t_1 = g^w$
   - $t_2 = pk^w$

#### Step 2: Challenge

Use the **Fiat-Shamir heuristic** to derive challenge:
$$c = H(C_1, C_2, t_1, t_2)$$

#### Step 3: Response

$$s = w + c \cdot r \mod q$$

#### Step 4: Send Proof

$$\pi = (t_1, t_2, s)$$

#### Step 5: Verifier Checks

- Compute:
  - $g^s \stackrel{?}{=} t_1 \cdot C_1^c$
  - $pk^s \stackrel{?}{=} t_2 \cdot (C_2 / m)^c$

If both hold, the verifier is convinced that the ciphertext encrypts $m$ under the public key, using secret randomness $r$.

---

## Summary of Ciphertext + Proof

Final verifiable encryption bundle:
- Ciphertext: $C = (C_1, C_2)$
- Proof: $\pi = (t_1, t_2, s)$

This package can be published or sent to a third party. Anyone can verify:
- The message inside is **exactly** $m$
- The ciphertext is **correctly formed**
- The prover **knows the value and randomness**

### Comparison: Verifiable Encryption vs Commitments

| Feature | Verifiable Encryption | Commitment Scheme |
|--------|------------------------|-------------------|
| Hides message | ✅ | ✅ |
| Binding | ✅ | ✅ |
| Can be decrypted by receiver | ✅ | ❌ |
| Requires public key | ✅ | ❌ |
| Includes zero-knowledge proof | ✅ | Optional |

### Limitations

- **Computational Overhead**: Proof generation and verification can be expensive.
- **Public Key Dependence**: You must know the receiver's public key in advance.
- **Security Assumptions**: Depends on hardness of discrete log or RSA assumptions.

### Further Reading

- [Camenisch & Shoup (2003)](https://eprint.iacr.org/2002/067): Practical Verifiable Encryption
- [Boneh et al. (1998)](https://crypto.stanford.edu/~dabo/pubs/papers/verenc.pdf): Discrete Log Encryption Proofs
- [Zero-Knowledge Proofs Overview](https://zkproof.org/)

---

## Verifiable Encryption in Voting: Ensuring Privacy and Validity

In modern cryptographic voting systems, **verifiable encryption** ensures that every encrypted vote is **valid**, **correctly formed**, and **keeps voter privacy** intact.

This example explores how verifiable encryption is used in voting — step by step — using ElGamal encryption and zero-knowledge proofs.

### What Problem Does It Solve?

In electronic voting, votes are **encrypted** to preserve voter privacy. But if we can’t see the vote, how can we be sure it’s valid?

>  **We want to prove that**:
> - The vote is for a **valid candidate**
> - The encryption is **honest and correct**
> - The content remains **hidden**

This is where **verifiable encryption** comes in.

### 🧩 Setup

Let’s define a simplified election system:

- Group $\mathbb{G}$ of prime order $q$, with generator $g$
- Election public key: $pk = g^x$, where $x$ is secret (held by election authority)
- Voter’s options: Candidates $\{1, 2, 3\}$

Votes are encoded as:
- Candidate 1 → $m_1 = g^1$
- Candidate 2 → $m_2 = g^2$
- Candidate 3 → $m_3 = g^3$

### Example: Voter Encrypts Vote for Candidate 2

Let the voter choose candidate 2, so:
$$m = g^2$$

Pick random $r \in \mathbb{Z}_q$, then compute ElGamal encryption:

$$C_1 = g^r,\quad C_2 = m \cdot pk^r = g^2 \cdot g^{xr} = g^{xr + 2}$$

So the encrypted ballot is:

$$C = (C_1, C_2)$$

### Verifiable Encryption: Proof of Valid Vote

We don’t want the voter to submit $g^{99}$ as a bogus vote.

So the voter adds a **zero-knowledge proof**:

> "This ciphertext encrypts **one of** $\{g^1, g^2, g^3\}$ using ElGamal."

This is a **disjunctive proof** (also called an OR-proof) — it proves that one of the possible statements is true, but hides which one.

#### OR-Proof (Sketch)

The voter constructs 3 proofs:

- One **real** proof for the value they actually encrypted (e.g., $m = g^2$)
- Two **simulated** (fake) proofs for the other values
- Combine all 3 into a single non-interactive ZK proof using Fiat-Shamir heuristic

This allows:
- Verifier to check: “One of the options is valid”
- Without learning: “Which candidate the voter chose”

### Ballot Submission

The voter submits:
- Ciphertext $C = (C_1, C_2)$
- ZK proof $\pi$ of valid vote

The server:
- Verifies the proof $\pi$
- Stores the ciphertext
- Keeps the vote content secret


### Tallying Phase

#### Option 1: Individual Decryption

Each encrypted vote is decrypted by election trustees:

$$m = C_2 / C_1^x$$

Recovered $m = g^1, g^2, g^3$ is mapped back to candidate.

#### Option 2: Homomorphic Tallying

Votes are multiplied:

$$C_{\text{sum}} = \prod C_i$$

Then decrypted once:

$$g^{\text{sum of votes}} \Rightarrow \text{tally}$$

Voter privacy is preserved, yet the result is public and verifiable.


### Summary

| Feature | Achieved With |
|--------|----------------|
| Voter privacy | Encryption |
| Vote validity | Verifiable encryption (ZK proof) |
| Tally integrity | Public decryption / verification |
| Trust minimization | Zero-knowledge + threshold crypto |

### Real-World Systems

- **Helios** (open-source web voting)
- **Belenios** (French secure e-voting system)
- **Civitas**, **Microsoft ElectionGuard**
- E-voting in **Estonia**, **Norway**, and more

All use **verifiable encryption** to guarantee vote correctness while preserving voter anonymity.

### Further Reading

- [Helios Voting System](https://heliosvoting.org/)
- [Camenisch et al. (2003) - Verifiable Encryption](https://eprint.iacr.org/2002/067)
- [Microsoft ElectionGuard](https://www.microsoft.com/en-us/security/blog/2022/11/01/electionguard-now-available-to-enable-verifiable-secure-voting/)

---

## Conclusion
Verifiable encryption is a cornerstone of cryptographic protocols where **privacy**, **integrity**, and **verifiability** must coexist. From digital elections to smart contracts, it enables a future where secrets can be safely sealed — and proven — without revealing them prematurely.

Verifiable encryption enables a beautiful balance in e-voting:
- ✅ **Trustable**: Everyone can verify the ballot is correct
- ✅ **Private**: No one knows how you voted
- ✅ **Auditable**: The system can be publicly checked

It’s one of the building blocks of modern secure democratic systems.

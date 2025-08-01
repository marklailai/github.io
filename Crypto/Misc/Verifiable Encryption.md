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

---

## Verifiable Encryption with ElGamal + ZK Proof

We’ll use ElGamal encryption over a cyclic group $G$ of prime order $q$, with generator $g$.

### Setup

- **Receiver’s public key**: $pk = g^x$, where $x \in \mathbb{Z}_q$ is the private key.
- **Message to encrypt**: $m \in \mathbb{Z}_q$
- **Randomness**: $r \in \mathbb{Z}_q$

### Encrypting the Message

ElGamal ciphertext:
- $C_1 = g^r$
- $C_2 = pk^r \cdot m = g^{xr} \cdot m$

So the ciphertext is:  
$$C = (C_1, C_2) = (g^r, g^{xr} \cdot m)$$

### Zero-Knowledge Proof

We want to prove knowledge of $r$ such that:
- $C_1 = g^r$
- $C_2 / m = pk^r$

That is, prove:
$$\log_g(C_1) = \log_{pk}(C_2 / m)$$

This is a **proof of equality of discrete logs**, which can be done using Sigma protocols (like Schnorr’s protocol) or turned non-interactive via Fiat-Shamir.

---

## Example Proof (Schnorr-style)

Prover wants to prove knowledge of $r$ such that:
- $C_1 = g^r$
- $C_2 = pk^r \cdot m$

### Step 1: Prover computes

1. Choose random $w \in \mathbb{Z}_q$
2. Compute:
   - $t_1 = g^w$
   - $t_2 = pk^w$

### Step 2: Challenge

Use the **Fiat-Shamir heuristic** to derive challenge:
$$c = H(C_1, C_2, t_1, t_2)$$

### Step 3: Response

$$s = w + c \cdot r \mod q$$

### Step 4: Send Proof

$$\pi = (t_1, t_2, s)$$

### Step 5: Verifier Checks

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

---

## Comparison: Verifiable Encryption vs Commitments

| Feature | Verifiable Encryption | Commitment Scheme |
|--------|------------------------|-------------------|
| Hides message | ✅ | ✅ |
| Binding | ✅ | ✅ |
| Can be decrypted by receiver | ✅ | ❌ |
| Requires public key | ✅ | ❌ |
| Includes zero-knowledge proof | ✅ | Optional |

---

## Limitations

- **Computational Overhead**: Proof generation and verification can be expensive.
- **Public Key Dependence**: You must know the receiver's public key in advance.
- **Security Assumptions**: Depends on hardness of discrete log or RSA assumptions.

---

## Further Reading

- [Camenisch & Shoup (2003)](https://eprint.iacr.org/2002/067): Practical Verifiable Encryption
- [Boneh et al. (1998)](https://crypto.stanford.edu/~dabo/pubs/papers/verenc.pdf): Discrete Log Encryption Proofs
- [Zero-Knowledge Proofs Overview](https://zkproof.org/)

---

## Conclusion

Verifiable encryption is a cornerstone of cryptographic protocols where **privacy**, **integrity**, and **verifiability** must coexist. From digital elections to smart contracts, it enables a future where secrets can be safely sealed — and proven — without revealing them prematurely.

Stay tuned for a follow-up post with a **Python demo** of ElGamal verifiable encryption using open-source libraries!


# Comparing Authenticated Encryption: ETM, MTE, and E&M

## Introduction
In secure communication, **authenticated encryption (AE)** ensures both confidentiality (via encryption) and integrity (via authentication) of data. Three primary approaches exist: **Encrypt-then-MAC (ETM)**, **MAC-then-Encrypt (MTE)**, and **Encrypt-and-MAC (E&M)**. This post explores each method, their security implications, and real-world use cases.

---

## 1. Encrypt-then-MAC (ETM)
### Process:
1. Encrypt the plaintext: $C = Encrypt(K_e, P)$  
2. Compute MAC over the ciphertext: $T = MAC(K_m, C)$  
3. Transmit $(C, T)$.

### Security Highlights:
-  **Strongest security guarantees** (proven secure under standard models).  
-  Ciphertext integrity protects against tampering.  
-  Slightly slower (two passes over data).

### Why It’s Secure:
- The MAC covers the ciphertext. An attacker altering `C` must forge `T`, which is infeasible with a secure MAC.  
- Leaks no plaintext information via the tag.

### Used In:
- TLS 1.2/IPsec (with specific ciphersuites).  
- SSH.  
- Modern protocols like Signal.

---

## 2. MAC-then-Encrypt (MTE)
### Process:
1. Compute MAC over the plaintext: $T = MAC(K_m, P)$  
2. Encrypt $(P, T)$: $C = Encrypt(K_e, P \parallel T)$  
3. Transmit $C$.

### Security Highlights:
-  **Security depends on encryption mode**:  
  - Secure with **stateful modes** (e.g., AES-GCM).  
  - Vulnerable with **stream ciphers** (e.g., RC4) or CBC padding oracles.  
-  Single encryption pass (potentially faster).

### Risks:
- If decryption outputs $(P, T)$ before verifying the MAC, attackers may exploit padding/errors (e.g., BEAST attack).  
- Tag is encrypted → no integrity check until decryption.

### Used In:
- TLS 1.0/1.1 (historically with CBC modes).  
- Java’s TLS implementation (older versions).

---

## 3. Encrypt-and-MAC (E&M)
### Process:
1. Encrypt the plaintext: $C = Encrypt(K_e, P)$  
2. Compute MAC over the **plaintext**: $T = MAC(K_m, P)$  
3. Transmit $(C, T)$.

### Security Highlights:
-  **Weakest of the three**:  
  - Tag leaks plaintext information.  
  - No binding between $C$ and $T$ → attackers can replay $(C, T')$.  
-  Parallelizable (encryption + MAC can run simultaneously).

### Risks:
- MAC on plaintext may expose data patterns.  
- Attacker can replace $C$ (decrypts to garbage) but reuse valid $T$.

### Used In:
- **Avoided in modern designs** due to vulnerabilities.  
- Legacy systems (e.g., SSHv1, now deprecated).

---

## Comparison Table
| **Criteria**       | **ETM**                  | **MTE**                     | **E&M**                     |
|--------------------|--------------------------|-----------------------------|-----------------------------|
| **Security**       | ★★★★★ (Strongest)        | ★★★☆☆ (Varies with cipher)  | ★★☆☆☆ (Weakest)             |
| **Performance**    | Moderate (2 passes)      | Fast (1 pass)               | Fast (parallelizable)       |
| **Ciphertext Integrity** | Yes                   | Depends on decryption order | No (tag covers plaintext)   |
| **Vulnerabilities**| None known              | Padding/error oracles       | Plaintext leaks, replay     |
| **Real-World Use** | TLS 1.2+, SSH, Signal   | Legacy TLS (CBC mode)       | Deprecated (SSHv1)          |

---

## Why ETM Dominates Modern Cryptography
1. **Provable Security**: Reduces to IND-CCA2 security if encryption is IND-CPA and MAC is SUF-CMA.  
2. **Early Rejection**: Verify MAC *before* decryption → mitigates DoS and side-channel attacks.  
3. **Defense-in-Depth**: Tampering with ciphertext is detected immediately.

> 💡 **Rule of Thumb**: Prefer ETM unless constrained by legacy systems. Always use independent keys for encryption and MAC!

---

## Conclusion
- **ETM** is the **gold standard** for new designs.  
- **MTE** requires careful implementation to avoid pitfalls.  
- **E&M** is **insecure** and should be avoided.  

When implementing AE, always use standardized constructions (e.g., AES-GCM, ChaCha20-Poly1305) rather than rolling your own. Security isn’t just about algorithms—it’s about how they compose! 🔒

---
*Further Reading: [NIST SP 800-38D](https://csrc.nist.gov/publications/detail/sp/800-38d/final), [RFC 5116](https://datatracker.ietf.org/doc/html/rfc5116)*  

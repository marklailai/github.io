# SIMD Acceleration on x86 and ARM: Boosting SM2, SM3, and SM4 Cryptography

Cryptography is at the heart of modern secure systems. Whether you're implementing VPNs, TLS, digital signatures, or national standards like China's **SM2**, **SM3**, and **SM4**, **performance matters**. And one of the best-kept secrets to achieving high throughput is using **SIMD** instructions.

In this post, we’ll explore how **AVX2** (on x86) and **NEON** (on ARM) can supercharge cryptographic operations—specifically for SM2, SM3, and SM4. We’ll dive deep into implementation strategies, performance techniques, and common pitfalls.

---

## What is SIMD?

**SIMD (Single Instruction, Multiple Data)** is a CPU feature that allows parallel processing of multiple data elements using a single instruction. Instead of looping over individual operations, SIMD processes **vectors of data** simultaneously—ideal for data-parallel problems like encryption or hashing.

### Examples:

- Encrypting **4 or 8 SM4 blocks** in parallel.
- Hashing **multiple SM3 message blocks** concurrently.
- Accelerating **large-number modular arithmetic** in SM2 with vectorized multiplications.

---

## 🧱 SIMD Architecture Comparison: AVX2 vs NEON

| Feature               | x86: AVX2                      | ARM: NEON                          |
|----------------------|-------------------------------|------------------------------------|
| Register width        | 256 bits                      | 128 bits                           |
| Vector size           | 8 × 32-bit or 4 × 64-bit ints | 4 × 32-bit or 2 × 64-bit ints      |
| Available on          | Intel Haswell+, AMD Ryzen     | ARM Cortex-A (A7, A72, A76, etc.)  |
| Power consumption     | High                          | Low                                |
| Instruction set       | Rich, flexible                | Simpler, fixed                     |
| Crypto library support| OpenSSL, IPP, BoringSSL       | BoringSSL, mbedTLS, wolfSSL        |

---

## 🇨🇳 SM2/SM3/SM4 Overview

### SM2 – Public-Key ECC

- Based on elliptic curve over GF(p)
- Used for digital signatures, encryption, key exchange
- Computational bottlenecks:
  - Big-number modular arithmetic (256-bit integers)
  - Scalar-point multiplication

### SM3 – Cryptographic Hash Function

- Similar to SHA-256
- Operates on 512-bit message blocks
- 64 compression rounds
- Suitable for SIMD-based **message-parallel** or **round-parallel** acceleration

### SM4 – Block Cipher

- 128-bit block size, 128-bit key
- 32 rounds of substitution-permutation network (SPN)
- Perfect for **block-parallel** SIMD encryption (like AES-NI)

---

## 🚀 SIMD Acceleration of SM4

### 🔄 SM4 Round Function Structure

Each round:

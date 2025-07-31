# SIMD Acceleration on x86 and ARM: Boosting SM2, SM3, and SM4 Cryptography

Cryptography is at the heart of modern secure systems. Whether you're implementing VPNs, TLS, digital signatures, or national standards like China's **SM2**, **SM3**, and **SM4**, **performance matters**. And one of the best-kept secrets to achieving high throughput is using **SIMD** instructions.

In this post, we’ll explore how **AVX2** (on x86) and **NEON** (on ARM) can supercharge cryptographic operations—specifically for SM2, SM3, and SM4. We’ll dive deep into implementation strategies, performance techniques, and common pitfalls.

---

## What is SIMD?

**SIMD (Single Instruction, Multiple Data)** is a CPU feature that allows parallel processing of multiple data elements using a single instruction. Instead of looping over individual operations, SIMD processes **vectors of data** simultaneously—ideal for data-parallel problems like encryption or hashing.

Imagine you want to add two arrays:
```c
A = [1, 2, 3, 4]
B = [5, 6, 7, 8]
```

A **regular CPU loop** might do:
```c
for (int i = 0; i < 4; i++) {
    C[i] = A[i] + B[i];
}
```
With SIMD, the CPU can perform the additions in one or two operations instead of four, using special wide registers (e.g., 128-bit, 256-bit, 512-bit) that hold multiple values.

**Benifit of SIMD**:
- Speeds up repetitive data operations
- Reduces CPU cycles for bulk computations
- Widely used in high-performance computing and real-time applications

**Application examples**:
- Encrypting **4 or 8 SM4 blocks** in parallel.
- Hashing **multiple SM3 message blocks** concurrently.
- Accelerating **large-number modular arithmetic** in SM2 with vectorized multiplications.

### SIMD Architecture Comparison: AVX2 vs NEON

| Feature               | x86: AVX2                      | ARM: NEON                          |
|----------------------|-------------------------------|------------------------------------|
| Register width        | 256 bits                      | 128 bits                           |
| Vector size           | 8 × 32-bit or 4 × 64-bit ints | 4 × 32-bit or 2 × 64-bit ints      |
| Available on          | Intel Haswell+, AMD Ryzen     | ARM Cortex-A (A7, A72, A76, etc.)  |
| Power consumption     | High                          | Low                                |
| Instruction set       | Rich, flexible                | Simpler, fixed                     |
| Crypto library support| OpenSSL, IPP, BoringSSL       | BoringSSL, mbedTLS, wolfSSL        |

---

## How to use SIMD

Using SIMD (Single Instruction, Multiple Data) means writing code that takes advantage of vectorized instructions provided by your CPU, such as AVX, SSE (on x86/x86-64), or NEON (on ARM). Here's a guide to help you get started:

### 1. Choose a Method to Access SIMD
You can use SIMD in one of three ways:

#### (A) Compiler Auto-Vectorization
Write regular C/C++ loops.

Let the compiler optimize to SIMD behind the scenes.

```c
// Example: auto-vectorized loop
void add(float* a, float* b, float* out, int len) {
    for (int i = 0; i < len; i++) {
        out[i] = a[i] + b[i];
    }
}
```
**Tips**:

- Compile with optimization flags like -O3 -march=native (GCC/Clang).
- Check with -ftree-vectorizer-verbose=2 (GCC) or -Rpass=loop-vectorized (Clang) to see if SIMD is applied.

#### (B) Intrinsics (Manual SIMD)
Write code with SIMD intrinsics—functions that map directly to SIMD CPU instructions.

**Example: AVX2 float addition (x86_64)**
```c
#include <immintrin.h>

void add_avx(float* a, float* b, float* out) {
    __m256 va = _mm256_loadu_ps(a);     // Load 8 floats
    __m256 vb = _mm256_loadu_ps(b);     // Load 8 floats
    __m256 vc = _mm256_add_ps(va, vb);  // Add
    _mm256_storeu_ps(out, vc);          // Store result
}
```
**Notes**:

- Requires data alignment (or use loadu/storeu for unaligned).
- You control exactly what happens at the hardware level.
- Available for AVX, AVX2, AVX-512, SSE2, SSE4.1, etc.

#### (C) SIMD Libraries
Use high-level SIMD abstraction libraries:

- SIMDe: portable SIMD in C
- xsimd: C++ headers for SIMD types
- Vc: vector classes with AVX/SSE support
- NumPy + Numba (Python) with SIMD parallelization

### 2. Compile with SIMD Support
For AVX2 (x86):
```bash
gcc -O3 -mavx2 -march=native simd_test.c -o simd_test
```
For NEON (ARM):
```bash
gcc -O3 -mfpu=neon -march=armv8-a+simd simd_test.c -o simd_test
```

### 3. Test and Benchmark
Compare performance between scalar and SIMD versions using:

- `chrono` in C++
- `time` in C
- `perf` or `valgrind` tools
- Custom timers for latency and throughput

---

## CN SM2/SM3/SM4 Overview

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

## SIMD Acceleration of SM4

### SM4 Round Function Structure

Each round:
$T(x) = L(τ(x))$

- **τ(x)**: 8-bit S-box substitutions (non-linear)
- **L(x)**: Linear transform using XOR and rotate

### SIMD Strategy

- **Parallel block encryption** (ECB/CTR mode): Best use-case
- **Bitslicing** possible, but more complex than AES
- SIMD implementation focuses on parallel evaluation of 4/8 rounds across multiple blocks

### AVX2 Implementation (4-way SM4)

```c
#include <immintrin.h>

void sm4_encrypt_4x(__m128i blocks[4], const __m128i rk[32]) {
    for (int i = 0; i < 32; ++i) {
        __m128i rkey = _mm_set1_epi32(((uint32_t*)&rk[i])[0]);
        for (int j = 0; j < 4; j++) {
            blocks[j] = sm4_round(blocks[j], rkey);
        }
    }
}
```
For 8-way, use __m256i and _mm256_* intrinsics. You’ll need:

- Vectorized XOR
- Vectorized rotate left: `_mm256_or_si256(_mm256_slli_epi32(...)`, `_mm256_srli_epi32(...))`
- Efficient shuffle for S-box substitution

### NEON Implementation
```c
#include <arm_neon.h>

void sm4_encrypt_neon(uint32x4_t blocks[4], const uint32x4_t rk[32]) {
    for (int i = 0; i < 32; ++i) {
        uint32x4_t key = vdupq_n_u32(((uint32_t*)&rk[i])[0]);
        for (int j = 0; j < 4; j++) {
            blocks[j] = sm4_round_neon(blocks[j], key);
        }
    }
}
```
Key challenges:

- Emulating rotate operations with `vshlq_n_u32`, `vshrq_n_u32`
- Table lookups for S-box: `vtbl`, or scalar + vector combination

**Performance**:
- AVX2: Up to 6–8× faster over scalar
- NEON: Up to 3–4× on mobile CPUs

---

## SIMD Acceleration of SM3
### SM3 Structure
- Processes 512-bit message blocks
- 64 rounds per block
- Heavy use of 32-bit rotates, XORs, conditional functions (FF, GG)

### SIMD Strategy
1. Parallel message blocks (best suited for AVX2):
  - Each lane processes an independent block
2. Round parallelism (more complex):
  - Multiple rounds of the same block processed using SIMD
3. W message schedule (SIMD-friendly):
  - Heavy on shifts, XORs—easy to parallelize

### AVX2 Sample (Parallel Message Processing)
```c
__m256i A = _mm256_set1_epi32(V[0]);
__m256i B = _mm256_set1_epi32(V[1]);
// ...
for (int i = 0; i < 64; i++) {
    __m256i SS1 = _mm256_rol_epi32(...); // custom rotate
    __m256i TT1 = _mm256_add_epi32(...);
    // Perform FF/TT logic
}
```

### NEON Sample
```c
uint32x4_t A = vdupq_n_u32(V[0]);
uint32x4_t B = vdupq_n_u32(V[1]);
// ...
for (int i = 0; i < 64; i++) {
    uint32x4_t SS1 = vorrq_u32(vshlq_n_u32(...), vshrq_n_u32(...));
    // Vectorized XORs and rotates
}
```
**Performance**
- AVX2: Hashing performance close to or above SHA-256 w/ SIMD
- NEON: Mobile-friendly acceleration, ideal for Android secure messaging

---

## SIMD Acceleration of SM2
### SIMD in ECC is Harder
- SM2 is based on ECC over GF(p)
- SIMD works best for **fixed-size, block-oriented** operations
- ECC involves **conditional logic, carry propagation**, and **variable timing**

### SIMD-Accelerated Techniques
Field multiplication
Use SIMD for Montgomery/Barrett multiplication
SIMD Karatsuba for large 256-bit integer multiplication
Batch operations
Batch signature verification (multi-scalar multiplication)

Useful in authentication servers

SIMD-friendly libraries

Use __m256i or NEON to accelerate:

Schoolbook multiplication

Modular reduction

Point doubling/addition

AVX2 Modular Multiplication
c
复制
编辑
__m256i A_lo = _mm256_loadu_si256(...);
__m256i B_lo = _mm256_loadu_si256(...);
__m256i prod = _mm256_mul_epu32(A_lo, B_lo);
// Accumulate + reduce
NEON Multiplication
c
复制
编辑
uint64x2_t A = vld1q_u64(...);
uint64x2_t B = vld1q_u64(...);
uint64x2_t prod = vmulq_u64(A, B);
// Combine and reduce
🧪 Benchmarking & Toolchain
Use rdtsc, clock_gettime(), or perf for timing

Align data (AVX2 needs 32-byte aligned memory)

Enable compiler flags:

-mavx2 -O3 (x86)

-mfpu=neon -O3 (ARM)

Libraries to Explore
Intel IPP Crypto

ARM mbedTLS

SIMDe (SIMD Everywhere)

BoringSSL

OpenSSL 3.x

📌 Final Thoughts
SIMD is a powerful tool for cryptography. For block-oriented ciphers like SM4 and hash functions like SM3, SIMD acceleration can yield multi-fold performance gains. Even for SM2, careful optimization of field arithmetic with SIMD yields real-world benefits, especially in high-load verification scenarios.

Whether you're targeting Intel Xeon or ARM Cortex-A, understanding SIMD can turn your crypto from sluggish to blazing fast.

🛠 Need help implementing SM4 in AVX2 or tuning SM3 for NEON? Drop a comment or request a sample repo!

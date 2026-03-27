Here is the fully organized guide with all original text, tables, and specific diagram placeholders (empty headings) kept exactly as they appeared in the source document.

### 1. DID (Decentralized Identifier) Research Overview & W3C Standards
#### What is a DID?
A **Decentralized Identifier (DID)** is a new type of globally unique identifier defined by W3C that enables verifiable, self-sovereign digital identity. Unlike traditional identifiers like email or usernames, DIDs are decentralized (no central authority required), persistent (cannot be taken away administratively), cryptographically verifiable (proof of ownership), and resolvable (can be looked up to discover associated information).

#### How Users Use DID (Real-World Scenario)
###### Simple Analogy
Think of a DID as your **digital passport** that you create and control yourself, rather than one issued by a government. Just like a physical passport proves your identity when traveling, a DID proves your digital identity online—but you own it completely.

###### Real-World Application: Job Application with Verifiable Credentials
Let's walk through a practical scenario where Alice uses her DID to apply for a job:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Alice's Job Application Journey                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: Create Digital Identity                                            │
│  ─────────────────────────────────                                          │
│  Alice downloads a DID wallet app (like Dock Wallet or Trinsic Wallet)      │
│                                                                             │
│  ┌─────────────┐                                                            │
│  │ Alice's     │  Creates                                                   │
│  │ Smartphone  │ ─────────>  did:key:z6MkhaXg... (Alice's DID)              │
│  │             │         ┌─────────────────────────┐                        │
│  └─────────────┘         │  Private Key (kept      │                        │
│                          │  secret on phone)       │                        │
│                          │  Public Key (shared     │                        │
│                          │  with others)           │                        │
│                          └─────────────────────────┘                        │
│                                                                             │
│  STEP 2: Receive Verifiable Credentials                                     │
│  ─────────────────────────────────────                                      │
│                                                                             │
│  ┌──────────────┐      Issue Credential      ┌──────────────┐               │
│  │  University  │ ──────────────────────────>│  Alice's     │               │
│  │  (Issuer)    │   "Bachelor of Science     │  DID Wallet  │               │
│  │  did:web:    │    in Computer Science"    │              │               │
│  │  uni.edu     │   Signed with uni's key    │ <X> Stored   │               │
│  └──────────────┘                            └──────────────┘               │
│                                                                             │
│  ┌──────────────┐      Issue Credential      ┌──────────────┐               │
│  │  Previous    │ ──────────────────────────>│  Alice's     │               │
│  │  Employer    │   "3 Years Experience      │  DID Wallet  │               │
│  │  (Issuer)    │    Certificate"            │              │               │
│  └──────────────┘                            └──────────────┘               │
│                                                                             │
│  STEP 3: Apply for Job                                                      │
│  ───────────────────                                                        │
│                                                                             │
│  ┌──────────────┐      Scan QR Code          ┌──────────────┐               │
│  │  TechCorp    │ <──────────────────────────│  Alice's     │               │
│  │  (Employer)  │   "We need your degree     │  Smartphone  │               │
│  │              │    and experience proof"   │              │               │
│  └──────────────┘                            └──────────────┘               │
│                                                                             │
│  STEP 4: Selective Disclosure                                               │
│  ───────────────────────────                                                │
│                                                                             │
│  Alice reviews the request and chooses what to share:                       │
│  <X> University Degree (Bachelor of Science)                                │
│  <X> Years of Experience (3 years)                                          │
│  < > Home Address (not shared)                                              │
│  < > Date of Birth (not shared)                                             │
│                                                                             │
│  STEP 5: Present Credentials                                                │
│  ─────────────────────────                                                  │
│                                                                             │
│  Alice's wallet creates a presentation:                                     │
│  ┌─────────────────────────────────────────────────────────┐                │
│  │  Verifiable Presentation                                │                │
│  │  ─────────────────────                                  │                │
│  │  Holder: did:key:z6MkhaXg... (Alice's DID)              │                │
│  │                                                         │                │
│  │  Credentials:                                           │                │
│  │  1. University Degree (signed by uni.edu)               │                │
│  │  2. Experience Certificate (signed by prev-employer)    │                │
│  │                                                         │                │
│  │  Proof: Alice's digital signature                       │                │
│  └─────────────────────────────────────────────────────────┘                │
│                                                                             │
│  STEP 6: Employer Verification                                              │
│  ───────────────────────────                                                │
│                                                                             │
│  TechCorp's system automatically verifies:                                  │
│  1. <X> University signature is valid (uni.edu is a recognized institution) │
│  2. <X> Previous employer signature is valid                                │
│  3. <X> Alice's presentation signature proves she controls the DID          │
│  4. <X> Credentials have not been tampered with                             │
│  5. <X> Credentials are not expired or revoked                              │
│                                                                             │
│  Result: Alice gets the interview!!!                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### ⚠️ Important Clarification: DID vs Verifiable Credentials
**The DID Document does NOT contain the credentials!** Here's the correct relationship:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│           Relationship: DID Document vs Verifiable Credentials              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    ALICE'S DID DOCUMENT                             │    │
│  │                    (Stored in DID Registry)                         │    │
│  │                                                                     │    │
│  │  {                                                                  │    │
│  │    "id": "did:key:z6MkhaXg...",      ◄── Alice's DID                │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "id": "#key-1",                                                │    │
│  │      "type": "Ed25519VerificationKey2020",                          │    │
│  │      "publicKeyMultibase": "z6MkhaXg..."  ◄── Alice's Public Key    │    │
│  │    }],                                                              │    │
│  │    "authentication": ["#key-1"]      ◄── Key for proving control    │    │
│  │  }                                                                  │    │
│  │                                                                     │    │
│  │  Contains: Cryptographic keys ONLY                                  │    │
│  │  Does NOT contain: Degrees, experience, personal data               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    ▲                                        │
│                                    │                                        │
│                    DID is referenced in credentials                         │
│                                    │                                        │
│  ┌─────────────────────────────────┴─────────────────────────────────────┐  │
│  │              ALICE'S VERIFIABLE CREDENTIALS (Stored in Wallet)        │  │
│  │                                                                       │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │  CREDENTIAL 1: University Degree                                │  │  │
│  │  │  ─────────────────────────────                                  │  │  │
│  │  │  {                                                              │  │  │
│  │  │    "@context": ["https://www.w3.org/2018/credentials/v1"],      │  │  │
│  │  │    "id": "urn:uuid:1234...",                                    │  │  │
│  │  │    "type": ["VerifiableCredential", "DegreeCredential"],        │  │  │
│  │  │    "issuer": "did:web:uni.edu",      ◄── University's DID       │  │  │
│  │  │    "credentialSubject": {                                       │  │  │
│  │  │      "id": "did:key:z6MkhaXg...",    ◄── Alice's DID (subject)  │  │  │
│  │  │      "degree": "Bachelor of Science",                           │  │  │
│  │  │      "field": "Computer Science",                               │  │  │
│  │  │      "graduationDate": "2023-05-15"                             │  │  │
│  │  │    },                                                           │  │  │
│  │  │    "proof": {                                                   │  │  │
│  │  │      "type": "Ed25519Signature2020",                            │  │  │
│  │  │      "created": "2023-05-15T10:00:00Z",                         │  │  │
│  │  │      "proofValue": "z58DAdF..."    ◄── University's Signature   │  │  │
│  │  │    }                                                            │  │  │
│  │  │  }                                                              │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                       │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │  CREDENTIAL 2: Experience Certificate                           │  │  │
│  │  │  ─────────────────────────────────                              │  │  │
│  │  │  {                                                              │  │  │
│  │  │    "issuer": "did:web:prev-employer.com",                       │  │  │
│  │  │    "credentialSubject": {                                       │  │  │
│  │  │      "id": "did:key:z6MkhaXg...",    ◄── Alice's DID (subject)  │  │  │
│  │  │      "position": "Software Engineer",                           │  │  │
│  │  │      "years": 3                                                 │  │  │
│  │  │    },                                                           │  │  │
│  │  │    "proof": { ... }                ◄── Employer's Signature     │  │  │
│  │  │  }                                                              │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                       │  │
│  │  Credentials are signed by issuers and stored in Alice's wallet       │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**How Each Component Works:**
| Component | What It Contains | Who Creates It | Where It's Stored |
| :--- | :--- | :--- | :--- |
| **DID Document** | Public keys for verification | Alice (or method-specific process) | DID Registry (blockchain, web server, etc.) |
| **Verifiable Credential** | Claims about Alice (degree, experience) | Issuer (University, Employer) | Alice's wallet (credential holder) |
| **Private Key** | Secret key for signing | Alice's wallet | Securely on Alice's device only |


**How Verification Works:**
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Verification Process                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. ISSUER SIGNS CREDENTIAL                                                 │
│  ─────────────────────────                                                  │
│  University creates credential with Alice's DID as subject                  │
│  University signs with its private key                                      │
│                                                                             │
│  ┌─────────────┐         Sign with           ┌──────────────────────┐       │
│  │  University │ ────>   University's  ────> │ Signed Credential    │       │
│  │  Private Key│         Private Key         │ (given to Alice)     │       │
│  └─────────────┘                             └──────────────────────┘       │
│                                                                             │
│  2. ALICE PRESENTS CREDENTIAL                                               │
│  ────────────────────────────                                               │
│  Alice creates presentation with her private key                            │
│                                                                             │
│  ┌─────────────┐         Sign with           ┌──────────────────────┐       │
│  │  Alice's    │ ────>   Alice's     ────>   │ Verifiable           │       │
│  │  Private Key│         Private Key         │ Presentation         │       │
│  └─────────────┘                             └──────────────────────┘       │
│                                                                             │
│  3. EMPLOYER VERIFIES                                                       │
│  ───────────────────                                                        │
│  Employer checks both signatures using public DIDs                          │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  a) Resolve University's DID ────> Get University's Public Key      │    │
│  │  b) Verify credential signature ────>  Valid                        │    │
│  │                                                                     │    │
│  │  c) Resolve Alice's DID ────> Get Alice's Public Key                │    │
│  │  d) Verify presentation signature ────>  Valid                      │    │
│  │                                                                     │    │
│  │  Result: Both signatures valid, Alice is the legitimate holder      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```
**Key Points:**
1. **DID Document = Keys Only** - Contains public keys for cryptographic verification.
2. **Credentials = Claims + Signatures** - Contain actual data (degree, experience) signed by issuers.
3. **Subject Binding** - Each credential references Alice's DID in the credentialSubject.id field.
4. **Issuer Verification** - Anyone can verify by resolving the issuer's DID to get their public key.
5. **Holder Binding** - Alice proves she's the legitimate holder by signing the presentation with her private key.

**Simple Summary: One DID, Many Credentials**

```
                    ONE DID (Alice's Identity)
                         did:key:z6Mk...
                              │
                              │  Stored in DID Registry
                              │  (contains only public keys)
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
    │   VC #1         │ │   VC #2         │ │   VC #3         │
    │   University    │ │   Employer      │ │   Government    │
    │   Degree        │ │   Certificate   │ │   ID Card       │
    │                 │ │                 │ │                 │
    │  Subject:       │ │  Subject:       │ │  Subject:       │
    │  did:key:z6Mk.. │ │  did:key:z6Mk.. │ │  did:key:z6Mk.. │
    └─────────────────┘ └─────────────────┘ └─────────────────┘
              │               │               │
              └───────────────┴───────────────┘
                              │
                    Stored in Alice's Wallet
                    (multiple credentials connect
                     to one DID)
```
**In Short:**
* ✅ **One DID** = One identity identifier (stored on registry).
* ✅ **Many VCs** = Multiple credentials all reference the same DID (stored in wallet).
* ✅ **One-to-Many Relationship** = Single DID : Multiple Verifiable Credentials.

###### Why Verify Alice is the Holder? (Even When We Trust the University)
**The Critical Security Problem:**
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    The Stolen Credential Attack                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SCENARIO: What if someone steals Alice's credentials?                      │
│                                                                             │
│  WITHOUT HOLDER VERIFICATION (Vulnerable):                                  │
│  ─────────────────────────────────────────                                  │
│                                                                             │
│  1. University issues degree to Alice                                       │
│     ┌──────────────┐         Issue VC         ┌──────────────┐              │
│     │  University  │ ────────────────────────>│    Alice     │              │
│     │  (Trusted)   │   "Degree for Alice"     │   (Hacker    │              │
│     └──────────────┘                          │   steals it) │              │
│                                               └──────────────┘              │
│                                                                             │
│  2. Hacker applies for job with stolen credential                           │
│     ┌──────────────┐         Present VC        ┌──────────────┐             │
│     │    Hacker    │ ────────────────────────> │   TechCorp   │             │
│     │  (Impostor)  │   "Here's my degree"      │  (Employer)  │             │
│     └──────────────┘                           └──────────────┘             │
│                                                                             │
│  3. TechCorp verifies ONLY the university signature                         │
│     <X> University signature valid                                          │
│     <X> Credential not revoked                                              │
│     <N> BUT: Is this really Alice presenting it?                            │
│                                                                             │
│  4. Result: Hacker gets the job!  FRAUD!                                    │
│                                                                             │
│  PROBLEM: Anyone with a copy of the credential can impersonate Alice!       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**The Solution: Holder Binding Verification**
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Secure Verification WITH Holder Binding                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  WITH HOLDER VERIFICATION (Secure):                                         │
│  ───────────────────────────────────                                        │
│                                                                             │
│  1. University issues degree to Alice's DID                                 │
│     ┌──────────────┐         Issue VC         ┌──────────────┐              │
│     │  University  │ ────────────────────────>│    Alice     │              │
│     │  (Trusted)   │   "Degree for DID:       │  (Controls   │              │
│     └──────────────┘    did:key:z6Mk..."      │   private    │              │
│                                               │   key)       │              │
│                                               └──────────────┘              │
│                                                                             │
│  2. Hacker steals the credential (but NOT the private key)                  │
│     ┌──────────────┐                                                        │
│     │    Hacker    │   Has: VC (public data)                                │
│     │  (Impostor)  │   Does NOT have: Private key to sign                   │
│     └──────────────┘                                                        │
│                                                                             │
│  3. TechCorp requires proof of control                                      │
│     ┌──────────────┐         Challenge         ┌──────────────┐             │
│     │   TechCorp   │ ────────────────────────> │    Hacker    │             │
│     │  (Employer)  │   "Sign this nonce with   │  (Cannot     │             │
│     └──────────────┘    your DID's key"        │   sign!)     │             │
│                                                └──────────────┘             │
│                                                                             │
│  4. Hacker cannot provide valid proof                                       │
│     <N> Hacker doesn't have Alice's private key                             │
│     <N> Cannot create valid signature                                       │
│     <N> VERIFICATION FAILS                                                  │
│                                                                             │
│  5. Legitimate Alice applies                                                │
│     ┌──────────────┐         Challenge         ┌──────────────┐             │
│     │   TechCorp   │ ────────────────────────> │    Alice     │             │
│     │  (Employer)  │   "Sign this nonce"       │  (Has        │             │
│     └──────────────┘                           │   private    │             │
│              │                                 │   key)       │             │
│              │         Signed Proof            └──────────────┘             │
│              │◄────────────────────────────────────────┤                    │
│              │                                         │                    │
│              │         <Y> Signature valid!            │                    │
│              │         <Y> Only Alice could sign!      │                    │
│              │                                         │                    │
│              ▼                                         │                    │
│     ┌──────────────┐                                   │                    │
│     │  <N> Alice   │◄──────────────────────────────────┘                    │
│     │  gets job!   │                                                        │
│     └──────────────┘                                                        │
│                                                                             │
│  SECURITY GUARANTEE: Only the person with the private key                   │
│  (the legitimate holder) can present the credential!                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**The Two-Layer Verification:**
| Layer | What We Verify | Why It Matters | Who We Trust |
| :--- | :--- | :--- | :--- |
| **Layer 1: Issuer** | University's signature on the credential | The degree is authentic and was really issued by the university | University as an institution |
| **Layer 2: Holder** | Alice's signature with her DID's private key | The person presenting the credential is the legitimate owner | Mathematics (cryptography) |


**Real-World Analogy:**
```
Traditional Physical Document:
┌─────────────────────────────────────────────────────────────────┐
│  University Degree Certificate                                  │
│                                                                 │
│  ┌─────────────┐                                                │
│  │  University │  Issues paper degree                           │
│  │   Seal      │  (hard to forge)                               │
│  └─────────────┘                                                │
│                                                                 │
│  Problem: Anyone who steals the paper can claim it's theirs!    │
│  No way to prove they're the legitimate holder.                 │
└─────────────────────────────────────────────────────────────────┘

DID-Based Digital Credential:
┌─────────────────────────────────────────────────────────────────┐
│  Verifiable Credential + Holder Proof                           │
│                                                                 │
│  ┌─────────────┐     ┌─────────────────────────────────────┐    │
│  │  University │     │  Alice's Digital Signature          │    │
│  │  Signature  │  +  │  (impossible to forge without       │    │
│  │  (Layer 1)  │     │   Alice's private key)              │    │
│  └─────────────┘     └─────────────────────────────────────┘    │
│                                                                 │
│  Security: Even if you steal the credential file, you cannot    │
│  prove you're the holder without Alice's private key!           │
└─────────────────────────────────────────────────────────────────┘
```

**Key Insight:**
Trusting the issuer means we believe the credential content is true, while verifying the holder means we believe the person presenting it is the real owner; both are necessary for secure identity verification.

**Simple Summary of Responsibilities:**
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Who Guarantees What?                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  UNIVERSITY (Issuer)                    ALICE (Holder)                      │
│  ───────────────────                    ───────────────                     │
│                                                                             │
│  <X> "This degree is real"               <X> "I am the real Alice"          │
│  <X> "Alice completed CS degree"         <X> "I own this credential"        │
│  <X> "Graduated 2023"                    <X> "I have the private key"       │
│                                                                             │
│  ↓                                      ↓                                   │
│  Signs VC with university's key         Signs presentation with Alice's key │
│  ↓                                      ↓                                   │
│  Verifier checks:                       Verifier checks:                    │
│  "Is this really from University?"      "Is this really Alice?"             │
│                                                                             │
│  ╔═══════════════════════════════════════════════════════════════════════╗  │
│  ║                                                                       ║  │
│  ║   University ensures:    THE CREDENTIAL IS TRUE                       ║  │
│  ║                                                                       ║  │
│  ║   Alice proves:          SHE IS THE ONE THE DEGREE BELONGS TO         ║  │
│  ║                                                                       ║  │
│  ╚═══════════════════════════════════════════════════════════════════════╝  │
│                                                                             │
│  Without Alice's proof:                                                     │
│  <N> Anyone with a copy of the credential could claim to be Alice           │
│                                                                             │
│  With Alice's proof:                                                        │
│  <X> Only the person with the private key (Alice) can use the credential    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**One-Sentence Summary:**
The university **attests** that Alice has a degree, but Alice must **prove** she is the person that degree was issued to.

###### How Alice's Signature Proves Her Identity (Cryptographic Proof)
When Alice presents her credentials to the employer, she signs a **Verifiable Presentation** with her private key. The verifier then uses Alice's public key (from her DID document) to verify the signature.

**The Math Behind It:**
```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  Alice's Private Key ───Sign───> Signature ───> VP.proof.proofValue │
│          │                                                          │
│          │ (mathematically linked)                                  │
│          ▼                                                          │
│   Alice's Public Key ───Verify──> ✅ Valid / ❌ Invalid            │
│       (in DID doc)                                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Why This Proves Alice's Identity:**
| What Alice Has | What Verifier Has | Mathematical Property |
| :--- | :--- | :--- |
| Private key (secret) | Public key (from DID document) | Only private key can create signatures that public key can verify |

The verifier knows:
- Alice's DID is `did:key:z6MkhaXg...`
- Her DID document contains her public key
- **Anyone** can get that public key (it's public)
- **Only Alice** has the corresponding private key

**Verification Flow:**
```
Employer (Verifier)                     Alice's DID Document
─────────────────────────────────────────────────────────────────
1. Receive VP from Alice
   │
   ▼
2. Extract holder DID from VP
   "holder": "did:key:z6MkhaXg..."
   │
   ▼
3. Resolve Alice's DID ───────────────▶ did:key:z6MkhaXg...
                                       │
                                       ▼
4. Get Alice's public key ◀─────────── {
                                         verificationMethod: [{
                                           publicKeyMultibase: "z6Mk..."
                                         }]
                                       }
   │
   ▼
5. Verify signature
   VP.proof.proofValue + Alice's public key
   │
   ▼
   ✅ Signature valid = Only Alice could have signed!
```

**What This Prevents (Stolen Credential Attack):**
```
Without Alice's signature:
┌──────────────┐      Stolen VC        ┌──────────────┐
│   Hacker     │ ────────────────────> │   Employer   │
│  (Has VC)    │   "Here's my degree"  │              │
└──────────────┘                       └──────────────┘
       ✅ Employer: "University signature valid!"
       ❌ BUT: Is this really Alice?

With Alice's signature:
┌──────────────┐      VP (signed)      ┌──────────────┐
│   Hacker     │ ────────────────────> │   Employer   │
│  (No private │   "Here's my degree"  │              │
│      key)    │                       │              │
└──────────────┘                       └──────────────┘
       ❌ Employer: "Sign this challenge"
       ❌ Hacker: Can't! (no private key)
       ❌ VERIFICATION FAILS
```

**Key insight:** The VC proves *"University says Alice has a degree"*, but Alice's signature on the VP proves *"I am the real Alice presenting this credential."*

###### The Challenge (Nonce): Preventing Replay Attacks
The **challenge (nonce)** in the presentation proof prevents replay attacks:
**Replay Attack Without Challenge:**
```
┌──────────────┐                    ┌──────────────┐
│   Hacker     │                    │   Employer   │
└──────┬───────┘                    └──────┬───────┘
       │                                   │
       │ 1. Record Alice's VP + signature  │
       │<──────────────────────────────────│ (legitimate session)
       │                                   │
       │ 2. Wait...                        │
       │                                   │
       │ 3. Replay same VP to Employer #2  │
       │──────────────────────────────────>│ (new session)
       │   "Here's my degree"              │
       │                                   │
       │                    ❌ EMPLOYER #2 ACCEPTS!
       │                    (same signature is valid)
```

**With Challenge (Nonce) - Replay Prevented:**
```
SESSION 1 (Legitimate):
┌──────────────┐                    ┌──────────────┐
│    Alice     │                    │   Employer   │
└──────┬───────┘                    └──────┬───────┘
       │                                   │
       │<──── "Challenge: abc123"          │
       │                                   │
       │────> "Signed: abc123"             │
       │     (signature over "abc123")     │
       │                                   │
       │<──── " Verified!"                 │
       │                                   │

SESSION 2 (Hacker Replay):
┌──────────────┐                    ┌──────────────┐
│   Hacker     │                    │  Employer #2 │
└──────┬───────┘                    └──────┬───────┘
       │                                   │
       │<──── "Challenge: xyz789"          │  ← DIFFERENT nonce!
       │                                   │
       │────> "Signed: abc123" (replay)    │
       │     (signature over OLD "abc123") │
       │                                   │
       │<──── " INVALID! Signature         │
       │      doesn't match challenge"     │
```

**Why It Works:**
| Mechanism | Purpose |
| :--- | :--- |
| **Challenge = Random nonce** | Unique per session |
| **Alice signs the challenge** | Proves she's participating NOW |
| **Verifier checks signed challenge** | Ensures signature is fresh |

The challenge binds the signature to **this specific session**, making replayed signatures invalid for any other session.

###### DID vs Traditional Digital Certificate: Proving Ownership
```
┌─────────────────────────────────────────────────────────────────────────────┐
│         Traditional Digital Certificate (e.g., DigiCert) vs DID             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  TRADITIONAL DIGITAL CERTIFICATE                                            │
│  ─────────────────────────────────                                          │
│                                                                             │
│  ┌──────────────┐          Issue Cert          ┌──────────────┐             │
│  │     CA       │ ────────────────────────────>│    Alice     │             │
│  │  (DigiCert)  │   "Certificate for alice@    │              │             │
│  └──────────────┘    example.com"              └──────────────┘             │
│                                                        │                    │
│                                                        ▼                    │
│                                               ┌──────────────┐              │
│                                               │  Alice wants │              │
│                                               │  to use cert │              │
│                                               └──────────────┘              │
│                                                        │                    │
│  To prove ownership:                                   ▼                    │
│  <N> Certificate alone doesn't prove Alice's identity                       │
│                                                                             │
│  ┌──────────────┐         Show Physical ID      ┌──────────────┐            │
│  │   Verifier   │ <──────────────────────────── │    Alice     │            │
│  │              │   "Show me your passport/     │              │            │
│  │              │    driver's license"          │   Passport   │            │
│  └──────────────┘                               └──────────────┘            │
│                                                                             │
│  Problems:                                                                  │
│  • Physical ID can be forged                                                │
│  • Manual verification process                                              │
│  • Privacy concerns (showing unnecessary info)                              │
│  • No cryptographic binding between cert and identity                       │
│                                                                             │
│  ═══════════════════════════════════════════════════════════════════════    │
│                                                                             │
│  DID-BASED VERIFIABLE CREDENTIAL                                            │
│  ─────────────────────────────────                                          │
│                                                                             │
│  ┌──────────────┐          Issue VC            ┌──────────────┐             │
│  │  University  │ ────────────────────────────>│    Alice     │             │
│  │  (Issuer)    │   "Degree for DID:           │  (Controls   │             │
│  └──────────────┘    did:key:z6Mk..."          │   private    │             │
│                                                │   key)       │             │
│                                                └──────────────┘             │
│                                                        │                    │
│                                                        ▼                    │
│                                               ┌──────────────┐              │
│                                               │  Alice wants │              │
│                                               │  to use VC   │              │
│                                               └──────────────┘              │
│                                                        │                    │
│  To prove ownership:                                   ▼                    │
│  <X> Cryptographic proof - NO physical ID needed!                           │
│                                                                             │
│  ┌──────────────┐         Digital Challenge     ┌──────────────┐            │
│  │   Verifier   │ ────────────────────────────> │    Alice     │            │
│  │              │   "Sign this with your DID"   │              │            │
│  └──────────────┘                               └──────────────┘            │
│         │                                              │                    │
│         │         Return Signed Proof                  │                    │
│         │<─────────────────────────────────────────────┘                    │
│         │                                                                   │
│         ▼                                                                   │
│  ┌──────────────┐                                                           │
│  │  Verify with │   "Signature valid!"                                      │
│  │  Alice's DID │   "Only holder of private key could sign"                 │
│  │  public key  │   " Alice proven as legitimate holder"                    │
│  └──────────────┘                                                           │
│                                                                             │
│  Advantages:                                                                │
│  • Cryptographically impossible to forge                                    │
│  • Automatic verification (no manual checking)                              │
│  • Privacy-preserving (selective disclosure)                                │
│  • Strong cryptographic binding between VC and identity                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Key Difference:**
| Aspect | Traditional Certificate | DID-Based VC |
| :--- | :--- | :--- |
| **Proof of ownership** | Physical ID (passport, driver's license) | Cryptographic signature |
| **Verification method** | Manual visual inspection | Automatic cryptographic verification |
| **Security** | Can be forged | Mathematically impossible to forge |
| **Privacy** | Reveals all ID info | Minimal disclosure (only what's needed) |
| **Binding strength** | Weak (separate systems) | Strong (cryptographically linked) |
| **Convenience** | In-person or scanned documents | Digital, instant, remote |


**Your Understanding is Correct:**
For DigiCert, a CA issues a cert and Alice shows physical ID to prove ownership; for a DID, a University issues a VC and Alice uses a DID signature to prove ownership. The DID replaces the need for physical ID verification with cryptographic proof!

###### How Alice Chooses Which Credentials to Share (Selective Disclosure)
One of the powerful privacy features of DID/VC is that Alice can choose exactly which credentials to share and which to keep private.

**Scenario: Alice has 7 credentials but only wants to show 3**
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Alice's Wallet: 7 Credentials                           │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  ALICE'S DIGITAL WALLET                                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │   CREDENTIAL #1: University Degree (Computer Science)               │   │
│  │     Issuer: MIT (did:web:mit.edu)                                   │   │
│  │     Status: <Y> Available                                           │   │
│  │                                                                     │   │
│  │   CREDENTIAL #2: Master's Degree (Data Science)                     │   │
│  │     Issuer: Stanford (did:web:stanford.edu)                         │   │
│  │     Status: <Y> Available                                           │   │
│  │                                                                     │   │
│  │   CREDENTIAL #3: Employment Certificate (Google)                    │   │
│  │     Issuer: Google HR (did:web:google.com)                          │   │
│  │     Status: <Y> Available                                           │   │
│  │                                                                     │   │
│  │   CREDENTIAL #4: AWS Certification                                  │   │
│  │     Issuer: Amazon Web Services (did:web:aws.amazon.com)            │   │
│  │     Status: <Y> Available                                           │   │
│  │                                                                     │   │
│  │   CREDENTIAL #5: Conference Speaker Badge (Blockchain Expo)         │   │
│  │     Issuer: Expo Org (did:web:blockchainexpo.com)                   │   │
│  │     Status: <Y> Available                                           │   │
│  │                                                                     │   │
│  │   CREDENTIAL #6: Health Insurance Card                              │   │
│  │     Issuer: BlueCross (did:web:bluecross.com)                       │   │
│  │     Status: <N> Private (not sharing)                               │   │
│  │                                                                     │   │
│  │   CREDENTIAL #7: Government ID (Passport)                           │   │
│  │     Issuer: Gov Agency (did:web:gov.agency)                         │   │
│  │     Status: <N> Private (not sharing)                               │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

**Step 1: Employer Sends Request**
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Step 1: Employer Requests Specific Credentials          │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  TechCorp sends a "Credential Request" to Alice's wallet:                  │
│                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  CREDENTIAL REQUEST                                                 │   │
│  │  From: TechCorp (did:web:techcorp.com)                              │   │
│  │                                                                     │   │
│  │  "Please provide credentials that prove:"                           │   │
│  │                                                                     │   │
│  │  <X> 1. You have a degree in Computer Science or related field      │   │
│  │  <X> 2. You have at least 2 years of work experience                │   │
│  │  < > 3. AWS certification (optional, preferred)                     │   │
│  │                                                                     │   │
│  │  We do NOT need:                                                    │   │
│  │  < > Your Master's degree details                                   │   │
│  │  < > Your health insurance information                              │   │
│  │  < > Your government ID                                             │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

**Step 2: Alice Reviews and Selects**
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Step 2: Alice Chooses What to Share                     │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  Alice's wallet shows a selection screen:                                  │
│                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  SELECT CREDENTIALS TO SHARE                                        │   │
│  │  Request from: TechCorp                                             │   │
│  │                                                                     │   │
│  │  SELECTED (Will Share):                                             │   │
│  │  <X> [Credential #1] MIT Degree - Computer Science                  │   │
│  │      └─> Meets requirement: "Degree in CS or related field"         │   │
│  │                                                                     │   │
│  │  <X> [Credential #3] Google Employment Certificate                  │   │
│  │      └─> Meets requirement: "2+ years work experience"              │   │
│  │                                                                     │   │
│  │  <X> [Credential #4] AWS Certification                              │   │
│  │      └─> Optional but strengthens application                       │   │
│  │                                                                     │   │
│  │  ─────────────────────────────────────────────────────────────────  │   │
│  │                                                                     │   │
│  │  NOT SELECTED (Will NOT Share):                                     │   │
│  │  < > [Credential #2] Stanford Master's Degree                       │   │
│  │      └─> Not needed (already showing MIT degree)                    │   │
│  │                                                                     │   │
│  │  < > [Credential #5] Conference Speaker Badge                       │   │
│  │      └─> Not relevant to job requirements                           │   │
│  │                                                                     │   │
│  │  PRIVATE (Never Share):                                             │   │
│  │  < > [Credential #6] Health Insurance                               │   │
│  │      └─> Sensitive health data - not sharing                        │   │
│  │                                                                     │   │
│  │  < > [Credential #7] Government ID/Passport                         │   │
│  │      └─> Too much personal info - not needed                        │   │
│  │                                                                     │   │
│  │  ─────────────────────────────────────────────────────────────────  │   │
│  │                                                                     │   │
│  │  [ <Y> Confirm: Share 3 Selected Credentials ]                      │   │
│  │  [ <N> Cancel: Don't share anything ]                               │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                            │
│  Alice's choice: Share credentials #1, #3, and #4                          │
│  Keep private: credentials #2, #5, #6, #7                                  │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

**Step 3: Alice Creates Verifiable Presentation**
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Step 3: Create Verifiable Presentation                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Alice's wallet creates a presentation containing ONLY the 3 selected VCs:  │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  VERIFIABLE PRESENTATION                                            │    │
│  │  Holder: did:key:z6MkhaXg... (Alice's DID)                          │    │
│  │                                                                     │    │
│  │  ┌───────────────────────────────────────────────────────────────┐  │    │
│  │  │ CREDENTIAL 1: MIT Degree (Computer Science)                   │  │    │
│  │  │ - Included <Y>                                                │  │    │
│  │  └───────────────────────────────────────────────────────────────┘  │    │
│  │                                                                     │    │
│  │  ┌───────────────────────────────────────────────────────────────┐  │    │
│  │  │ CREDENTIAL 2: Google Employment (3 years)                     │  │    │
│  │  │ - Included <Y>                                                │  │    │
│  │  └───────────────────────────────────────────────────────────────┘  │    │
│  │                                                                     │    │
│  │  ┌───────────────────────────────────────────────────────────────┐  │    │
│  │  │ CREDENTIAL 3: AWS Certification                               │  │    │
│  │  │ - Included <Y>                                                │  │    │
│  │  └───────────────────────────────────────────────────────────────┘  │    │
│  │                                                                     │    │
│  │  ┌───────────────────────────────────────────────────────────────┐  │    │
│  │  │ CREDENTIALS 4-7: NOT INCLUDED                                 │  │    │
│  │  │ - Stanford Master's <X>                                       │  │    │
│  │  │ - Conference Badge <X>                                        │  │    │
│  │  │ - Health Insurance <X>                                        │  │    │
│  │  │ - Government ID <X>                                           │  │    │
│  │  └───────────────────────────────────────────────────────────────┘  │    │
│  │                                                                     │    │
│  │  PROOF: Alice's digital signature (proves she controls the DID)     │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Result: Only 3 credentials shared, 4 remain private                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Technical Implementation:**
```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": "VerifiablePresentation",
  "holder": "did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK",
  
  "verifiableCredential": [
    // ONLY the 3 selected credentials included
    { "id": "urn:uuid:mit-degree-001", ... },
    { "id": "urn:uuid:google-employment-002", ... },
    { "id": "urn:uuid:aws-cert-003", ... }
    // Credentials 4-7 are NOT here - Alice chose not to share them
  ],
  
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2024-01-15T10:30:00Z",
    "proofPurpose": "authentication",
    "verificationMethod": "did:key:z6MkhaXg...#z6MkhaXg...",
    "proofValue": "z7sN8K..."
  }
}
```

**Key Points:**
| Feature | How It Works | Benefit |
| :--- | :--- | :--- |
| **Holder Control** | Alice decides what to share | Privacy protection |
| **Selective Disclosure** | Choose specific credentials from wallet | Share only what's needed |
| **No Revealing** | Unselected credentials never leave wallet | Complete privacy for unshared data |
| **Contextual Sharing** | Different presentations for different situations | Right data for right context |

**Real-World Example:**
```
Job Application to TechCorp:
- Share: MIT Degree, Google Experience, AWS Cert
- Don't share: Health Insurance, Government ID

Doctor Visit to Hospital:
- Share: Health Insurance, Government ID
- Don't share: Employment history, AWS Cert

Bank Loan Application:
- Share: Government ID, Employment, Degree
- Don't share: Health Insurance, Conference badges
```

**Step 4: Verifier Extracts and Verifies the VCs**
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Step 4: Verifier Processes the Presentation             │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  TechCorp receives the Verifiable Presentation from Alice:                 │
│                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  RECEIVED: VerifiablePresentation.json                              │   │
│  │                                                                     │   │
│  │  TechCorp's Verification System starts processing...                │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                       │
│                                    ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STEP 4.1: Extract VCs from Presentation                            │   │
│  │  ─────────────────────────────────────                              │   │
│  │                                                                     │   │
│  │  Presentation.verifiableCredential[0] ────> MIT Degree VC           │   │
│  │  Presentation.verifiableCredential[1] ────> Google Employment VC    │   │
│  │  Presentation.verifiableCredential[2] ────> AWS Certification VC    │   │
│  │                                                                     │   │
│  │  <X> Extracted 3 VCs from presentation                              │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                       │
│                                    ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STEP 4.2: Verify Holder Binding (Alice's Identity)                 │   │
│  │  ─────────────────────────────────────────────────                  │   │
│  │                                                                     │   │
│  │  Check: Presentation.proof                                          │   │
│  │    ↓                                                                │   │
│  │  Extract verificationMethod: did:key:z6MkhaXg...#z6MkhaXg...        │   │
│  │    ↓                                                                │   │
│  │  Resolve Alice's DID ────> Get Alice's public key                   │   │
│  │    ↓                                                                │   │
│  │  Verify signature on presentation                                   │   │
│  │    ↓                                                                │   │
│  │ <X> VALID - Only Alice could have signed this presentation          │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                       │
│                                    ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STEP 4.3: Verify Each VC (Issuer Authenticity)                     │   │
│  │  ─────────────────────────────────────────────                      │   │
│  │                                                                     │   │
│  │  FOR EACH extracted VC:                                             │   │
│  │                                                                     │   │
│  │  VC #1: MIT Degree                                                  │   │
│  │    ├─ Extract issuer: did:web:mit.edu                               │   │
│  │    ├─ Resolve MIT's DID ────> Get MIT's public key                  │   │
│  │    ├─ Verify VC signature with MIT's key                            │   │
│  │    ├─ Check revocation status                                       │   │
│  │    └─ <X> VALID - MIT really issued this degree                     │   │
│  │                                                                     │   │
│  │  VC #2: Google Employment                                           │   │
│  │    ├─ Extract issuer: did:web:google.com                            │   │
│  │    ├─ Resolve Google's DID ────> Get Google's public key            │   │
│  │    ├─ Verify VC signature with Google's key                         │   │
│  │    ├─ Check revocation status                                       │   │
│  │    └─ <X> VALID - Google really issued this certificate             │   │
│  │                                                                     │   │
│  │  VC #3: AWS Certification                                           │   │
│  │    ├─ Extract issuer: did:web:aws.amazon.com                        │   │
│  │    ├─ Resolve AWS's DID ────> Get AWS's public key                  │   │
│  │    ├─ Verify VC signature with AWS's key                            │   │
│  │    ├─ Check revocation status                                       │   │
│  │    └─ <X> VALID - AWS really issued this certification              │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                       │
│                                    ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STEP 4.4: Verify Subject Binding (VCs belong to Alice)             │   │
│  │  ─────────────────────────────────────────────────────              │   │
│  │                                                                     │   │
│  │  FOR EACH VC:                                                       │   │
│  │    Check: VC.credentialSubject.id == Alice's DID?                   │   │
│  │                                                                     │   │
│  │    MIT Degree.subject.id: did:key:z6MkhaXg... == Alice's DID? <X>   │   │
│  │    Google Cert.subject.id: did:key:z6MkhaXg... == Alice's DID? <X>  │   │
│  │    AWS Cert.subject.id: did:key:z6MkhaXg... == Alice's DID? <X>     │   │
│  │                                                                     │   │
│  │    <X> All VCs were issued TO Alice (not to someone else)           │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                       │
│                                    ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │  ╔═════════════════════════════════════════════════════════════════╗│   │
│  │  ║  <X> ALL VERIFICATIONS PASSED                                   ║│   │
│  │  ║                                                                 ║│   │
│  │  ║  1. Presentation signed by Alice <X>                            ║│   │
│  │  ║  2. MIT degree is authentic <X>                                 ║│   │
│  │  ║  3. Google employment is authentic <X>                          ║│   │
│  │  ║  4. AWS certification is authentic <X>                          ║│   │
│  │  ║  5. All VCs belong to Alice <X>                                 ║│   │
│  │  ║  6. No credentials revoked <X>                                  ║│   │
│  │  ║                                                                 ║│   │
│  │  ║  RESULT: Alice is legitimate!                                   ║│   │
│  │  ╚═════════════════════════════════════════════════════════════════╝│   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

**Verification Summary Table:**
| Step | What Verifier Does | What It Proves |
| :--- | :--- | :--- |
| **Extract VCs** | Parse verifiableCredential array from presentation | Gets the 3 credentials Alice shared |
| **Verify Holder** | Check presentation signature with Alice's public key | Alice is the one presenting (not stolen) |
| **Verify Issuer #1** | Check MIT's signature on degree VC | MIT really issued this degree |
| **Verify Issuer #2** | Check Google's signature on employment VC | Google really issued this certificate |
| **Verify Issuer #3** | Check AWS's signature on certification VC | AWS really issued this certification |
| **Verify Subject** | Check credentialSubject.id matches Alice's DID | All VCs belong to Alice (not someone else) |


**Answer to Your Question:**
> **Yes!** The verifier:
> 1. **Extracts** the 3 VCs from `presentation.verifiableCredential` array
> 2. **Verifies** Alice's signature on the presentation (holder proof)
> 3. **Verifies** each VC's issuer signature (MIT, Google, AWS)
> 4. **Verifies** all VCs belong to Alice (subject binding)

**One-Sentence Summary:**
The Verifiable Presentation is like an envelope containing 3 VCs; the verifier opens the envelope, checks Alice signed the envelope, and checks each VC is authentic.

---

### 2. How DID/DID Document and VC Are Generated and Issued
###### Part 1: DID and DID Document Generation
**Who Generates the DID/DID Document?**
The answer depends on the DID method:
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Who Generates DID/DID Document?                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  METHOD 1: Self-Generated (did:key)                                         │
│  ──────────────────────────────────                                         │
│                                                                             │
│  Alice (User) generates everything herself:                                 │
│                                                                             │
│  Alice's Device                                                             │
│  ┌─────────────────────────────────────────┐                                │
│  │  1. Generate Key Pair                   │                                │
│  │     - Private Key (kept secret)         │                                │
│  │     - Public Key                        │                                │
│  │                                         │                                │
│  │  2. Construct DID from Public Key       │                                │
│  │     did:key:z6Mk...                     │                                │
│  │                                         │                                │
│  │  3. Generate DID Document               │                                │
│  │     (deterministic algorithm)           │                                │
│  │                                         │                                │
│  │  <N> NO external service provider       │                                │
│  │  <N> NO registration needed             │                                │
│  │  <X> Alice has complete control         │                                │
│  └─────────────────────────────────────────┘                                │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  METHOD 2: Domain-Based (did:web)                                           │
│  ─────────────────────────────────                                          │
│                                                                             │
│  Organization generates and hosts DID document:                             │
│                                                                             │
│  Organization's IT Team                                                     │
│  ┌─────────────────────────────────────────┐                                │
│  │  1. Generate Key Pair                   │                                │
│  │                                         │                                │
│  │  2. Create DID from domain              │                                │
│  │     did:web:example.com                 │                                │
│  │                                         │                                │
│  │  3. Create DID Document                 │                                │
│  │                                         │                                │
│  │  4. Host on web server                  │                                │
│  │     /.well-known/did.json               │                                │
│  │                                         │                                │
│  │  <X> Organization controls              │                                │
│  │  <X> DNS/HTTPS provides authority       │                                │
│  └─────────────────────────────────────────┘                                │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  METHOD 3: Blockchain-Based (did:ethr, did:ion)                             │
│  ──────────────────────────────────────────────                             │
│                                                                             │
│  User generates keys, but DID document is resolved from blockchain:         │
│                                                                             │
│  Alice (User)                    Blockchain Network                         │
│  ┌─────────────────────┐         ┌──────────────────────────────────────┐   │
│  │ 1. Generate Key Pair│         │                                      │   │
│  │                     │         │  Smart Contract (ERC-1056)           │   │
│  │ 2. Create DID from  │         │  ┌────────────────────────────────┐  │   │
│  │    Ethereum Address │         │  │ DID Document stored/resolved   │  │   │
│  │                     │────────>│  │ from blockchain events         │  │   │
│  │ 3. Sign transactions│         │  └────────────────────────────────┘  │   │
│  │    (if updating)    │         │                                      │   │
│  └─────────────────────┘         └──────────────────────────────────────┘   │
│                                                                             │
│  <X> Alice controls keys (self-sovereign)                                   │
│  <X> Blockchain provides decentralized registry                             │
│  <X> No single service provider controls the DID                            │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  METHOD 4: Service Provider Assisted (BSN-DID, etc.)                        │
│  ───────────────────────────────────────────────────                        │
│                                                                             │
│  In some enterprise/government scenarios:                                   │
│                                                                             │
│  Alice + BSN Service Provider                                               │
│  ┌─────────────────────────────────────────┐                                │
│  │  1. Alice requests DID creation         │                                │
│  │     (provides identity proof)           │                                │
│  │                                         │                                │
│  │  2. Service Provider verifies identity  │                                │
│  │     (e.g., real-name authentication)    │                                │
│  │                                         │                                │
│  │  3. Service Provider generates DID      │                                │
│  │     and DID Document                    │                                │
│  │                                         │                                │
│  │  4. Alice receives private keys         │                                │
│  │     (or key is secured for Alice)       │                                │
│  │                                         │                                │
│  │  <X> Alice gets verified DID            │                                │
│  │  <X> Service provider guarantees        │                                │
│  │     real-world identity                 │                                │
│  └─────────────────────────────────────────┘                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Summary Table: Who Does What**
| DID Method | Key Generation | DID Creation | DID Document Creation | Storage/Registry | Service Provider Role |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **did:key** | User | User (algorithmic) | User (deterministic) | None (self-contained) | None - fully self-sovereign |
| **did:web** | Organization | Organization | Organization | Web server (DNS-based) | Organization IT hosts document |
| **did:ethr** | User | User (from address) | Smart contract / Default | Ethereum blockchain | Blockchain network provides registry |
| **did:ion** | User | User (from public key) | Sidetree nodes aggregate | Bitcoin blockchain | ION network nodes help batch/anchor |
| **BSN-DID** | Service Provider | Service Provider | Service Provider | BSN blockchain | BSN provides verified, real-name DID |


**Key Insight:**
- **Self-Sovereign Methods (did:key, did:ethr, did:ion)**: User generates everything. No service provider needed. True "self-sovereign identity."
- **Organization Methods (did:web)**: Organization generates and controls. Good for corporate/institutional identities.
- **Assisted Methods (BSN-DID)**: Service provider helps generate, but user still controls keys (in most designs). Bridges real-world identity with decentralized identity.

###### Method A: did:key (Self-Generated)
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    did:key Generation Process (Self-Generated)              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: Generate Cryptographic Key Pair                                    │
│  ─────────────────────────────────────                                      │
│                                                                             │
│  Alice's Wallet App:                                                        │
│  ┌─────────────────────────────────────────┐                                │
│  │  Generate Ed25519 Key Pair              │                                │
│  │                                         │                                │
│  │  Private Key (sk) ──────> Keep Secret   │                                │
│  │     ↓                                   │                                │
│  │  Public Key (pk) ───────> Used for DID  │                                │
│  └─────────────────────────────────────────┘                                │
│                                                                             │
│  STEP 2: Encode Public Key                                                  │
│  ─────────────────────────                                                  │
│                                                                             │
│  Raw Public Key: 0x279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959...   │
│       ↓                                                                     │
│  Multicodec Prefix: 0xed (for Ed25519)                                      │
│       ↓                                                                     │
│  Multibase Base58-btc Encoding: z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnn    │
│                                                                             │
│  STEP 3: Construct DID                                                      │
│  ────────────────────                                                       │
│                                                                             │
│  did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK                   │
│  │      │                                               │                   │
│  │      │                                               └── Method-Specific │
│  │      │                                                    ID (encoded pk)│
│  │      └── Method Name                                      │              │
│  └── Scheme                                                  │              │
│                                                                             │
│  STEP 4: Generate DID Document (Deterministic)                              │
│  ─────────────────────────────────────────────                              │
│                                                                             │
│  The DID document is automatically generated from the public key:           │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  {                                                                  │    │
│  │    "@context": ["https://www.w3.org/ns/did/v1"],                    │    │
│  │    "id": "did:key:z6MkhaXg...",                                     │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "id": "did:key:z6MkhaXg...#z6MkhaXg...",                       │    │
│  │      "type": "Ed25519VerificationKey2020",                          │    │
│  │      "controller": "did:key:z6MkhaXg...",                           │    │
│  │      "publicKeyMultibase": "z6MkhaXg..."                            │    │
│  │    }],                                                              │    │
│  │    "authentication": ["#z6MkhaXg..."]                               │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  STEP 5: Store Private Key                                                  │
│  ───────────────────────                                                    │
│                                                                             │
│  <N> NO REGISTRY NEEDED for did:key                                         │
│  <X> Private key stored securely in Alice's wallet                          │
│  <X> DID document can be regenerated anytime from the public key            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### Method B: did:web (Domain-Based)
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    did:web Generation Process (Domain-Based)                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: Own a Domain                                                       │
│  ────────────────────                                                       │
│  Alice's organization owns: example.com                                     │
│                                                                             │
│  STEP 2: Generate Key Pair                                                  │
│  ───────────────────────                                                    │
│  Generate cryptographic keys (same as did:key)                              │
│                                                                             │
│  STEP 3: Create DID                                                         │
│  ────────────────                                                           │
│                                                                             │
│  did:web:example.com                                                        │
│  │      │           │                                                       │
│  │      │           └── Domain name (must own DNS)                          │
│  │      └── Method Name                                                     │
│  └── Scheme                                                                 │
│                                                                             │
│  STEP 4: Create and Host DID Document                                       │
│  ───────────────────────────────────                                        │
│                                                                             │
│  Create did.json file:                                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  {                                                                  │    │
│  │    "@context": ["https://www.w3.org/ns/did/v1"],                    │    │
│  │    "id": "did:web:example.com",                                     │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "id": "did:web:example.com#key-1",                             │    │
│  │      "type": "JsonWebKey2020",                                      │    │
│  │      "controller": "did:web:example.com",                           │    │
│  │      "publicKeyJwk": { "kty": "OKP", "crv": "Ed25519", ... }        │    │
│  │    }],                                                              │    │
│  │    "authentication": ["did:web:example.com#key-1"]                  │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Host at: https://example.com/.well-known/did.json                          │
│                                                                             │
│  STEP 5: DNS Resolution                                                     │
│  ───────────────────                                                        │
│                                                                             │
│  When someone resolves did:web:example.com:                                 │
│  1. Extract domain: example.com                                             │
│  2. DNS lookup → resolves to web server IP                                  │
│  3. HTTPS GET: https://example.com/.well-known/did.json                     │
│  4. Return DID document                                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### Method C: did:ethr (Blockchain-Based)
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 did:ethr Generation Process (Ethereum-Based)                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: Generate Ethereum Key Pair                                         │
│  ─────────────────────────────────                                          │
│                                                                             │
│  Private Key ────> Public Key (64 hex chars) ────> Ethereum Address (40)    │
│       │                                               │                     │
│       │                                               ▼                     │
│       │                                        0xb9c5714089478a327f...      │
│       │                                               │                     │
│       └───────────────────────────────────────────────┘                     │
│                    Used to control the DID                                  │
│                                                                             │
│  STEP 2: Construct DID                                                      │
│  ────────────────────                                                       │
│                                                                             │
│  did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a                        │
│  │      │  │                                          │                     │
│  │      │  │                                          └── Ethereum Address  │
│  │      │  └── Network identifier (optional, defaults to mainnet)           │
│  │      └── Method Name                                                     │
│  └── Scheme                                                                 │
│                                                                             │
│  STEP 3: Default DID Document (No Registration Required)                    │
│  ───────────────────────────────────────────────────────                    │
│                                                                             │
│  Even without blockchain transaction, a default DID document exists:        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  {                                                                  │    │
│  │    "id": "did:ethr:0xb9c5...",                                      │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "id": "did:ethr:0xb9c5...#controller",                         │    │
│  │      "type": "EcdsaSecp256k1RecoveryMethod2020",                    │    │
│  │      "controller": "did:ethr:0xb9c5...",                            │    │
│  │      "blockchainAccountId": "eip155:1:0xb9c5..."                    │    │
│  │    }],                                                              │    │
│  │    "authentication": ["#controller"]                                │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  STEP 4: Optional - Register on ERC-1056 Smart Contract                     │
│  ───────────────────────────────────────────────────────                    │
│                                                                             │
│  To add services or delegate keys, submit Ethereum transaction:             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Transaction to: 0xdca7ef03e98e0dc2b855be647c39abe984fcf21b         │    │
│  │  Function: changeOwnerDelegated() or setAttribute()                 │    │
│  │  Cost: Gas fees (ETH)                                               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  STEP 5: Resolution                                                         │
│  ────────────                                                               │
│                                                                             │
│  When resolving did:ethr:0xb9c5...:                                         │
│  1. Query ERC-1056 smart contract on Ethereum                               │
│  2. Read events: DIDOwnerChanged, DIDDelegateChanged, DIDAttributeChanged   │
│  3. Build DID document from event history                                   │
│  4. Return complete DID document                                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### Part 2: Verifiable Credential Generation and Issuance
```
┌─────────────────────────────────────────────────────────────────────────────┐
│              Verifiable Credential Generation and Issuance Process          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ACTORS:                                                                    │
│  ┌──────────────┐         ┌──────────────┐         ┌──────────────┐         │
│  │   University │         │    Alice     │         │   Employer   │         │
│  │   (Issuer)   │         │   (Holder)   │         │  (Verifier)  │         │
│  └──────┬───────┘         └──────┬───────┘         └──────┬───────┘         │
│         │                        │                        │                 │
└─────────┼────────────────────────┼────────────────────────┼─────────────────┘
          │                        │                        │
          ▼                        │                        │
┌─────────────────────┐            │                        │
│ STEP 1: University  │            │                        │
│ Prepares Credential │            │                        │
│ ─────────────────── │            │                        │
│                     │            │                        │
│  {                  │            │                        │
│    "@context": [    │            │                        │
│      "https://www.w3.org/2018/credentials/v1",            │
│      "https://www.w3.org/2018/credentials/examples/v1"    │
│    ],               │            │                        │
│    "id": "urn:uuid:12345678-1234-1234-1234-123456789abc"  │
│    "type": ["VerifiableCredential", "DegreeCredential"],  │
│    "issuer": {      │            │                        │
│      "id": "did:web:uni.edu",  ◄── University's DID       │
│      "name": "Example University"                         │
│    },               │            │                        │
│    "issuanceDate": "2023-05-15T10:00:00Z",                │
│    "credentialSubject": {        ◄── Claims about Alice   │
│      "id": "did:key:z6MkhaXg...", ◄── Alice's DID         │
│      "degree": {                                          │
│        "type": "BachelorDegree",                          │
│        "name": "Bachelor of Science",                     │
│        "field": "Computer Science"                        │
│      },                                                   │
│      "graduationDate": "2023-05-15"                       │
│    }                                                      │
│  }                                                        │
└─────────┬─────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────┐
│ STEP 2: University  │
│ Signs Credential    │
│ ─────────────────   │
│                     │
│  Canonicalization:  │
│  Normalize JSON-LD  │
│       ↓             │
│  Hash (SHA-256):    │
│  0x8a3f2b1c...      │
│       ↓             │
│  Sign with          │
│  University's       │
│  Private Key        │
│       ↓             │
│  Signature:         │
│  z58DAdF7HV...      │
└─────────┬───────────┘
          │
          ▼
┌────────────────────────────────────────────────────────┐
│ STEP 3: Complete                                       │
│ Signed Credential                                      │
│ ─────────────────                                      │
│                                                        │
│  {                                                     │
│    ...credential data...                               │
│    "proof": {                                          │
│      "type": "Ed25519Signature2020",                   │
│      "created": "2023-05-15T10:00:00Z",                │
│      "proofPurpose": "assertionMethod",                │
│      "verificationMethod": "did:web:uni.edu#key-1",    │
│      "proofValue": "z58DAdF7HV..."  ◄── Signature      │
│    }                                                   │
│  }                                                     │
└─────────┬──────────────────────────────────────────────┘
          │
          │ Issue to Alice
          ▼
┌─────────────────────┐            ┌─────────────────────┐
│ STEP 4: Alice       │            │ STEP 5: Alice Stores│
│ Receives Credential │───────────>│ Credential in Wallet│
│ ─────────────────── │            │ ─────────────────── │
└─────────────────────┘            └─────────────────────┘
                                              │
          ┌───────────────────────────────────┼───────────────────────────┐
          │                                   │                           │
          ▼                                   ▼                           ▼
┌─────────────────────┐            ┌──────────────────────┐   ┌─────────────────────┐
│ STEP 6: Employer    │            │ STEP 7: Alice Creates│   │ STEP 8: Employer    │
│ Requests Proof      │◄───────────│ Verifiable           │   │ Verifies            │
│ ─────────────────   │  Scan QR   │ Presentation         │──>│ ───────────         │
│                     │  Code      │ ──────────────────   │   │                     │
│ "Please prove your  │            │                      │   │ 1. Resolve Uni DID  │
│  degree and         │            │ {                    │   │ 2. Verify signature │
│  experience"        │            │   "@context": [...], │   │ 3. Resolve Alice DID│
│                     │            │   "type": "VerifiablePresentation",            │
│                     │            │   "verifiableCredential": [cred1, cred2],      │
│                     │            │   "holder": "did:key:z6MkhaXg...",             │
│                     │            │   "proof": {         │   │ 4. Verify Alice sig │
│                     │            │     "type": "Ed25519Signature2020",            │
│                     │            │     "proofPurpose": "authentication",          │
│                     │            │     "proofValue": "z7sN8K..." ◄── Alice sig    │
│                     │            │   }                                            │
│                     │            │ }                    │   │ <X> All verified!   │
└─────────────────────┘            └──────────────────────┘   └─────────────────────┘
```

###### Summary Table: DID vs VC Generation
| Aspect | DID/DID Document | Verifiable Credential |
| :--- | :--- | :--- |
| **Who generates** | User (or user's wallet/app) | Issuer (University, Employer, Government) |
| **Who signs** | N/A (DID doc contains public keys) | Issuer signs with their private key |
| **Storage location** | DID Registry (blockchain, web server, etc.) | Holder's wallet |
| **Contains** | Public keys, service endpoints | Claims about subject, issuer info, signature |
| **References** | Self-references | References subject's DID and issuer's DID |
| **Number per user** | One DID (can have multiple for different contexts) | Many VCs per DID |
| **Creation cost** | Varies (free for did:key, gas fees for did:ethr) | Free (issuer pays any blockchain fees) |
| **Updateable** | Depends on method (did:key: no, did:ethr: yes) | No (immutable once issued, can be revoked) |


###### Signature Schemes: DID Authentication vs VC Signing
**Important Clarification: BBS is NOT part of the core DID specification** - it's a cryptographic signature scheme used for **Verifiable Credentials (VCs)** that are linked to DIDs.
| Layer | Traditional | Privacy-Enhanced Alternative | Purpose |
| :--- | :--- | :--- | :--- |
| **DID Authentication** | ECDSA, Ed25519 | Same (ECDSA/Ed25519) | Prove control of DID |
| **Verifiable Credentials** | ECDSA, Ed25519 | **BBS** | Sign credentials with unlinkability |


###### Two Different Layers
**DID Layer (Still ECDSA/Ed25519)** 
DID documents contain verification methods that use traditional signatures (did:ethr uses ECDSA, did:key uses Ed25519/ECDSA, did:web uses ECDSA/Ed25519).
```json
{
  "verificationMethod": [{
    "type": "EcdsaSecp256k1VerificationKey2019",  // ◄── ECDSA
    "publicKeyMultibase": "zQ3sh..."
  }, {
    "type": "Ed25519VerificationKey2020",         // ◄── Ed25519
    "publicKeyMultibase": "z6Mk..."
  }]
}
```
- **did:ethr** - Uses ECDSA (secp256k1) tied to Ethereum addresses
- **did:key** - Often uses Ed25519 or ECDSA
- **did:web** - Can use ECDSA P-256 (common), Ed25519, etc.

**VC Layer (ECDSA → BBS Option)** 
This is where BBS comes in as an alternative:
```
Traditional VC (ECDSA):
┌────────────────────────────────────────────────────────────┐
│ Issuer signs VC with ECDSA                                 │
│                                                            │
│ Each presentation = Same signature                         │
│ <N> Linkable across verifiers                              │
└────────────────────────────────────────────────────────────┘

Privacy-Preserving VC (BBS):
┌────────────────────────────────────────────────────────────┐
│ Issuer signs VC with BBS once                              │
│                                                            │
│ Each presentation = Fresh zero-knowledge proof             │
│ <X> Unlinkable                                             │
│ <X> Selective disclosure                                   │
└────────────────────────────────────────────────────────────┘
```

###### Why Use BBS for VCs?
| Feature | Traditional (ECDSA/Ed25519) | BBS Signatures | Benefit |
| :--- | :--- | :--- | :--- |
| **Unlinkability** | ❌ Same signature reused | ✅ Fresh proof each time | Prevent tracking across presentations |
| **Selective Disclosure** | ❌ Reveal all or nothing | ✅ Reveal only chosen attributes | Privacy-preserving data sharing |
| **Predicate Proofs** | ❌ Not possible | ✅ Prove statements (e.g., age > 18) without revealing exact value | Minimal disclosure |


###### Example: Alice's Job Application
**Traditional ECDSA:**
```
┌─────────────────────────────────────────────────────────────────────┐
│ Alice presents degree credential to Employer A                      │
│ Signature: 0x7a3f2b... (same for all presentations)                 │
│                                                                     │
│ Alice presents same credential to Employer B                        │
│ Signature: 0x7a3f2b... (identical!)                                 │
│                                                                     │
│ ⚠️ Employers A and B can collude to link Alice's applications       │
└─────────────────────────────────────────────────────────────────────┘
```

**BBS Signatures:**
```
┌─────────────────────────────────────────────────────────────────────┐
│ Alice presents degree to Employer A                                 │
│ Proof: π₁ = (randomized, zero-knowledge proof)                      │
│                                                                     │
│ Alice presents degree to Employer B                                 │
│ Proof: π₂ = (completely different due to fresh randomness)          │
│                                                                     │
│ <X> Cannot be linked - mathematically proven unlinkable             │
│ <X> Alice reveals only "degree: Bachelor" not "graduationDate"      │
└─────────────────────────────────────────────────────────────────────┘
```

###### Summary
| Use Case | Algorithm | Why |
| :--- | :--- | :--- |
| DID creation/control | ECDSA, Ed25519 | Hardware support, simplicity |
| DID authentication | ECDSA, Ed25519 | Standard algorithms |
| VC signing (traditional) | ECDSA, Ed25519 | Wide compatibility |
| **VC signing (privacy-focused)** | **BBS** | **Unlinkability, selective disclosure** |

**Key Point:** BBS is an **alternative for VC signatures**, enhancing the privacy layer of the DID ecosystem while the DID itself remains a simple identifier.

---

### 3. How Authorities of DID and VC Are Guaranteed
###### 1. Cryptographic Security Foundation
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Cryptographic Security Layers                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  LAYER 1: Public Key Infrastructure (PKI)                                   │
│  ─────────────────────────────────────────                                  │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Asymmetric Cryptography (Ed25519, secp256k1, etc.)                 │    │
│  │                                                                     │    │
│  │   Private Key ──────Sign──────> Signature                           │    │
│  │      │                                                              │    │
│  │      │ Derive                                                       │    │
│  │      ▼                                                              │    │
│  │   Public Key ────Verify──────> <X> Valid / <N> Invalid              │    │
│  │                                                                     │    │
│  │  Mathematical guarantee: Only private key holder can create         │    │
│  │  valid signatures; anyone with public key can verify                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    ▲                                        │
│                                    │                                        │
│  LAYER 2: DID Document Binding     │                                        │
│  ─────────────────────────────     │                                        │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  DID Document contains public keys for verification                 │    │
│  │                                                                     │    │
│  │  did:example:123 ────Resolves───> {                                 │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "publicKeyMultibase": "z6Mk..."  ◄── Public key                │    │
│  │    }]                                                               │    │
│  │  }                                                                  │    │
│  │                                                                     │    │
│  │  Anyone can resolve DID to get the public key for verification      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    ▲                                        │
│                                    │                                        │
│  LAYER 3: Verifiable Credentials   │                                        │
│  ───────────────────────────────── │                                        │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  VC contains:                                                       │    │
│  │  1. Claims about subject                                            │    │
│  │  2. Issuer's DID reference                                          │    │
│  │  3. Issuer's digital signature                                      │    │
│  │                                                                     │    │
│  │  Verification:                                                      │    │
│  │  1. Resolve issuer's DID ────> Get issuer's public key              │    │
│  │  2. Verify signature on VC ──> Confirm issuer signed it             │    │
│  │  3. Verify VC not tampered ──> Check hash matches                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### 2. DID Authority Guarantees
###### 2.1 Control Proof (Authentication)
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Proving Control Over a DID                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  CHALLENGE-RESPONSE PROTOCOL                                                │
│  ───────────────────────────                                                │
│                                                                             │
│  Verifier                          Prover (DID Controller)                  │
│  ────────                          ───────────────────────                  │
│                                                                             │
│  Step 1: Send Challenge                                                     │
│  ┌──────────────┐                                                           │
│  │  "Prove you  │                                                           │
│  │   control    │──────────>                                                │
│  │   did:ex:123 │                                                           │
│  └──────────────┘                                                           │
│                                                                             │
│  Step 2: Create Proof                                                       │
│                               ┌──────────────────────┐                      │
│                               │  1. Generate nonce   │                      │
│                               │  2. Sign with        │                      │
│                               │     private key      │                      │
│                               │  3. Create proof     │                      │
│                               │     document         │                      │
│                               └──────────┬───────────┘                      │
│                                          │                                  │
│  Step 3: Verify Proof                    │                                  │
│  ┌──────────────┐                        │                                  │
│  │  Resolve DID │◄───────────────────────┘                                  │
│  │  Get public  │  Send proof + signature                                   │
│  │  key         │                                                           │
│  └──────┬───────┘                                                           │
│         │                                                                   │
│         ▼                                                                   │
│  ┌──────────────┐                                                           │
│  │  Verify      │                                                           │
│  │  signature   │                                                           │
│  │  with public │                                                           │
│  │  key         │                                                           │
│  └──────┬───────┘                                                           │
│         │                                                                   │
│         ▼                                                                   │
│  ┌──────────────┐                                                           │
│  │  <X> Valid   │  Only holder of private key could create valid signature  │
│  │     OR       │                                                           │
│  │  <N> Invalid │  Proof of control established!                            │
│  └──────────────┘                                                           │
│                                                                             │
│  GUARANTEE: Mathematical certainty that prover controls the DID             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### 2.2 DID Method-Specific Authority
| DID Method | Authority Mechanism | Security Guarantee |
| :--- | :--- | :--- |
| **did:key** | Self-generated from key pair | Control = possession of private key |
| **did:web** | Domain ownership via DNS + TLS | Control = DNS record + HTTPS server control |
| **did:ethr** | Ethereum address control | Control = possession of Ethereum private key |
| **did:ion** | Bitcoin anchoring + Sidetree | Control = possession of operation signing keys |
| **did:sov** | Hyperledger Indy consensus | Control = consensus of validator nodes |


###### 3. Verifiable Credential Authority Guarantees
###### 3.1 Issuer Authentication
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Verifying VC Issuer Authority                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  VERIFICATION STEPS:                                                        │
│                                                                             │
│  Step 1: Extract Issuer DID from VC                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  {                                                                  │    │
│  │    "issuer": "did:web:uni.edu",  ◄── Extract issuer DID             │    │
│  │    ...                                                              │    │
│  │    "proof": {                                                       │    │
│  │      "verificationMethod": "did:web:uni.edu#key-1"                  │    │
│  │    }                                                                │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│                                    ▼                                        │
│  Step 2: Resolve Issuer's DID                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  did:web:uni.edu ────Resolve───> {                                  │    │
│  │    "verificationMethod": [{                                         │    │
│  │      "id": "did:web:uni.edu#key-1",                                 │    │
│  │      "publicKeyJwk": { "x": "0-e2i2...", ... }  ◄── Public key      │    │
│  │    }]                                                               │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│                                    ▼                                        │
│  Step 3: Verify VC Signature                                                │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                                                                     │    │
│  │  VC Document (without proof) ────Hash───> Hash Value                │    │
│  │                                                                     │    │
│  │  Signature from VC proof ────────> Verify against hash              │    │
│  │         │                            using issuer's public key      │    │
│  │         ▼                                                           │    │
│  │    <X> Valid Signature                                              │    │
│  │       = Issuer really did sign this VC                              │    │
│  │                                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  GUARANTEE: Cryptographic proof that specific issuer created the VC         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### 3.2 Trust Frameworks
Beyond cryptography, real-world trust is established through:
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Trust Framework Layers                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  LAYER 1: Cryptographic Trust (Base Layer)                                  │
│  ─────────────────────────────────────────                                  │
│  • Mathematical guarantees of signatures                                    │
│  • Decentralized verification (no central authority needed)                 │
│  • Tamper-evident (any modification breaks signature)                       │
│                                                                             │
│  LAYER 2: Institutional Trust                                               │
│  ────────────────────────────                                               │
│  • University is accredited and recognized                                  │
│  • Government ID is legally valid                                           │
│  • Employer is registered business                                          │
│  • Trust registries (eIDAS, etc.)                                           │
│                                                                             │
│  LAYER 3: Governance Trust                                                  │
│  ─────────────────────────                                                  │
│  • Legal frameworks (eIDAS 2.0 in EU)                                       │
│  • Industry standards (W3C VC Data Model)                                   │
│  • Compliance requirements (GDPR, KYC/AML)                                  │
│  • Audit trails and accountability                                          │
│                                                                             │
│  LAYER 4: Technical Trust                                                   │
│  ─────────────────────                                                      │
│  • DID method specifications                                                │
│  • Revocation registries (if credential is revoked)                         │
│  • Status lists (is credential still valid?)                                │
│  • Schema validation (does data follow expected format?)                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### 4. Revocation and Status Checking
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Credential Revocation Mechanisms                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  PROBLEM: What if a credential needs to be revoked?                         │
│  (e.g., degree revoked due to academic misconduct, employee terminated)     │
│                                                                             │
│  SOLUTION 1: Revocation List                                                │
│  ───────────────────────────                                                │
│                                                                             │
│  Issuer maintains a public revocation list:                                 │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  https://uni.edu/revoked-credentials.json                           │    │
│  │                                                                     │    │
│  │  {                                                                  │    │
│  │    "revokedCredentials": [                                          │    │
│  │      "urn:uuid:1234...",  ◄── Alice's degree revoked                │    │
│  │      "urn:uuid:5678..."                                             │    │
│  │    ]                                                                │    │
│  │  }                                                                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Verifier checks: Is credential ID in revocation list?                      │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  SOLUTION 2: Status List 2021 (Privacy-Preserving)                          │
│  ────────────────────────────────────────────────                           │
│                                                                             │
│  Issuer publishes compressed bitstring:                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Status List: [0, 0, 1, 0, 0, 0, 1, 0, ...]  (1 = revoked)          │    │
│  │                                                                     │    │
│  │  Alice's credential index: 2 ────> Value = 1 (REVOKED)              │    │
│  │  Bob's credential index: 0 ──────> Value = 0 (VALID)                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Advantages:                                                                │
│  • Verifier doesn't reveal which credential they're checking                │
│  • Efficient for large numbers of credentials                               │
│  • Cryptographically signed by issuer                                       │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  SOLUTION 3: Blockchain Anchoring                                           │
│  ────────────────────────────────                                           │
│                                                                             │
│  Issuer publishes revocation transaction:                                   │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Ethereum Transaction                                               │    │
│  │                                                                     │    │
│  │  Function: revokeCredential(bytes32 credentialHash)                 │    │
│  │  From: did:ethr:uni.edu                                             │    │
│  │  Data: 0x8a3f2b1c... (hash of Alice's credential)                   │    │
│  │  Status: <X> Confirmed on blockchain                                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Immutable, timestamped proof of revocation                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

###### 5. Complete Authority Verification Flow
```
┌─────────────────────────────────────────────────────────────────────────────┐
│               Complete Authority Verification Example                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SCENARIO: TechCorp verifies Alice's degree                                 │
│                                                                             │
│  ┌──────────────┐                              ┌──────────────┐             │
│  │  TechCorp    │                              │  University  │             │
│  │  (Verifier)  │                              │  (Issuer)    │             │
│  └──────┬───────┘                              └──────┬───────┘             │
│         │                                             │                     │
│         │  1. Receive VC from Alice                   │                     │
│         │◄────────────────────────────────────────────┤                     │
│         │                                             │                     │
│         │  2. Verify VC Structure                     │                     │
│         │     <X> Valid JSON-LD                       │                     │
│         │     <X> Required fields present             │                     │
│         │     <X> Not expired                         │                     │
│         │                                             │                     │
│         │  3. Resolve Issuer DID                      │                     │
│         │     did:web:uni.edu ────> DNS lookup        │                     │
│         │     DNS ────> 203.0.113.10                  │                     │
│         │     HTTPS GET /.well-known/did.json         │                     │
│         │     <X> Retrieved DID document              │                     │
│         │                                             │                     │
│         │  4. Verify Issuer Signature                 │                     │
│         │     Extract public key from DID doc         │                     │
│         │     Verify signature on VC                  │                     │
│         │     <X> Signature valid                     │                     │
│         │     <X> Issuer authenticated                │                     │
│         │                                             │                     │
│         │  5. Check Revocation Status                 │                     │
│         │     Query: uni.edu/status/1234...           │                     │
│         │     Response: { "revoked": false }          │                     │
│         │     <X> Credential not revoked              │                     │
│         │                                             │                     │
│         │  6. Verify Holder Binding                   │                     │
│         │     VC.subject.id == Alice's DID?           │                     │
│         │     <X> Yes, issued to correct person       │                     │
│         │                                             │                     │
│         │  7. Verify Presentation (if applicable)     │                     │
│         │     Alice signed presentation?              │                     │
│         │     <X> Yes, Alice controls her DID         │                     │
│         │                                             │                     │
│         │  ╔═══════════════════════════════════════╗  │                     │
│         │  ║  <X> ALL CHECKS PASSED                ║  │                     │
│         │  ║  • Cryptographically authentic        ║  │                     │
│         │  ║  • Issuer verified (uni.edu)          ║  │                     │
│         │  ║  • Not revoked                        ║  │                     │
│         │  ║  • Issued to correct person           ║  │                     │
│         │  ║  • Holder proved control              ║  │                     │
│         │  ╚═══════════════════════════════════════╝  │                     │
│         │                                             │                     │
└─────────┴─────────────────────────────────────────────┴─────────────────────┘
```

###### 6. Security Properties Summary
| Security Property | How It's Guaranteed | Mechanism |
| :--- | :--- | :--- |
| **Authenticity** | Digital signatures | Only private key holder can sign |
| **Integrity** | Cryptographic hashing | Any tampering breaks signature |
| **Non-repudiation** | Blockchain/ledger anchoring (optional) | Immutable record of issuance |
| **Control** | Challenge-response protocol | Prover must demonstrate key possession |
| **Revocability** | Revocation lists/status lists | Issuer can invalidate credentials |
| **Privacy** | Selective disclosure | Holder chooses what to reveal |
| **Decentralization** | Distributed registries | No single point of failure/control |


###### Key Benefits in This Scenario
| Traditional Process | DID-Based Process |
| :--- | :--- |
| Alice emails PDF resume | Alice shares cryptographically signed credentials |
| Employer must contact university to verify degree | Employer verifies instantly using university's public DID |
| Verification takes days/weeks | Verification takes seconds |
| Alice reveals all information | Alice shares only what's necessary |
| Risk of forged documents | Cryptographically impossible to forge |
| Alice has no control over her data | Alice owns and controls her credentials |


###### Other Real-World Use Cases
| Use Case | How DID Helps |
| :--- | :--- |
| **Healthcare** | Patients carry medical records between hospitals securely |
| **Banking** | Instant KYC verification without sharing excessive personal data |
| **Supply Chain** | Verify product authenticity from manufacturer to consumer |
| **Government Services** | Citizens access services with privacy-preserving digital ID |
| **Education** | Students collect lifelong learning credentials from multiple institutions |
| **IoT Devices** | Devices prove their identity and firmware authenticity |


---

### 4. DID Syntax
###### Basic Structure
A DID is a URI (Uniform Resource Identifier) consisting of three parts:
```
did:method:method-specific-id
│    │      │
│    │      └── Method-Specific Identifier (unique within the method)
│    └───────── Method Name (identifies the DID method)
└────────────── URI Scheme (always "did")

###### ABNF Grammar (Formal Definition)
According to W3C specification, the DID syntax is defined using ABNF (Augmented Backus-Naur Form):
```abnf
did                 = "did:" method-name ":" method-specific-id
method-name         = 1*method-char
method-char         = %x61-7A / DIGIT    ; lowercase letters a-z or digits 0-9
method-specific-id  = *( *idchar ":" ) 1*idchar
idchar              = ALPHA / DIGIT / "." / "-" / "_" / pct-encoded
pct-encoded         = "%" HEXDIG HEXDIG   ; percent-encoded character
```

###### Syntax Components Explained
| Component | Description | Rules | Examples |
| :--- | :--- | :--- | :--- |
| **did** | URI scheme | Fixed string "did:" (lowercase) | did: |
| **method-name** | DID method identifier | Lowercase letters (a-z) and digits (0-9) only; at least 1 character | web, ethr, key, ion, sov |
| **method-specific-id** | Unique identifier within method | Letters, digits, ., -, _, and percent-encoded characters; can contain : as namespace separator | 123456, abc123, EiAnKD8... |


###### Character Set Details
**Allowed Characters in method-name:** 
Lowercase letters and digits are allowed, but uppercase and special characters are not allowed.
```
a b c d e f g h i j k l m n o p q r s t u v w x y z 0 1 2 3 4 5 6 7 8 9
```
- <X> Lowercase letters (a-z)
- <X> Digits (0-9)
- <N> Uppercase letters (NOT allowed)
- <N> Special characters (NOT allowed)

**Allowed Characters in method-specific-id:**
```
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z (uppercase)
a b c d e f g h i j k l m n o p q r s t u v w x y z (lowercase)
0 1 2 3 4 5 6 7 8 9 (digits)
. - _ (special characters)
% XX (percent-encoded, where XX is hexadecimal)
: (colon, for namespace separation)
```

###### Real-World Examples by Method
| DID Method | Example DID | Explanation |
| :--- | :--- | :--- |
| did:web | did:web:example.com:user:alice | Identity anchored to domain example.com |
| did:ethr | did:ethr:0xE6Fe... | Identity on Ethereum blockchain |
| did:key | did:key:z6Mk... | Self-contained key-based identity |
| did:ion | did:ion:EiAnKD... | Bitcoin-anchored identity (ION) |
| did:sov | did:sov:WRfXP... | Identity on Sovrin/Hyperledger Indy |
| did:peer | did:peer:z6Mk... | Peer-to-peer private identity |
| did:jwk | did:jwk:eyJr... | JSON Web Key based identity |


###### DID Methods Explained
###### did:web - DNS-Based Decentralized Identifier
**Overview:** did:web uses existing web infrastructure (DNS + HTTPS) to host DID documents.
**How It Works:**
```
did:web:example.com:user:alice
    │       │         │
    │       │         └── Path: /user/alice/did.json
    │       └──────────── Domain: example.com
    └──────────────────── Method: web
```

**Resolution Process:**
1. Parse the DID to extract domain and path.
2. Construct HTTPS URL.
3. Fetch DID document via HTTPS with TLS verification.
4. Return the JSON document.
**DID Document Storage:**
* Stored at: https://<domain>/.well-known/did.json (for domain-level DID) or https://<domain>/<path>/did.json (for path-based DIDs).
**Example:**
```
DID:  did:web:example.com
URL:  https://example.com/.well-known/did.json

DID:  did:web:example.com:user:alice
URL:  https://example.com/user/alice/did.json
```
  
| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ⚠️ Low - Depends on DNS (centralized) and web server |
| **Cost** | ✅ Free - No blockchain fees |
| **Setup** | ✅ Easy - Just host a JSON file |
| **Key Rotation** | ✅ Supported - Update the JSON file |
| **Deactivation** | ✅ Supported - Remove the JSON file |
| **Best For** | Organizations, enterprises, domain-based identity |
| **Limitations** | Depends on domain ownership; DNS can be seized |


###### did:ethr - Ethereum-Based Decentralized Identifier
**Overview:** did:ethr leverages Ethereum blockchain and smart contracts (ERC-1056) for identity management.
**How It Works:**
```
did:ethr:0xE6Fe788d8ca214A080b0f6aC7F48480b2AEfa9a6
    │      │
    │      └── Ethereum address (identifier)
    └───────── Method: ethr
```

**Key Features:**
- Uses ERC-1056 smart contract for identity management
- Any Ethereum address or secp256k1 public key can be a DID
- No transaction needed to create a DID (implicit registration)
- Supports key rotation, delegation, and service endpoints

**Resolution Process:**
1. Extract Ethereum address from DID
2. Query ERC-1056 smart contract for identity owner
3. Fetch events (DIDOwnerChanged, DIDDelegateChanged, DIDAttributeChanged)
4. Build DID document from contract state and events

**Network Support:**
- Ethereum Mainnet (default): `did:ethr:0x...` or `did:ethr:mainnet:0x...`
- Goerli Testnet: `did:ethr:goerli:0x...`
- Other EVM chains: `did:ethr:0x1:0x...` (chain ID)

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ✅ High - Uses Ethereum blockchain |
| **Cost** | ⚠️ Gas fees for updates (free to create) |
| **Setup** | ✅ Easy - Just generate an Ethereum key pair |
| **Key Rotation** | ✅ Supported - Via smart contract |
| **Deactivation** | ✅ Supported - Set owner to 0x0 |
| **Best For** | Web3 apps, DeFi, Ethereum ecosystem |
| **Limitations** | Gas fees for updates; depends on Ethereum |


###### did:key - Self-Contained Cryptographic Identifier
**Overview:** did:key is a purely generative DID method derived directly from a cryptographic public key.
**How It Works:**
```
did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK
    │     │
    │     └── Multibase-encoded public key
    └──────── Method: key
```

**Generation Process:**
1. Generate a cryptographic key pair (e.g., Ed25519)
2. Encode public key with multicodec prefix
3. Encode result with Multibase (base58-btc)
4. DID = `did:key:<encoded-value>`

**Supported Key Types:** Ed25519, X25519, Secp256k1, P-256, P-384.
| Key Type | Prefix | Example DID Start |
|----------|--------|-------------------|
| Ed25519 | `z6Mk` | `did:key:z6Mk...` |
| X25519 | `z6LS` | `did:key:z6LS...` |
| Secp256k1 | `zQ3s` | `did:key:zQ3s...` |
| P-256 | `zDn` | `did:key:zDn...` |
| P-384 | `z82` | `did:key:z82...` |

**Resolution Process:**
1. Decode the multibase-encoded value.
2. Extract key type from multicodec prefix.
3. Generate DID document from the public key.

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ✅ Highest - No registry needed |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Simplest - Just generate a key |
| **Key Rotation** | ❌ Not supported |
| **Deactivation** | ❌ Not supported |
| **Best For** | Ephemeral identities, offline use, testing, temporary credentials |
| **Limitations** | Cannot update or deactivate; compromised keys are permanent |


###### did:ion - Bitcoin-Anchored Scalable Identifier
**Overview:** Layer 2 network built on Bitcoin using the Sidetree protocol for high scalability.
**How It Works:**
```
did:ion:EiAnKD8-jfdd0MDcZUjAbRgaThBrMxPTFOxcnfJhI7dKgA
    │     │
    │     └── Sidetree-encoded identifier
    └──────── Method: ion
```

**Architecture:**
```
┌─────────────────────────────────────────────────────┐
│                    ION Network                      │
│  ┌─────────────────────────────────────────────┐    │
│  │           Sidetree Protocol                 │    │
│  │  • Batch DID operations                     │    │
│  │  • Content-addressable storage (IPFS)       │    │
│  │  • Deterministic resolution                 │    │
│  └─────────────────────────────────────────────┘    │
│                        ▲                            │
│                        │ Anchoring                  │
│  ┌─────────────────────┴───────────────────────┐    │
│  │           Bitcoin Blockchain                │    │
│  │  • Immutable timestamping                   │    │
│  │  • Proof of existence                       │    │
│  └─────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

**Key Features:** Scalable, no token required, batch operations, censorship resistant.
- **Scalable:** Thousands of DID operations per second
- **No token required:** Uses Bitcoin only for anchoring
- **Batch operations:** Multiple DIDs in single Bitcoin transaction
- **Censorship resistant:** DIDs can only be deactivated by owners

**Resolution Process:**
1. Decode Sidetree identifier.
2. Find anchoring transaction on Bitcoin.
3. Fetch DID operation data from IPFS.
4. Apply operations in chronological order and return resulting DID document.

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ✅ Very High - Bitcoin + distributed storage |
| **Cost** | ⚠️ Low - Batch operations reduce Bitcoin fees |
| **Setup** | ⚠️ Moderate - Requires ION node or resolver |
| **Key Rotation** | ✅ Supported |
| **Deactivation** | ✅ Supported |
| **Best For** | Large-scale public identity, enterprise SSI |
| **Limitations** | More complex setup; depends on Bitcoin |


###### did:sov - Hyperledger Indy-Based Identifier
**Overview:** operates on Hyperledger Indy, a permissioned distributed ledger designed for decentralized identity.
**How It Works:**
```
did:sov:WRfXPg8dantKVubE3HX8pw
    │     │
    │     └── Indy network identifier
    └──────── Method: sov
```

**Key Features:** Built-in governance, privacy-preserving, supports anonymous credentials.
- Built-in governance framework
- Privacy-preserving by design
- Supports anonymous credentials (AnonCreds)
- Purpose-built for identity use cases

**Resolution Process:** Parse the DID, query the Indy ledger for the NYM transaction, build DID document.
1. Parse the DID
2. Query the Indy ledger for the NYM transaction
3. Build DID document from ledger state

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ⚠️ Medium - Permissioned network |
| **Cost** | ⚠️ Transaction fees (low) |
| **Setup** | ⚠️ Moderate - Requires Indy network access |
| **Key Rotation** | ✅ Supported |
| **Deactivation** | ✅ Supported |
| **Best For** | Enterprise identity, regulated industries, governments |
| **Limitations** | Permissioned network; less decentralized than public chains |


###### did:peer - Private Peer-to-Peer Identifier
**Overview:** Designed for private, pairwise relationships where DIDs don't need to be registered on any network.
**How It Works:**
```
did:peer:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK
    │       │
    │       └── Encoded genesis document
    └────────── Method: peer
```

**Key Features:** No registry required, created and exchanged directly between peers, supports key rotation.
- No registry required
- Created and exchanged directly between peers
- Supports key rotation through versioning
- Ideal for private relationships

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ✅ Very High - No registry |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Easy - Generate and share |
| **Key Rotation** | ✅ Supported (versioned) |
| **Deactivation** | ✅ Supported |
| **Best For** | Private relationships, agent-to-agent communication |
| **Limitations** | Not resolvable publicly; requires secure exchange |


###### did:jwk - JSON Web Key Identifier
**Overview:** encodes a JSON Web Key (JWK) directly in the DID.
**How It Works:**
```
did:jwk:eyJrdHkiOiJFQyIsImNydiI6IlAtMjU2In0
    │     │
    │     └── Base64url-encoded JWK
    └──────── Method: jwk
```

**Resolution Process:** Decode base64url-encoded value, parse as JWK, generate DID document.
1. Decode the base64url-encoded value
2. Parse as JWK (JSON Web Key)
3. Generate DID document from JWK

| Aspect | Description |
| :--- | :--- |
| **Decentralization** | ✅ High - No registry needed |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Easy - Encode a JWK |
| **Key Rotation** | ❌ Not supported |
| **Deactivation** | ❌ Not supported |
| **Best For** | Quick prototypes, testing, JWT/JWK ecosystems |
| **Limitations** | Cannot update or deactivate; longer DIDs than did:key |


###### Quick Comparison Table
| Method | Registry | Key Rotation | Deactivation | Cost | Best Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **did:web** | DNS/HTTPS | ✅ | ✅ | Free | Organizational identity |
| **did:ethr** | Ethereum | ✅ | ✅ | Gas fees | Web3, DeFi |
| **did:key** | None | ❌ | ❌ | Free | Ephemeral, offline |
| **did:ion** | Bitcoin | ✅ | ✅ | Low | Large-scale SSI |
| **did:sov** | Indy | ✅ | ✅ | Low | Enterprise, regulated |
| **did:peer** | None | ✅ | ✅ | Free | Private P2P |
| **did:jwk** | None | ❌ | ❌ | Free | Testing, JWK ecosystem |


###### DID URL Syntax
A DID URL extends the basic DID with path, query, and fragment components:
```abnf
did-url = did path-abempty [ "?" query ] [ "#" fragment ]
```

```
did:example:123456/path/to/resource?versionId=1#public-key-1
│              │                │           │
│              │                │           └── Fragment: identifies sub-resource
│              │                └────────────── Query: parameters
│              └─────────────────────────────── Path: resource location
└────────────────────────────────────────────── Base DID
```

**DID URL Components:**
| Component | Symbol | Purpose | Example |
| :--- | :--- | :--- | :--- |
| Path | / | Navigate to resources | did:example:123/credentials |
| Query | ? | Pass parameters | did:example:123?versionId=1 |
| Fragment | # | Reference sub-resource | did:example:123#key-1 |


###### Valid vs Invalid DIDs
| DID | Valid? | Reason |
| :--- | :--- | :--- |
| did:web:example.com | ✅ Valid | Correct format |
| did:ethr:0x123abc | ✅ Valid | Lowercase method name |
| did:Web:example.com | ❌ Invalid | Uppercase in method name |
| did:web: | ❌ Invalid | Missing method-specific-id |
| did::123 | ❌ Invalid | Empty method name |
| did:web:example.com#key-1 | ✅ Valid | DID URL with fragment |
| did:example:123:456:789 | ✅ Valid | Multiple colons allowed in method-specific-id |
| did:my-method:abc123 | ✅ Valid | Method name with hyphen |
| did:my_method:abc123 | ✅ Valid | Method name with underscore |


###### Percent-Encoding
| Character | Encoded | Example |
| :--- | :--- | :--- |
| Space | %20 | did:web:example.com:user%20name |
| # (in id) | %23 | did:example:tag%23value |
| ? (in id) | %3F | did:example:query%3Fparam |


###### Namespace Hierarchy in method-specific-id
```
did:example:namespace1:namespace2:identifier
             │          │          │
             │          │          └── Final identifier
             │          └────────────── Sub-namespace
             └───────────────────────── Root namespace
```

Example:
```
did:web:example.com:users:alice
         │         │     │
         │         │     └── User identifier
         │         └──────── Users namespace
         └────────────────── Domain
```

###### Case Sensitivity
- **Method name**: Case-insensitive (but MUST be lowercase)
- **Method-specific-id**: Case-sensitive (depends on method specification)

```
did:web:example.com  <X> Valid
did:WEB:example.com  <N> Invalid (uppercase method)
did:web:Example.com  <X> Valid (case-sensitive in method-specific-id)
```


##### W3C Status
DID 1.0 was approved as an official W3C Recommendation in June 2022.

---

### 5. Core Architecture
##### 5.1 Architecture Overview
The DID architecture consists of several interconnected components that work together to enable decentralized identity management. Here's a comprehensive view:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        DID Ecosystem Architecture                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌──────────────┐                          ┌──────────────────────┐       │
│    │ DID Subject  │<────── identifies ─────  │         DID          │       │
│    │  (Entity)    │                          │  did:example:123     │       │
│    └──────────────┘                          └──────────┬───────────┘       │
│           │                                            │                    │
│           │                                            │ resolves to        │
│           │                                            ▼                    │
│           │                               ┌──────────────────────┐          │
│           │                               │    DID Document      │          │
│           │                               │  ┌────────────────┐  │          │
│           │                               │  │ id             │  │          │
│           │                               │  │ controller     │  │          │
│           │                               │  │ verification   │  │          │
│           │                               │  │ authentication │  │          │
│           │                               │  │ services       │  │          │
│           │                               │  └────────────────┘  │          │
│           │                               └──────────┬───────────┘          │
│           │                                          │                      │
│           │                                          │ stored in            │
│           │                                          ▼                      │
│    ┌──────┴─────────┐                   ┌──────────────────────┐            │
│    │ DID Controller │<── controls ──────│ Verifiable Data      │            │
│    │  (Entity)      │                   │ Registry             │            │
│    └────────────────┘                   │ (Blockchain/DB/etc)  │            │
│                                         └──────────────────────┘            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

##### 5.2 Core Components Explained
###### DID Subject (被标识主体)
**Definition:** The entity that the DID identifies (a person, organization, device, abstract entity, etc.).
**Key Points:** The DID subject is what the DID refers to, does not necessarily control the DID, and can be the same as the controller.
```
DID Subject Examples:
┌─────────────────────────────────────────────────────────┐
│ Subject Type        │ Example DID                      │
├─────────────────────┼──────────────────────────────────┤
│ Person              │ did:web:alice.example.com        │
│ Organization        │ did:web:company.com              │
│ IoT Device          │ did:ion:EiAnKD8...device123      │
│ Smart Contract      │ did:ethr:0x123abc...             │
│ Data Model          │ did:example:dataset456           │
└─────────────────────────────────────────────────────────┘
```

###### DID Controller (DID 控制者)
**Definition:** The entity that has the authority to make changes to the DID document.
**Key Points:** Controls the DID document, owns private keys, can be the same as the subject, and a DID can have multiple controllers.
```
Controller Relationships:
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Case 1: Self-Controlled (Subject = Controller)         │
│  ┌─────────────┐                                        │
│  │   Person    │── owns ──> DID <── controls ──┐        │
│  │  (Alice)    │                              │         │
│  └─────────────┘                              │         │
│                                      (same entity)      │
│                                                         │
│  Case 2: Guardianship (Subject ≠ Controller)            │
│  ┌─────────────┐                              │         │
│  │   Parent    │── controls ──> DID <── identifies      │
│  │             │                              │         │
│  └─────────────┘                              │         │
│                                      ┌───────┴───┐      │
│                                      │   Child   │      │
│                                      │ (Subject) │      │
│                                      └───────────┘      │
│                                                         │
│  Case 3: Multi-Controller                               │
│  ┌─────────────┐        ┌─────────────┐                 │
│  │ Controller 1│───────>│     DID     │<──────┐         │
│  └─────────────┘        └─────────────┘       │         │
│                                 │              │        │
│                         ┌───────┴───────┐      │        │
│                         │   Subject     │      │        │
│                         │ (Organization)│      │        │
│                         └───────────────┘      │        │
│  ┌─────────────┐                               │        │
│  │ Controller 2│───────────────────────────────┘        │
│  └─────────────┘                                        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

###### DID Document (DID 文档)
**Definition:** A JSON-LD document that contains information associated with a DID, including cryptographic material and service endpoints.
**Structure:**
```json
{
  "@context": "https://www.w3.org/ns/did/v1.1",
  "id": "did:example:123456789abcdefghi",
  "controller": "did:example:123456789abcdefghi",
  "verificationMethod": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "Multikey",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyMultibase": "z6MkmM42vxfqZQsv4ehtTjFFxQ4sQKS2w6WR7emozFAn5cxu"
  }],
  "authentication": ["did:example:123456789abcdefghi#keys-1"],
  "assertionMethod": ["did:example:123456789abcdefghi#keys-1"],
  "keyAgreement": [{
    "id": "did:example:123456789abcdefghi#keys-2",
    "type": "X25519KeyAgreementKey2019",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyMultibase": "z6LSj72tK8brWgZja8NLRwPigth2T9QRiG1uH9oKZuKjdh9p"
  }],
  "service": [{
    "id": "did:example:123456789abcdefghi#hub",
    "type": "IdentityHub",
    "serviceEndpoint": "https://hub.example.com/"
  }]
}
```

###### Verifiable Data Registry (可验证数据注册表)
**Definition:** A system where DIDs and DID documents are stored and can be resolved from.
**Types of Registries:**
| Registry Type | Examples | Characteristics |
| :--- | :--- | :--- |
| **Blockchain** | Bitcoin, Ethereum, Hyperledger Indy | Immutable, decentralized, consensus-based |
| **Distributed Database** | IPFS, Distributed Hash Tables | Content-addressable, peer-to-peer |
| **Web Infrastructure** | DNS + HTTPS (did:web) | Existing infrastructure, centralized DNS |
| **Self-Contained** | None (did:key, did:jwk) | No registry, derived from key |


##### 2.3 DID Document Properties Deep Dive
###### Property Categories
###### Verification Methods Explained
**Definition:** Cryptographic material (public keys) that can be used to verify proofs.
**Verification Method Properties:**
| Property | Required | Description |
| :--- | :--- | :--- |
| id | Yes | Unique identifier (DID URL) |
| type | Yes | Key type (Multikey, JsonWebKey2020, etc.) |
| controller | Yes | DID that controls this key |
| publicKeyMultibase | No | Multibase-encoded public key |
| publicKeyJwk | No | JWK format public key |


**Common Verification Method Types:**
| Type | Algorithm | Use Case |
| :--- | :--- | :--- |
| Multikey | Ed25519, secp256k1, etc. | General purpose |
| JsonWebKey2020 | RSA, EC (P-256, etc.) | JWT/JWS compatibility |
| Ed25519VerificationKey2020 | Ed25519 | Digital signatures |
| EcdsaSecp256k1VerificationKey2019 | secp256k1 | Ethereum, Bitcoin |


###### Verification Relationships Explained
**Definition:** Relationships between a DID subject and its verification methods, defining the purpose of each key.
**Detailed Purpose of Each Relationship:**
| Relationship | Purpose | Example Use Case |
| :--- | :--- | :--- |
| authentication | Prove control of DID | Login, access control |
| assertionMethod | Issue Verifiable Credentials | University issuing diplomas |
| keyAgreement | Encrypt/decrypt messages | Secure messaging, key exchange |
| capabilityInvocation | Authorize actions | Smart contract calls, API access |
| capabilityDelegation | Delegate authority | Authorizing another party to act |


###### Service Endpoints Explained
**Definition:** URLs where services related to the DID subject can be accessed.
**Common Service Types:**
| Service Type | Purpose |
| :--- | :--- |
| DIDCommMessaging | Secure peer-to-peer messaging |
| IdentityHub | Personal data storage |
| LinkedDomains | Linked websites/domains |
| VerifiableCredentialService | VC issuance/verification |


##### 2.4 How Components Work Together
###### Complete DID Lifecycle Flow
###### Authentication Flow Example

##### 2.5 Key Components Summary
| Component | Description | Required? |
| :--- | :--- | :--- |
| id | The DID itself (identifier) | ✅ Yes |
| controller | Who controls the DID document | No (defaults to self) |
| alsoKnownAs | Other identifiers for this subject | No |
| verificationMethod | Cryptographic public keys | No |
| authentication | Keys for proving DID control | No |
| assertionMethod | Keys for issuing credentials | No |
| keyAgreement | Keys for encryption | No |
| capabilityInvocation | Keys for invoking actions | No |
| capabilityDelegation | Keys for delegating authority | No |
| service | Service endpoints | No |


---

### 6. How DID Works: Technical Deep Dive
##### 3.1 DID Operations Overview
| Operation | Description | Key Point |
| :--- | :--- | :--- |
| **Create** | Generate a new DID and DID document | Establishes control over the identifier |
| **Read/Resolve** | Retrieve the DID document for a DID | Enables verification and interaction |
| **Update** | Modify the DID document | Supports key rotation, service changes |
| **Deactivate** | Permanently revoke the DID | Prevents future use of the identifier |


##### 3.2 DID Resolution Process
###### Resolution Function
###### Detailed Resolution Algorithm
###### Resolution Options
| Option | Type | Description | Default |
| :--- | :--- | :--- | :--- |
| accept | string | Preferred media type (e.g., application/did+json) | method-specific |
| expandRelativeUrls | boolean | Convert relative URLs to absolute | false |
| versionId | string | Request specific version | latest |
| versionTime | datetime | Request version at timestamp | latest |


###### Resolution Outputs
| Output | Description |
| :--- | :--- |
| didResolutionMetadata | Metadata about the resolution process |
| didDocument | The resolved DID document |
| didDocumentMetadata | Metadata about the DID document |


###### DID Resolution Metadata Properties
| Property | Type | Description |
| :--- | :--- | :--- |
| contentType | string | Media type of the DID document representation |
| error | string | Error code if resolution failed |
| retrieved | datetime | When the document was retrieved |


###### DID Document Metadata Properties
| Property | Type | Description |
| :--- | :--- | :--- |
| created | datetime | Timestamp when DID was created |
| updated | datetime | Timestamp of last update |
| versionId | string | Version identifier |
| deactivated | boolean | Whether the DID is deactivated |
| nextUpdate | datetime | When next update occurred |
| nextVersionId | string | Next version ID |
| equivalentId | array | Other DIDs referring to same subject |
| canonicalId | string | Canonical DID for this subject |


##### 3.3 DID URL Dereferencing
###### Dereferencing Function
###### DID URL Components
###### Dereferencing Process

##### 3.4 Common DID Parameters
| Parameter | Description | Example |
| :--- | :--- | :--- |
| service | Select service by ID | did:example:123?service=files |
| serviceType | Select service by type | did:example:123?serviceType=LinkedDomains |
| relativeRef | Relative URI at service endpoint | did:example:123?service=files&relativeRef=/doc.pdf |
| versionId | Specific DID document version | did:example:123?versionId=1 |
| versionTime | DID document at timestamp | did:example:123?versionTime=2021-05-10T17:00:00Z |
| hl | Resource hash for integrity | did:example:123?hl=zQmWvQx... |
| transformKey | Transform key format | did:example:123?transformKey=JsonWebKey |


##### 3.5 Authentication Flow
###### Authentication Process
###### Cryptographic Proof Structure
###### Proof Purposes
| Purpose | When to Use | Example |
| :--- | :--- | :--- |
| authentication | Prove DID control | Login, access control |
| assertionMethod | Issue credentials | University diploma |
| keyAgreement | Establish shared secret | Encrypted messaging |
| capabilityInvocation | Perform authorized action | API call |
| capabilityDelegation | Grant authority to another | Delegate signing |


##### 3.6 Error Handling
###### Resolution Errors
| Error Code | Description | When It Occurs |
| :--- | :--- | :--- |
| invalidDid | DID syntax is invalid | Malformed DID string |
| methodNotSupported | Resolver doesn't support method | Unknown DID method |
| notFound | DID document not found | DID doesn't exist |
| deactivated | DID has been deactivated | DID was revoked |
| representationNotSupported | Media type not supported | Invalid accept header |


###### Dereferencing Errors
| Error Code | Description |
| :--- | :--- |
| invalidDidUrl | DID URL syntax is invalid |
| notFound | Resource not found |
| serviceNotFound | Referenced service doesn't exist |


##### 3.7 Method-Specific Resolution Examples
###### did:web Resolution
###### did:ethr Resolution
###### did:key Resolution
| updated | Timestamp of last update | | deactivated | Whether DID is deactivated (true/false) | | nextUpdate | Timestamp of next scheduled update | | versionId | Version identifier of DID document | | nextVersionId | Version identifier of next version | | equivalentId | Logically equivalent DIDs | | canonicalId | Canonical DID for the subject |


##### 3.5 Authentication Flow
##### 3.6 Verification Relationships
| Relationship | Purpose |
| :--- | :--- |
| authentication | Prove control of DID (login, authentication) |
| assertionMethod | Issue Verifiable Credentials |
| keyAgreement | Encrypt messages to DID subject |
| capabilityInvocation | Authorize operations on behalf of DID subject |
| capabilityDelegation | Delegate capabilities to another party |


##### 3.7 DID Operations Lifecycle

---

### 7. Verifiable Credentials Integration & Use Cases
##### Ecosystem Participants
##### Verifiable Credential Structure
##### Verifiable Credential Types
* Educational credentials (degrees, certificates)
* Professional certifications
* Identity documents (passports, driver's licenses)
* Health records
* Supply chain attestations
* Financial credentials

#### 5. Industry Use Cases
##### Healthcare
* **Medical records management** - Secure sharing between providers
* **Vaccination verification** - COVID-19 immunity passports
* **Patient data portability** - Consent-controlled data sharing
* **Prescription tracking** - Fraud prevention
##### Supply Chain
* **Product authenticity**, **Chain of custody**, **Cold chain verification**, **Supplier credentials**
##### Government & Public Sector
* **Digital ID for refugees**, **Permanent resident cards**, **Citizen services**, **Voting systems**
##### Education
* **Academic credentials**, **Professional development**, **Skill verification**, **Cross-institutional records**
##### Finance
* **KYC/AML compliance**, **Credit history**, **Cross-border payments**, **DeFi identity**
##### Enterprise
* **Employee credentials**, **B2B authentication**, **Audit trails**, **Vendor management**

#### 6. Privacy & Security Considerations
##### Privacy by Design Principles
1. **Pairwise-pseudonymous DIDs** - Different DIDs for each relationship
2. **Off-chain private data** - Never store PII on public ledgers
3. **Selective disclosure** - Share only necessary information
4. **Zero-knowledge proofs** - Prove attributes without revealing data
##### Security Best Practices
* Key rotation mechanisms, Multi-signature support, Delegation capabilities, Revocation registries.

---

### 8. DID Development in China
##### 8.1 Overview
China has been actively developing distributed digital identity infrastructure with strong government backing, combining government-verified identity with distributed ledger technology.
##### 8.2 Key Players in China
###### CTID (居民身份网络可信凭证)
* **Operator:** Ministry of Public Security First Research Institute.
* **Type:** Software-based solution.
* **Features:** Real identity verification, multi-factor authentication, based on ID card + biometrics.
* **Scale:** 50+ billion data records, 20,000+ requests per second capacity.
###### eID (网络电子身份标识)
* **Operator:** Ministry of Public Security Third Research Institute.
* **Type:** Hardware-based solution.
* **Features:** Smart security chip, online/offline auth, digital signature capabilities loaded on financial IC/SIM cards and smartphones.
##### 8.3 BSN (Blockchain Service Network)
###### Overview
BSN is a global blockchain public infrastructure initiated by the State Information Center, China Mobile, China UnionPay, and Beijing Red Date Technology.
###### BSN-DID Service
###### BSN Real-name DID Service Features
| Feature | Description |
| :--- | :--- |
| Real-name Verification | Based on CTID system (ID + name or ID + name + face recognition) |
| Unified DID | Same person gets same DID across different applications |
| Key Management | Different public keys for different applications |
| Private Key Storage | Held by user or application provider |
| Chain | BSN Yan'an Chain (延安链) |


###### BSN Official Services
| Service | Description |
| :--- | :--- |
| **BSN实名DID服务** | Real-name verified DID issuance |
| **全网分布式域名服务** | Distributed domain name system across all chains |
| **可信区块链运行监测** | Trusted blockchain monitoring platform |
| **BSN官方DDC服务** | Official DDC/NFT contract service |
| **私钥托管服务** | Private key escrow service (with ID recovery) |


##### 8.4 DIDA (分布式数字身份产业联盟)
###### Overview
DIDA is China's first distributed digital identity industry alliance, formed to promote DID technology standards and applications.
###### Key Information
| Aspect | Details |
| :--- | :--- |
| Founded | August 2020 |
| White Paper | First comprehensive DID white paper in China |
| Members | 12 founding council members |
| Focus | Technology standards, industry applications, legal compliance |


###### DIDA's Four Major Tasks
1. **Technical Standards**, 2. **Infrastructure**, 3. **Application Scenarios**, 4. **International Cooperation**.
###### Expert Advisors
Cai Jiren, Chen Jing, Li Jingchun, Liu Duo, Ma Zhitao, Zhan Banghua, Xu Ke.

##### 8.5 National Standards
###### Standard: 区块链和分布式记账技术分布式身份管理系统概述
| Aspect | Details |
| :--- | :--- |
| Standard ID | ISO/TR 23249:2022 (adopted) |
| Committee | TC590 (National Blockchain Standardization Committee) |
| Authority | Ministry of Industry and Information Technology |
| Drafter | China Electronics Standardization Institute |
| Status | Under development |


###### Scope
Classification of distributed identity management systems, basic functions, mainstream system analysis, participant roles, framework definitions.
##### 8.6 China vs International Comparison
| Aspect | China | International (EU/US) |
| :--- | :--- | :--- |
| **Approach** | Government-led, real-name verified | Market-driven, self-sovereign |
| **Identity Source** | CTID/eID (government) | Multiple issuers |
| **Blockchain** | BSN (national infrastructure) | Various (Ethereum, Hyperledger, etc.) |
| **Privacy Model** | Real-name with controlled disclosure | Pseudonymous by default |
| **Standardization** | Adopting ISO standards | W3C, DIF standards |
| **Key Projects** | BSN-DID, DIDA | eIDAS, NIST guidelines |


##### 8.7 Application Areas in China
###### Government Services
Digital identity for citizens, Cross-department data sharing, Administrative service authentication, Social security integration.
###### Financial Services
Bank account opening (KYC), Insurance claims, Securities trading, Cross-border payments.
###### Healthcare
Patient identity management, Medical record sharing, Prescription verification, Health certificate issuance.
###### Cross-border Applications
International travel credentials, Cross-border trade verification, International education credentials.
##### 8.8 Challenges in China
| Challenge | Description |
| :--- | :--- |
| **Adoption** | CTID and eID not yet widely adopted |
| **Privacy Balance** | Balancing real-name requirements with privacy protection |
| **KYC Limitations** | Financial institutions cannot access detailed identity info |
| **Interoperability** | Integration between different identity systems |
| **Hardware Dependency** | eID relies on hardware carriers that can be lost |
| **Cross-platform** | Linking CTID identities with business ecosystems |


##### 8.9 Future Outlook
1. **Standardization**, 2. **Infrastructure**, 3. **Applications**, 4. **International**, 5. **Privacy Enhancement**, 6. **Digital Yuan Integration**.

#### 8. Key Standards & Resources
| Resource | URL |
| :--- | :--- |
| W3C DID Specification | https://www.w3.org/TR/did-1.1/ |
| W3C DID Resolution | https://www.w3.org/TR/did-resolution/ |
| W3C DID Use Cases | https://www.w3.org/TR/did-use-cases/ |
| DID Method Registry | https://w3c.github.io/did-method-registry/ |
| DID Primer | https://w3c-ccg.github.io/did-primer/ |
| Verifiable Credentials | https://www.w3.org/TR/vc-data-model/ |
| BSN Official | https://www.bsnbase.com/ |
| BSN-DID Service | https://did.bsnbase.com/ |
| DIDA White Paper | https://dl.brop.cn/wechat/DIDA/DIDA白皮书.pdf |


---

### 9. Design Goals & Data Models
#### 9. Design Goals (W3C Specification)
| Goal | Description |
| :--- | :--- |
| Decentralization | Eliminate the requirement for centralized authorities |
| Control | Give entities the power to directly control their digital identifiers |
| Privacy | Enable entities to control the privacy of their information |
| Security | Enable sufficient security for requesting parties |
| Proof-based | Enable DID controllers to provide cryptographic proof |
| Discoverability | Make it possible to discover DIDs for other entities |
| Interoperability | Use interoperable standards |
| Portability | Be system- and network-independent |
| Simplicity | Favor a reduced set of simple features |
| Extensibility | Enable extensibility provided it does not hinder interoperability |


#### 10. Data Model Types
| Data Type | Description |
| :--- | :--- |
| map | A finite ordered sequence of key/value pairs |
| list | A finite ordered sequence of items |
| set | A finite ordered sequence of items that does not contain the same item twice |
| datetime | A date and time value |
| string | A sequence of code units |
| integer | A real number without a fractional component |
| double | A real number with a fractional component |
| boolean | A value that is either true or false |
| null | A value used to indicate the lack of a value |


#### 11. Summary: Key Takeaways
##### Technical Understanding
1. **DID Resolution** is the core process.
2. **DID Methods** define how DIDs work on specific infrastructures.
3. **Verification Relationships** define what actions a key can perform.
4. **DID URLs** extend DIDs with paths, queries, and fragments.
##### China's Approach
1. **Government-Led** - CTID and eID backed by Ministry of Public Security.
2. **Real-Name Foundation** - Identity verified against national ID system.
3. **BSN Infrastructure** - National blockchain network for DID services.
4. **DIDA Alliance** - Industry coordination and standardization.
5. **Standards Alignment** - Adopting ISO standards.
##### Global Comparison
| Factor | Western Approach | China Approach |
| :--- | :--- | :--- |
| Philosophy | Self-sovereign identity | Government-verified identity |
| Privacy | Pseudonymous by default | Real-name with privacy controls |
| Infrastructure | Decentralized networks | BSN national infrastructure |
| Adoption Driver | Market demand | Government policy |


---

### 10. Advanced Cryptography (Bilinear Pairing & BBS+)
**Bilinear Pairing** is a special mathematical function that acts as a "bridge" between two different cryptographic groups, enabling advanced privacy features.

##### 1. The Basic Concept: Three Groups
* **Group 1 ($G_1$)**: Points on an elliptic curve (addition).
* **Group 2 ($G_2$)**: Another curve group (addition).
* **Target Group ($G_T$)**: A set of numbers in a finite field (multiplication).
A Bilinear Pairing is $e: G_1 \times G_2 \rightarrow G_T$.

##### 2. The "Magic" Property: Bilinearity
It allows you to move multiplication into the exponent: $e(aP, bQ) = e(P, Q)^{ab}$. Both sides give you the exact same value in $G_T$.

##### 3. Why is this useful? (The "One-Way Bridge")
It allows you to verify relationships between hidden numbers without knowing the numbers themselves.
###### Example Scenario:
If Party A knows $a$ and publishes $A = aP$, and Party B publishes $B = bQ$, they can prove they know $ab$ using pairings ($e(A, B) = e(P, Q)^{ab}$). The verifier confirms the relationship but never learns what $a$ or $b$ are, creating the foundation of **Zero-Knowledge Proofs**.

##### 4. How it enables BBS+ Selective Disclosure
The pairing allows the verifier to check that the "hidden part" balances the equation against the "revealed part" and public key. The verifier can see the result match perfectly without reversing the pairing to calculate hidden messages.

##### 5. Common Curves Used
* **BLS12-381**: The most popular choice today.
* **BN254**: An older standard.
* **KZG Commitments**: Rely heavily on pairings.
##### Summary Analogy
Think of it as a special glass box where the glow changes predictably based on keys inside, but observers cannot see through the glass to find out what the keys actually are.

##### **1. Setup: The Cryptographic Building Blocks**
Requires pairing-friendly curves, a bilinear pairing, public generators ($g_0, g_1, ..., g_k, h$), and public key $w = x \cdot g_2$.
##### **2. Key Generation**
* **Secret Key ($sk$)**: random scalar $x$.
* **Public Key ($pk$)**: $w = x \cdot g_2$.
##### **3. Signing a Message Vector**
To sign $k$ messages, pick randomness $e$ and $s$, compute signature core $A = \left( g_0 \cdot g_1^{m_1} \cdot g_2^{m_2} \cdots g_k^{m_k} \cdot h^s \right)^{\frac{1}{e + x}}$, output $\sigma = (A, e, s)$.
##### **4. Verification (Full Disclosure)**
Recompute LHS ($e(A, w \cdot g_2^e)$) and RHS ($e(g_0 \cdot g_1^{m_1} \cdots g_k^{m_k} \cdot h^s, g_2)$) and check equality.
##### **5. Selective Disclosure: The Magic Step**
###### **Step 1: Randomize the Signature**
Compute $A' = r \cdot A$ to blind the signature.
###### **Step 2: Generate a Zero-Knowledge Proof (ZKP)**
The holder creates a proof that they know hidden messages and randomness.
###### **How the ZKP Works (Simplified)**
Commit to hidden values, prove consistency, and reveal only plaintext values.
###### **Step 3: Verifier Checks**
Checks the ZKP to ensure the pairing equation holds, the holder knows the values, and no information is leaked.
##### **6. Why This Works: The Role of Pairings**
Binding messages, hiding via exponents, blinding, and zero-knowledge.
##### **Example Use Case: Age Verification**
You can prove you are over 18 to a bar by randomizing the signature and revealing only your age.
##### **Key Takeaways**
Multi-Message Signatures, Selective Disclosure, Unlinkability, Privacy, and Efficiency.

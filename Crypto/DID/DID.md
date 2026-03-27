Here is the fully organized guide with all original text, tables, and specific diagram placeholders (empty headings) kept exactly as they appeared in the source document.

### 1. DID (Decentralized Identifier) Research Overview & W3C Standards
#### What is a DID?
A Decentralized Identifier (DID) is a new type of globally unique identifier defined by W3C that enables verifiable, self-sovereign digital identity. Unlike traditional identifiers like email or usernames, DIDs are decentralized (no central authority required), persistent (cannot be taken away administratively), cryptographically verifiable (proof of ownership), and resolvable (can be looked up to discover associated information).

#### How Users Use DID (Real-World Scenario)
###### Simple Analogy
Think of a DID as your **digital passport** that you create and control yourself, rather than one issued by a government. Just like a physical passport proves your identity when traveling, a DID proves your digital identity online—but you own it completely.

###### Real-World Application: Job Application with Verifiable Credentials
Let's walk through a practical scenario where Alice uses her DID to apply for a job:

###### ⚠️ Important Clarification: DID vs Verifiable Credentials
**The DID Document does NOT contain the credentials!** Here's the correct relationship:

**How Each Component Works:**
| Component | What It Contains | Who Creates It | Where It's Stored |
| :--- | :--- | :--- | :--- |
| **DID Document** | Public keys for verification | Alice (or method-specific process) | DID Registry (blockchain, web server, etc.) |
| **Verifiable Credential** | Claims about Alice (degree, experience) | Issuer (University, Employer) | Alice's wallet (credential holder) |
| **Private Key** | Secret key for signing | Alice's wallet | Securely on Alice's device only |


**How Verification Works:**
1. **DID Document = Keys Only** - Contains public keys for cryptographic verification.
2. **Credentials = Claims + Signatures** - Contain actual data (degree, experience) signed by issuers.
3. **Subject Binding** - Each credential references Alice's DID in the credentialSubject.id field.
4. **Issuer Verification** - Anyone can verify by resolving the issuer's DID to get their public key.
5. **Holder Binding** - Alice proves she's the legitimate holder by signing the presentation with her private key.

**Simple Summary: One DID, Many Credentials**
* ✅ **One DID** = One identity identifier (stored on registry).
* ✅ **Many VCs** = Multiple credentials all reference the same DID (stored in wallet).
* ✅ **One-to-Many Relationship** = Single DID : Multiple Verifiable Credentials.

###### Why Verify Alice is the Holder? (Even When We Trust the University)
**The Critical Security Problem:**
**The Solution: Holder Binding Verification**
**The Two-Layer Verification:**
| Layer | What We Verify | Why It Matters | Who We Trust |
| :--- | :--- | :--- | :--- |
| **Layer 1: Issuer** | University's signature on the credential | The degree is authentic and was really issued by the university | University as an institution |
| **Layer 2: Holder** | Alice's signature with her DID's private key | The person presenting the credential is the legitimate owner | Mathematics (cryptography) |


**Real-World Analogy:**
**Key Insight:**
Trusting the issuer means we believe the credential content is true, while verifying the holder means we believe the person presenting it is the real owner; both are necessary for secure identity verification.

**Simple Summary of Responsibilities:**
**One-Sentence Summary:**
The university **attests** that Alice has a degree, but Alice must **prove** she is the person that degree was issued to.

###### How Alice's Signature Proves Her Identity (Cryptographic Proof)
When Alice presents her credentials to the employer, she signs a **Verifiable Presentation** with her private key. The verifier then uses Alice's public key (from her DID document) to verify the signature.

**The Math Behind It:**
**Why This Proves Alice's Identity:**
| What Alice Has | What Verifier Has | Mathematical Property |
| :--- | :--- | :--- |
| Private key (secret) | Public key (from DID document) | Only private key can create signatures that public key can verify |


The verifier knows Alice's DID, her DID document contains her public key, anyone can get that public key, and only Alice has the corresponding private key.
**Verification Flow:**
**What This Prevents (Stolen Credential Attack):**
**Key insight:** The VC proves *"University says Alice has a degree"*, but Alice's signature on the VP proves *"I am the real Alice presenting this credential."*

###### The Challenge (Nonce): Preventing Replay Attacks
The **challenge (nonce)** in the presentation proof prevents replay attacks:
**Replay Attack Without Challenge:**
**With Challenge (Nonce) - Replay Prevented:**
**Why It Works:**
| Mechanism | Purpose |
| :--- | :--- |
| **Challenge = Random nonce** | Unique per session |
| **Alice signs the challenge** | Proves she's participating NOW |
| **Verifier checks signed challenge** | Ensures signature is fresh |

The challenge binds the signature to **this specific session**, making replayed signatures invalid for any other session.

###### DID vs Traditional Digital Certificate: Proving Ownership
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
**Step 1: Employer Sends Request**
**Step 2: Alice Reviews and Selects**
**Step 3: Alice Creates Verifiable Presentation**
**Technical Implementation:**
**Key Points:**
| Feature | How It Works | Benefit |
| :--- | :--- | :--- |
| **Holder Control** | Alice decides what to share | Privacy protection |
| **Selective Disclosure** | Choose specific credentials from wallet | Share only what's needed |
| **No Revealing** | Unselected credentials never leave wallet | Complete privacy for unshared data |
| **Contextual Sharing** | Different presentations for different situations | Right data for right context |


**Real-World Example:**
**Step 4: Verifier Extracts and Verifies the VCs**
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
**Yes!** The verifier extracts the 3 VCs, verifies Alice's signature, verifies each VC's issuer signature, and verifies all VCs belong to Alice.
**One-Sentence Summary:**
The Verifiable Presentation is like an envelope containing 3 VCs; the verifier opens the envelope, checks Alice signed the envelope, and checks each VC is authentic.

---

### 2. How DID/DID Document and VC Are Generated and Issued
###### Part 1: DID and DID Document Generation
**Who Generates the DID/DID Document?**
**Summary Table: Who Does What**
| DID Method | Key Generation | DID Creation | DID Document Creation | Storage/Registry | Service Provider Role |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **did:key** | User | User (algorithmic) | User (deterministic) | None (self-contained) | None - fully self-sovereign |
| **did:web** | Organization | Organization | Organization | Web server (DNS-based) | Organization IT hosts document |
| **did:ethr** | User | User (from address) | Smart contract / Default | Ethereum blockchain | Blockchain network provides registry |
| **did:ion** | User | User (from public key) | Sidetree nodes aggregate | Bitcoin blockchain | ION network nodes help batch/anchor |
| **BSN-DID** | Service Provider | Service Provider | Service Provider | BSN blockchain | BSN provides verified, real-name DID |


**Key Insight:**
Self-Sovereign Methods (did:key, did:ethr, did:ion) mean the user generates everything. Organization Methods (did:web) mean the organization generates and controls the identity. Assisted Methods (BSN-DID) mean a service provider helps generate, but the user still controls keys.

###### Method A: did:key (Self-Generated)
###### Method B: did:web (Domain-Based)
###### Method C: did:ethr (Blockchain-Based)
###### Part 2: Verifiable Credential Generation and Issuance
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
**DID Layer (Still ECDSA/Ed25519)** DID documents contain verification methods that use traditional signatures (did:ethr uses ECDSA, did:key uses Ed25519/ECDSA, did:web uses ECDSA/Ed25519).
**VC Layer (ECDSA → BBS Option)** This is where BBS comes in as an alternative:
###### Why Use BBS for VCs?
| Feature | Traditional (ECDSA/Ed25519) | BBS Signatures | Benefit |
| :--- | :--- | :--- | :--- |
| **Unlinkability** | ❌ Same signature reused | ✅ Fresh proof each time | Prevent tracking across presentations |
| **Selective Disclosure** | ❌ Reveal all or nothing | ✅ Reveal only chosen attributes | Privacy-preserving data sharing |
| **Predicate Proofs** | ❌ Not possible | ✅ Prove statements (e.g., age > 18) without revealing exact value | Minimal disclosure |


###### Example: Alice's Job Application
**Traditional ECDSA:**
**BBS Signatures:**
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
###### 2. DID Authority Guarantees
###### 2.1 Control Proof (Authentication)
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
###### 3.2 Trust Frameworks
Beyond cryptography, real-world trust is established through:
###### 4. Revocation and Status Checking
###### 5. Complete Authority Verification Flow
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
A DID is a URI consisting of three parts:
###### ABNF Grammar (Formal Definition)
According to W3C specification, the DID syntax is defined using ABNF (Augmented Backus-Naur Form):
###### Syntax Components Explained
| Component | Description | Rules | Examples |
| :--- | :--- | :--- | :--- |
| **did** | URI scheme | Fixed string "did:" (lowercase) | did: |
| **method-name** | DID method identifier | Lowercase letters (a-z) and digits (0-9) only; at least 1 character | web, ethr, key, ion, sov |
| **method-specific-id** | Unique identifier within method | Letters, digits, ., -, _, and percent-encoded characters; can contain : as namespace separator | 123456, abc123, EiAnKD8... |


###### Character Set Details
**Allowed Characters in method-name:** Lowercase letters and digits are allowed, but uppercase and special characters are not allowed.
**Allowed Characters in method-specific-id:**
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
**Resolution Process:**
1. Parse the DID to extract domain and path.
2. Construct HTTPS URL.
3. Fetch DID document via HTTPS with TLS verification.
4. Return the JSON document.
**DID Document Storage:**
* Stored at: https://<domain>/.well-known/did.json (for domain-level DID) or https://<domain>/<path>/did.json (for path-based DIDs).
**Example:**
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
**Key Features:** Uses ERC-1056 smart contract, any Ethereum address can be a DID implicitly, supports key rotation and delegation.
**Resolution Process:**
1. Extract Ethereum address from DID.
2. Query ERC-1056 smart contract for identity owner.
3. Fetch events (DIDOwnerChanged, etc.).
4. Build DID document from contract state and events.
**Network Support:**
* Ethereum Mainnet, Goerli Testnet, Other EVM chains.
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
**Generation Process:** Generate key pair, encode public key with multicodec prefix, encode result with Multibase, DID = did:key:<encoded-value>.
**Supported Key Types:** Ed25519, X25519, Secp256k1, P-256, P-384.
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
**Architecture:**
**Key Features:** Scalable, no token required, batch operations, censorship resistant.
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
**Key Features:** Built-in governance, privacy-preserving, supports anonymous credentials.
**Resolution Process:** Parse the DID, query the Indy ledger for the NYM transaction, build DID document.
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
**Key Features:** No registry required, created and exchanged directly between peers, supports key rotation.
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
**Resolution Process:** Decode base64url-encoded value, parse as JWK, generate DID document.
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
Example:
###### Case Sensitivity
Method name is case-insensitive (but MUST be lowercase), and Method-specific-id is case-sensitive.
##### W3C Status
DID 1.0 was approved as an official W3C Recommendation in June 2022.

---

### 5. Core Architecture
##### 2.1 Architecture Overview
##### 2.2 Core Components Explained
###### DID Subject (被标识主体)
**Definition:** The entity that the DID identifies (a person, organization, device, abstract entity, etc.).
**Key Points:** The DID subject is what the DID refers to, does not necessarily control the DID, and can be the same as the controller.

###### DID Controller (DID 控制者)
**Definition:** The entity that has the authority to make changes to the DID document.
**Key Points:** Controls the DID document, owns private keys, can be the same as the subject, and a DID can have multiple controllers.

###### DID Document (DID 文档)
**Definition:** A JSON-LD document that contains information associated with a DID, including cryptographic material and service endpoints.
**Structure:**

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

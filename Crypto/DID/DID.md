# DID (Decentralized Identifier) Research

## 1. Overview & W3C Standards

### What is a DID?

A Decentralized Identifier (DID) is a new type of globally unique identifier defined by W3C that enables verifiable, self-sovereign digital identity. Unlike traditional identifiers (email, usernames), DIDs are:

- **Decentralized** - No central authority required
- **Persistent** - Cannot be taken away administratively
- **Cryptographically verifiable** - Proof of ownership
- **Resolvable** - Can be looked up to discover associated information

### DID Syntax

#### Basic Structure

A DID is a URI (Uniform Resource Identifier) consisting of three parts:

```
did:method:method-specific-id
│    │      │
│    │      └── Method-Specific Identifier (unique within the method)
│    └───────── Method Name (identifies the DID method)
└────────────── URI Scheme (always "did")
```

#### ABNF Grammar (Formal Definition)

According to W3C specification, the DID syntax is defined using ABNF (Augmented Backus-Naur Form):

```abnf
did                 = "did:" method-name ":" method-specific-id
method-name         = 1*method-char
method-char         = %x61-7A / DIGIT    ; lowercase letters a-z or digits 0-9
method-specific-id  = *( *idchar ":" ) 1*idchar
idchar              = ALPHA / DIGIT / "." / "-" / "_" / pct-encoded
pct-encoded         = "%" HEXDIG HEXDIG   ; percent-encoded character
```

#### Syntax Components Explained

| Component | Description | Rules | Examples |
|-----------|-------------|-------|----------|
| **did** | URI scheme | Fixed string `"did:"` (lowercase) | `did:` |
| **method-name** | DID method identifier | Lowercase letters (a-z) and digits (0-9) only; at least 1 character | `web`, `ethr`, `key`, `ion`, `sov` |
| **method-specific-id** | Unique identifier within method | Letters, digits, `.`, `-`, `_`, and percent-encoded characters; can contain `:` as namespace separator | `123456`, `abc123`, `EiAnKD8-jfdd0MDcZUjAbRgaThBrMxPTFOxcnfJhI7dKgA` |

#### Character Set Details

**Allowed Characters in method-name:**
```
a b c d e f g h i j k l m n o p q r s t u v w x y z 0 1 2 3 4 5 6 7 8 9
```
- ✅ Lowercase letters (a-z)
- ✅ Digits (0-9)
- ❌ Uppercase letters (NOT allowed)
- ❌ Special characters (NOT allowed)

**Allowed Characters in method-specific-id:**
```
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z (uppercase)
a b c d e f g h i j k l m n o p q r s t u v w x y z (lowercase)
0 1 2 3 4 5 6 7 8 9 (digits)
. - _ (special characters)
% XX (percent-encoded, where XX is hexadecimal)
: (colon, for namespace separation)
```

#### Real-World Examples by Method

| DID Method | Example DID | Explanation |
|------------|-------------|-------------|
| `did:web` | `did:web:example.com:user:alice` | Identity anchored to domain `example.com` |
| `did:ethr` | `did:ethr:0xE6Fe788d8ca214A080b0f6aC7F48480b2AEfa9a6` | Identity on Ethereum blockchain |
| `did:key` | `did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK` | Self-contained key-based identity |
| `did:ion` | `did:ion:EiAnKD8-jfdd0MDcZUjAbRgaThBrMxPTFOxcnfJhI7dKgA` | Bitcoin-anchored identity (ION) |
| `did:sov` | `did:sov:WRfXPg8dantKVubE3HX8pw` | Identity on Sovrin/Hyperledger Indy |
| `did:peer` | `did:peer:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK` | Peer-to-peer private identity |
| `did:jwk` | `did:jwk:eyJrdHkiOiJFQyIsImNydiI6IlAtMjU2In0` | JSON Web Key based identity |

#### DID Methods Explained

##### did:web - DNS-Based Decentralized Identifier

**Overview:** `did:web` uses existing web infrastructure (DNS + HTTPS) to host DID documents, making it one of the simplest DID methods to implement.

**How It Works:**
```
did:web:example.com:user:alice
    │       │         │
    │       │         └── Path: /user/alice/did.json
    │       └──────────── Domain: example.com
    └──────────────────── Method: web
```

**Resolution Process:**
1. Parse the DID to extract domain and path
2. Construct HTTPS URL: `https://example.com/user/alice/did.json`
3. Fetch DID document via HTTPS with TLS verification
4. Return the JSON document

**DID Document Storage:**
- Stored at: `https://<domain>/.well-known/did.json` (for domain-level DID)
- Or: `https://<domain>/<path>/did.json` (for path-based DIDs)

**Example:**
```
DID:  did:web:example.com
URL:  https://example.com/.well-known/did.json

DID:  did:web:example.com:user:alice
URL:  https://example.com/user/alice/did.json
```

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ⚠️ Low - Depends on DNS (centralized) and web server |
| **Cost** | ✅ Free - No blockchain fees |
| **Setup** | ✅ Easy - Just host a JSON file |
| **Key Rotation** | ✅ Supported - Update the JSON file |
| **Deactivation** | ✅ Supported - Remove the JSON file |
| **Best For** | Organizations, enterprises, domain-based identity |
| **Limitations** | Depends on domain ownership; DNS can be seized |

---

##### did:ethr - Ethereum-Based Decentralized Identifier

**Overview:** `did:ethr` leverages Ethereum blockchain and smart contracts (ERC-1056) for identity management with no on-chain registration required.

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
|--------|-------------|
| **Decentralization** | ✅ High - Uses Ethereum blockchain |
| **Cost** | ⚠️ Gas fees for updates (free to create) |
| **Setup** | ✅ Easy - Just generate an Ethereum key pair |
| **Key Rotation** | ✅ Supported - Via smart contract |
| **Deactivation** | ✅ Supported - Set owner to 0x0 |
| **Best For** | Web3 apps, DeFi, Ethereum ecosystem |
| **Limitations** | Gas fees for updates; depends on Ethereum |

---

##### did:key - Self-Contained Cryptographic Identifier

**Overview:** `did:key` is a purely generative DID method where the DID and DID document are derived directly from a cryptographic public key. No registry or network required.

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

**Supported Key Types:**
| Key Type | Prefix | Example DID Start |
|----------|--------|-------------------|
| Ed25519 | `z6Mk` | `did:key:z6Mk...` |
| X25519 | `z6LS` | `did:key:z6LS...` |
| Secp256k1 | `zQ3s` | `did:key:zQ3s...` |
| P-256 | `zDn` | `did:key:zDn...` |
| P-384 | `z82` | `did:key:z82...` |

**Resolution Process:**
1. Decode the multibase-encoded value
2. Extract key type from multicodec prefix
3. Generate DID document from the public key

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ✅ Highest - No registry needed |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Simplest - Just generate a key |
| **Key Rotation** | ❌ Not supported |
| **Deactivation** | ❌ Not supported |
| **Best For** | Ephemeral identities, offline use, testing, temporary credentials |
| **Limitations** | Cannot update or deactivate; compromised keys are permanent |

---

##### did:ion - Bitcoin-Anchored Scalable Identifier

**Overview:** `did:ion` (Identity Overlay Network) is a Layer 2 network built on Bitcoin using the Sidetree protocol, designed for high scalability without requiring special tokens.

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
│                    ION Network                       │
│  ┌─────────────────────────────────────────────┐    │
│  │           Sidetree Protocol                  │    │
│  │  • Batch DID operations                      │    │
│  │  • Content-addressable storage (IPFS)        │    │
│  │  • Deterministic resolution                  │    │
│  └─────────────────────────────────────────────┘    │
│                        ▲                             │
│                        │ Anchoring                   │
│  ┌─────────────────────┴───────────────────────┐    │
│  │           Bitcoin Blockchain                 │    │
│  │  • Immutable timestamping                    │    │
│  │  • Proof of existence                        │    │
│  └─────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

**Key Features:**
- **Scalable:** Thousands of DID operations per second
- **No token required:** Uses Bitcoin only for anchoring
- **Batch operations:** Multiple DIDs in single Bitcoin transaction
- **Censorship resistant:** DIDs can only be deactivated by owners

**Resolution Process:**
1. Decode the Sidetree identifier
2. Find anchoring transaction on Bitcoin
3. Fetch DID operation data from IPFS/content-addressable storage
4. Apply operations in chronological order
5. Return resulting DID document

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ✅ Very High - Bitcoin + distributed storage |
| **Cost** | ⚠️ Low - Batch operations reduce Bitcoin fees |
| **Setup** | ⚠️ Moderate - Requires ION node or resolver |
| **Key Rotation** | ✅ Supported |
| **Deactivation** | ✅ Supported |
| **Best For** | Large-scale public identity, enterprise SSI |
| **Limitations** | More complex setup; depends on Bitcoin |

---

##### did:sov - Hyperledger Indy-Based Identifier

**Overview:** `did:sov` operates on Hyperledger Indy, a permissioned distributed ledger specifically designed for decentralized identity with built-in privacy features.

**How It Works:**
```
did:sov:WRfXPg8dantKVubE3HX8pw
    │     │
    │     └── Indy network identifier
    └──────── Method: sov
```

**Key Features:**
- Built-in governance framework
- Privacy-preserving by design
- Supports anonymous credentials (AnonCreds)
- Purpose-built for identity use cases

**Resolution Process:**
1. Parse the DID
2. Query the Indy ledger for the NYM transaction
3. Build DID document from ledger state

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ⚠️ Medium - Permissioned network |
| **Cost** | ⚠️ Transaction fees (low) |
| **Setup** | ⚠️ Moderate - Requires Indy network access |
| **Key Rotation** | ✅ Supported |
| **Deactivation** | ✅ Supported |
| **Best For** | Enterprise identity, regulated industries, governments |
| **Limitations** | Permissioned network; less decentralized than public chains |

---

##### did:peer - Private Peer-to-Peer Identifier

**Overview:** `did:peer` is designed for private, pairwise relationships where DIDs don't need to be registered on any network. Perfect for direct peer-to-peer interactions.

**How It Works:**
```
did:peer:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK
    │       │
    │       └── Encoded genesis document
    └────────── Method: peer
```

**Key Features:**
- No registry required
- Created and exchanged directly between peers
- Supports key rotation through versioning
- Ideal for private relationships

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ✅ Very High - No registry |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Easy - Generate and share |
| **Key Rotation** | ✅ Supported (versioned) |
| **Deactivation** | ✅ Supported |
| **Best For** | Private relationships, agent-to-agent communication |
| **Limitations** | Not resolvable publicly; requires secure exchange |

---

##### did:jwk - JSON Web Key Identifier

**Overview:** `did:jwk` encodes a JSON Web Key (JWK) directly in the DID, creating a self-contained identifier similar to `did:key` but using the standard JWK format.

**How It Works:**
```
did:jwk:eyJrdHkiOiJFQyIsImNydiI6IlAtMjU2In0
    │     │
    │     └── Base64url-encoded JWK
    └──────── Method: jwk
```

**Resolution Process:**
1. Decode the base64url-encoded value
2. Parse as JWK (JSON Web Key)
3. Generate DID document from JWK

| Aspect | Description |
|--------|-------------|
| **Decentralization** | ✅ High - No registry needed |
| **Cost** | ✅ Free - No network calls |
| **Setup** | ✅ Easy - Encode a JWK |
| **Key Rotation** | ❌ Not supported |
| **Deactivation** | ❌ Not supported |
| **Best For** | Quick prototypes, testing, JWT/JWK ecosystems |
| **Limitations** | Cannot update or deactivate; longer DIDs than did:key |

---

#### Quick Comparison Table

| Method | Registry | Key Rotation | Deactivation | Cost | Best Use Case |
|--------|----------|--------------|--------------|------|---------------|
| **did:web** | DNS/HTTPS | ✅ | ✅ | Free | Organizational identity |
| **did:ethr** | Ethereum | ✅ | ✅ | Gas fees | Web3, DeFi |
| **did:key** | None | ❌ | ❌ | Free | Ephemeral, offline |
| **did:ion** | Bitcoin | ✅ | ✅ | Low | Large-scale SSI |
| **did:sov** | Indy | ✅ | ✅ | Low | Enterprise, regulated |
| **did:peer** | None | ✅ | ✅ | Free | Private P2P |
| **did:jwk** | None | ❌ | ❌ | Free | Testing, JWK ecosystem |

#### DID URL Syntax

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
|-----------|--------|---------|---------|
| Path | `/` | Navigate to resources | `did:example:123/credentials` |
| Query | `?` | Pass parameters | `did:example:123?versionId=1` |
| Fragment | `#` | Reference sub-resource | `did:example:123#key-1` |

#### Valid vs Invalid DIDs

| DID | Valid? | Reason |
|-----|--------|--------|
| `did:web:example.com` | ✅ Valid | Correct format |
| `did:ethr:0x123abc` | ✅ Valid | Lowercase method name |
| `did:Web:example.com` | ❌ Invalid | Uppercase in method name |
| `did:web:` | ❌ Invalid | Missing method-specific-id |
| `did::123` | ❌ Invalid | Empty method name |
| `did:web:example.com#key-1` | ✅ Valid | DID URL with fragment |
| `did:example:123:456:789` | ✅ Valid | Multiple colons allowed in method-specific-id |
| `did:my-method:abc123` | ✅ Valid | Method name with hyphen |
| `did:my_method:abc123` | ✅ Valid | Method name with underscore |

#### Percent-Encoding

Special characters must be percent-encoded:

| Character | Encoded | Example |
|-----------|---------|---------|
| Space | `%20` | `did:web:example.com:user%20name` |
| `#` (in id) | `%23` | `did:example:tag%23value` |
| `?` (in id) | `%3F` | `did:example:query%3Fparam` |

#### Namespace Hierarchy in method-specific-id

The colon `:` within method-specific-id creates hierarchical namespaces:

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

#### Case Sensitivity

- **Method name**: Case-insensitive (but MUST be lowercase)
- **Method-specific-id**: Case-sensitive (depends on method specification)

```
did:web:example.com  ✅ Valid
did:WEB:example.com  ❌ Invalid (uppercase method)
did:web:Example.com  ✅ Valid (case-sensitive in method-specific-id)
```

### W3C Status

DID 1.0 was approved as an official W3C Recommendation in June 2022, making it an open web standard.

---

## 2. Core Architecture

### 2.1 Architecture Overview

The DID architecture consists of several interconnected components that work together to enable decentralized identity management. Here's a comprehensive view:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        DID Ecosystem Architecture                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌──────────────┐                          ┌──────────────────────┐       │
│    │ DID Subject  │◄────── identifies ──────│         DID          │       │
│    │  (Entity)    │                          │  did:example:123     │       │
│    └──────────────┘                          └──────────┬───────────┘       │
│           │                                            │                    │
│           │                                            │ resolves to        │
│           │                                            ▼                    │
│           │                               ┌──────────────────────┐           │
│           │                               │    DID Document      │           │
│           │                               │  ┌────────────────┐  │           │
│           │                               │  │ id             │  │           │
│           │                               │  │ controller     │  │           │
│           │                               │  │ verification   │  │           │
│           │                               │  │ authentication │  │           │
│           │                               │  │ services       │  │           │
│           │                               │  └────────────────┘  │           │
│           │                               └──────────┬───────────┘           │
│           │                                          │                        │
│           │                                          │ stored in              │
│           │                                          ▼                        │
│    ┌──────┴─────────┐                   ┌──────────────────────┐             │
│    │ DID Controller │◄── controls ──────│ Verifiable Data      │             │
│    │  (Entity)      │                   │ Registry             │             │
│    └────────────────┘                   │ (Blockchain/DB/etc)  │             │
│                                         └──────────────────────┘             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Core Components Explained

#### DID Subject (被标识主体)

**Definition:** The entity that the DID identifies. This can be:
- A person (individual)
- An organization
- A device (IoT, computer)
- An abstract entity (concept, data model)
- Any thing that needs to be identified

**Key Points:**
- The DID subject is what the DID refers to
- The subject does NOT control the DID (that's the controller)
- In many cases, the subject and controller are the same entity
- Example: A person's digital identity, a company's organizational identity

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

---

#### DID Controller (DID 控制者)

**Definition:** The entity that has the authority to make changes to the DID document.

**Key Points:**
- Controls the DID document (update, delete operations)
- Owns the private keys for the DID
- Can be the same as the DID subject (self-controlled)
- Can be different (guardianship, organizational control)
- A DID can have multiple controllers

```
Controller Relationships:
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Case 1: Self-Controlled (Subject = Controller)        │
│  ┌─────────────┐                                       │
│  │   Person    │── owns ──▶ DID ◀── controls ──┐      │
│  │  (Alice)    │                              │      │
│  └─────────────┘                              │      │
│                                      (same entity)    │
│                                                         │
│  Case 2: Guardianship (Subject ≠ Controller)           │
│  ┌─────────────┐                              │        │
│  │   Parent    │── controls ──▶ DID ◀── identifies    │
│  │             │                              │        │
│  └─────────────┘                              │        │
│                                      ┌───────┴───┐    │
│                                      │   Child   │    │
│                                      │ (Subject) │    │
│                                      └───────────┘    │
│                                                         │
│  Case 3: Multi-Controller                              │
│  ┌─────────────┐        ┌─────────────┐               │
│  │ Controller 1│───────▶│     DID     │◀──────┐       │
│  └─────────────┘        └─────────────┘       │       │
│                                 │              │       │
│                         ┌───────┴───────┐      │       │
│                         │   Subject     │      │       │
│                         │ (Organization)│      │       │
│                         └───────────────┘      │       │
│  ┌─────────────┐                               │       │
│  │ Controller 2│───────────────────────────────┘       │
│  └─────────────┘                                       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

#### DID Document (DID 文档)

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

---

#### Verifiable Data Registry (可验证数据注册表)

**Definition:** A system (blockchain, distributed database, etc.) where DIDs and DID documents are stored and can be resolved from.

**Types of Registries:**

| Registry Type | Examples | Characteristics |
|---------------|----------|-----------------|
| **Blockchain** | Bitcoin, Ethereum, Hyperledger Indy | Immutable, decentralized, consensus-based |
| **Distributed Database** | IPFS, Distributed Hash Tables | Content-addressable, peer-to-peer |
| **Web Infrastructure** | DNS + HTTPS (did:web) | Existing infrastructure, centralized DNS |
| **Self-Contained** | None (did:key, did:jwk) | No registry, derived from key |

```
Registry Architecture Examples:
┌────────────────────────────────────────────────────────────────────┐
│                                                                    │
│  Blockchain-Based (did:ethr, did:ion, did:sov)                    │
│  ┌──────────────────────────────────────────────────────────┐     │
│  │                    Blockchain Network                     │     │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │     │
│  │  │  Node 1  │  │  Node 2  │  │  Node 3  │  │  Node N  │ │     │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘ │     │
│  │       │             │             │             │        │     │
│  │       └─────────────┴──────┬──────┴─────────────┘        │     │
│  │                            │                             │     │
│  │                    ┌───────┴───────┐                     │     │
│  │                    │ DID Registry  │                     │     │
│  │                    │   (on-chain)  │                     │     │
│  │                    └───────────────┘                     │     │
│  └──────────────────────────────────────────────────────────┘     │
│                                                                    │
│  Web-Based (did:web)                                              │
│  ┌──────────────────────────────────────────────────────────┐     │
│  │     DNS        ──────▶    Web Server                      │     │
│  │  ┌─────────┐           ┌────────────────────┐            │     │
│  │  │.com DNS │           │ example.com/did.json│            │     │
│  │  └─────────┘           └────────────────────┘            │     │
│  └──────────────────────────────────────────────────────────┘     │
│                                                                    │
│  Self-Contained (did:key)                                         │
│  ┌──────────────────────────────────────────────────────────┐     │
│  │                    No Registry Needed                     │     │
│  │                                                          │     │
│  │    DID ──────────────▶ DID Document                      │     │
│  │    (derived from key)  (generated from key)              │     │
│  └──────────────────────────────────────────────────────────┘     │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

### 2.3 DID Document Properties Deep Dive

#### Property Categories

```
DID Document Properties
├── Core Properties
│   ├── id (required) ──────────── The DID itself
│   ├── controller (optional) ──── Who can modify this document
│   └── alsoKnownAs (optional) ─── Other identifiers for this subject
│
├── Verification Methods
│   └── verificationMethod ─────── Cryptographic keys
│
├── Verification Relationships
│   ├── authentication ─────────── Keys for proving control
│   ├── assertionMethod ────────── Keys for issuing credentials
│   ├── keyAgreement ───────────── Keys for encryption/key exchange
│   ├── capabilityInvocation ───── Keys for invoking capabilities
│   └── capabilityDelegation ───── Keys for delegating capabilities
│
└── Services
    └── service ────────────────── Service endpoints
```

#### Verification Methods Explained

**Definition:** Cryptographic material (public keys) that can be used to verify proofs.

```json
{
  "verificationMethod": [{
    "id": "did:example:123#key-1",
    "type": "Multikey",
    "controller": "did:example:123",
    "publicKeyMultibase": "z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK"
  }]
}
```

**Verification Method Properties:**

| Property | Required | Description |
|----------|----------|-------------|
| `id` | Yes | Unique identifier (DID URL) |
| `type` | Yes | Key type (Multikey, JsonWebKey2020, etc.) |
| `controller` | Yes | DID that controls this key |
| `publicKeyMultibase` | No | Multibase-encoded public key |
| `publicKeyJwk` | No | JWK format public key |

**Common Verification Method Types:**

| Type | Algorithm | Use Case |
|------|-----------|----------|
| `Multikey` | Ed25519, secp256k1, etc. | General purpose |
| `JsonWebKey2020` | RSA, EC (P-256, etc.) | JWT/JWS compatibility |
| `Ed25519VerificationKey2020` | Ed25519 | Digital signatures |
| `EcdsaSecp256k1VerificationKey2019` | secp256k1 | Ethereum, Bitcoin |

---

#### Verification Relationships Explained

**Definition:** Relationships between a DID subject and its verification methods, defining the purpose of each key.

```
Verification Relationships:
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                    DID Document                              │   │
│  │                                                              │   │
│  │  verificationMethod:                                         │   │
│  │    ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐      │   │
│  │    │  Key 1  │  │  Key 2  │  │  Key 3  │  │  Key 4  │      │   │
│  │    └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘      │   │
│  │         │            │            │            │            │   │
│  └─────────┼────────────┼────────────┼────────────┼────────────┘   │
│            │            │            │            │                 │
│            ▼            ▼            ▼            ▼                 │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ authentication ────────────▶ Key 1, Key 2                   │   │
│  │ assertionMethod ───────────▶ Key 1                          │   │
│  │ keyAgreement ──────────────▶ Key 3                          │   │
│  │ capabilityInvocation ──────▶ Key 2                          │   │
│  │ capabilityDelegation ──────▶ Key 4                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Detailed Purpose of Each Relationship:**

| Relationship | Purpose | Example Use Case |
|--------------|---------|------------------|
| `authentication` | Prove control of DID | Login, access control |
| `assertionMethod` | Issue Verifiable Credentials | University issuing diplomas |
| `keyAgreement` | Encrypt/decrypt messages | Secure messaging, key exchange |
| `capabilityInvocation` | Authorize actions | Smart contract calls, API access |
| `capabilityDelegation` | Delegate authority | Authorizing another party to act |

---

#### Service Endpoints Explained

**Definition:** URLs where services related to the DID subject can be accessed.

```json
{
  "service": [{
    "id": "did:example:123#messaging",
    "type": "DIDCommMessaging",
    "serviceEndpoint": "https://example.com/message",
    "accept": ["didcomm/v2"],
    "routingKeys": ["z6MkhaXg..."]
  }, {
    "id": "did:example:123#hub",
    "type": "IdentityHub",
    "serviceEndpoint": ["https://hub1.example.com/", "https://hub2.example.com/"]
  }]
}
```

**Common Service Types:**

| Service Type | Purpose |
|--------------|---------|
| `DIDCommMessaging` | Secure peer-to-peer messaging |
| `IdentityHub` | Personal data storage |
| `LinkedDomains` | Linked websites/domains |
| `VerifiableCredentialService` | VC issuance/verification |

---

### 2.4 How Components Work Together

#### Complete DID Lifecycle Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        DID Lifecycle                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. CREATE                                                                  │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                     │
│  │ Generate    │───▶│ Create DID  │───▶│ Store DID   │                     │
│  │ Key Pair    │    │ Document    │    │ Document    │                     │
│  └─────────────┘    └─────────────┘    └──────┬──────┘                     │
│                                                │                            │
│                                                ▼                            │
│                                       ┌─────────────────┐                   │
│                                       │ Verifiable Data │                   │
│                                       │    Registry     │                   │
│                                       └─────────────────┘                   │
│                                                                             │
│  2. RESOLVE (Read)                                                          │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                     │
│  │ Query DID   │───▶│ Lookup in   │───▶│ Return DID  │                     │
│  │             │    │ Registry    │    │ Document    │                     │
│  └─────────────┘    └─────────────┘    └─────────────┘                     │
│                                                                             │
│  3. UPDATE                                                                  │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                     │
│  │ Authenticate│───▶│ Modify DID  │───▶│ Store New   │                     │
│  │ (prove ctrl)│    │ Document    │    │ Version     │                     │
│  └─────────────┘    └─────────────┘    └─────────────┘                     │
│                                                                             │
│  4. DEACTIVATE                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                     │
│  │ Authenticate│───▶│ Mark DID as │───▶│ DID is No   │                     │
│  │ (prove ctrl)│    │ Deactivated │    │ Longer Valid│                     │
│  └─────────────┘    └─────────────┘    └─────────────┘                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

#### Authentication Flow Example

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DID Authentication Flow                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Alice (DID Controller)                Verifier (e.g., Website)            │
│  ┌─────────────────┐                   ┌─────────────────┐                  │
│  │                 │                   │                 │                  │
│  │  did:web:alice  │                   │    Service      │                  │
│  │  Private Key    │                   │                 │                  │
│  │                 │                   │                 │                  │
│  └────────┬────────┘                   └────────┬────────┘                  │
│           │                                     │                           │
│           │  1. Request Access                  │                           │
│           │ ◀───────────────────────────────────│                           │
│           │                                     │                           │
│           │  2. Challenge (nonce)               │                           │
│           │ ◀───────────────────────────────────│                           │
│           │                                     │                           │
│           │  3. Resolve did:web:alice           │                           │
│           │ ────────────────────────────────────▶                           │
│           │                                     │                           │
│           │  4. Return DID Document             │                           │
│           │ ◀───────────────────────────────────│                           │
│           │    (contains public key)            │                           │
│           │                                     │                           │
│           │  5. Sign challenge with private key │                           │
│           │ ────────────────────────────────────▶                           │
│           │                                     │                           │
│           │  6. Verify signature with public key│                           │
│           │    ✅ Authentication Successful!    │                           │
│           │                                     │                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 2.5 Key Components Summary

| Component | Description | Required? |
|-----------|-------------|-----------|
| `id` | The DID itself (identifier) | ✅ Yes |
| `controller` | Who controls the DID document | No (defaults to self) |
| `alsoKnownAs` | Other identifiers for this subject | No |
| `verificationMethod` | Cryptographic public keys | No |
| `authentication` | Keys for proving DID control | No |
| `assertionMethod` | Keys for issuing credentials | No |
| `keyAgreement` | Keys for encryption | No |
| `capabilityInvocation` | Keys for invoking actions | No |
| `capabilityDelegation` | Keys for delegating authority | No |
| `service` | Service endpoints | No |

---

## 3. DID Methods Comparison

| DID Method | Infrastructure | Decentralization | Scalability | Key Features | Best For |
|------------|---------------|------------------|-------------|--------------|----------|
| **did:ion** | Bitcoin (anchoring) | High | High | Batched operations, cost-effective | Large-scale public identity |
| **did:ethr** | Ethereum | High | Medium | Smart contracts, key rotation | Web3, dApps, DeFi |
| **did:sov** | Hyperledger Indy | Medium (permissioned) | Medium | Privacy features, governance | Enterprise, regulated industries |
| **did:web** | DNS/Web servers | Low | High | Simple, existing infrastructure | Organizational identity |
| **did:key** | None (self-contained) | High | High | Stateless, portable | Offline systems, temporary IDs |
| **did:peer** | P2P networks | High | High | Private, no registry | Pairwise relationships |
| **did:dht** | Distributed Hash Table | High | High | Decentralized storage | Quick identity lookup |
| **did:jwk** | JSON Web Key | High | High | Self-contained, simple | Simple verification, testing |

### Selection Guidelines

- **Simplicity first:** Start with `did:key` or `did:web`
- **Maximum decentralization:** `did:ion` or `did:ethr`
- **Enterprise compliance:** `did:sov`
- **Private relationships:** `did:peer`
- **Quick prototyping:** `did:jwk`

---

## 4. How DID Works: Technical Deep Dive

### 4.1 DID Resolution Process

DID Resolution is the process of obtaining a DID document for a given DID. This is one of four required operations (Create, Read/Resolve, Update, Deactivate).

#### Resolution Function

```
resolve(did, resolutionOptions) →
   « didResolutionMetadata, didDocument, didDocumentMetadata »
```

#### Step-by-Step Resolution Algorithm

```
┌─────────────────┐
│   Input: DID    │
│   + Options     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 1. Validate DID │
│    Syntax       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 2. Identify DID │
│    Method       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 3. Execute      │
│    Method-Spec  │
│    Resolution   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ 4. Return DID   │
│    Document +   │
│    Metadata     │
└─────────────────┘
```

#### Resolution Inputs

| Input | Description |
|-------|-------------|
| `did` | The DID to resolve (required) |
| `resolutionOptions` | Accept, expandRelativeUrls, etc. (optional) |

#### Resolution Outputs

| Output | Description |
|--------|-------------|
| `didResolutionMetadata` | Metadata about the resolution process (contentType, error, etc.) |
| `didDocument` | The resolved DID document (or null if error) |
| `didDocumentMetadata` | Metadata about the DID document (created, updated, versionId, deactivated, etc.) |

### 4.2 DID URL Dereferencing

DID URL Dereferencing retrieves a resource for a given DID URL (which may include path, query, and fragment).

#### Dereferencing Function

```
dereference(didUrl, dereferenceOptions) →
   « dereferencingMetadata, contentStream, contentMetadata »
```

#### DID URL Components

```
did:example:123456/path/to/resource?query=value#fragment
│              │                │           │
│              │                │           └── Fragment (identify sub-resource)
│              │                └────────────── Query (parameters)
│              └─────────────────────────────── Path (resource location)
└────────────────────────────────────────────── DID (base identifier)
```

### 4.3 Common DID Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `service` | Select service by ID | `did:example:123?service=files` |
| `serviceType` | Select service by type | `did:example:123?serviceType=LinkedDomains` |
| `relativeRef` | Relative URI at service endpoint | `did:example:123?service=files&relativeRef=/doc.pdf` |
| `versionId` | Specific DID document version | `did:example:123?versionId=1` |
| `versionTime` | DID document at timestamp | `did:example:123?versionTime=2021-05-10T17:00:00Z` |
| `hl` | Resource hash for integrity | `did:example:123?hl=zQmWvQx...` |

### 4.4 DID Document Metadata Properties

| Property | Description |
|----------|-------------|
| `created` | Timestamp when DID was created |
| `updated` | Timestamp of last update |
| `deactivated` | Whether DID is deactivated (true/false) |
| `nextUpdate` | Timestamp of next scheduled update |
| `versionId` | Version identifier of DID document |
| `nextVersionId` | Version identifier of next version |
| `equivalentId` | Logically equivalent DIDs |
| `canonicalId` | Canonical DID for the subject |

### 4.5 Authentication Flow

```
┌──────────┐                              ┌──────────┐
│  Holder  │                              │ Verifier │
└────┬─────┘                              └────┬─────┘
     │                                         │
     │  1. Present DID + Signature             │
     │────────────────────────────────────────▶│
     │                                         │
     │                        2. Resolve DID   │
     │                           ◀─────────────│
     │                                         │
     │                    3. Get Public Key    │
     │                       from DID Document │
     │                           ◀─────────────│
     │                                         │
     │                    4. Verify Signature  │
     │                       using Public Key  │
     │                           ◀─────────────│
     │                                         │
     │  5. Authentication Result               │
     │◀────────────────────────────────────────│
     │                                         │
```

### 4.6 Verification Relationships

| Relationship | Purpose |
|--------------|---------|
| `authentication` | Prove control of DID (login, authentication) |
| `assertionMethod` | Issue Verifiable Credentials |
| `keyAgreement` | Encrypt messages to DID subject |
| `capabilityInvocation` | Authorize operations on behalf of DID subject |
| `capabilityDelegation` | Delegate capabilities to another party |

### 4.7 DID Operations Lifecycle

```
                    ┌────────────┐
                    │   CREATE   │
                    │ (Generate) │
                    └─────┬──────┘
                          │
                          ▼
                    ┌────────────┐
         ┌─────────│   ACTIVE   │──────────┐
         │         │    DID     │          │
         │         └─────┬──────┘          │
         │               │                 │
         ▼               ▼                 ▼
   ┌──────────┐   ┌──────────┐      ┌───────────┐
   │  UPDATE  │   │  RESOLVE │      │  ROTATE   │
   │ (Modify) │   │  (Read)  │      │   Keys    │
   └────┬─────┘   └──────────┘      └───────────┘
        │
        │ (if needed)
        ▼
   ┌────────────┐
   │ DEACTIVATE │
   │  (Revoke)  │
   └────────────┘
```

---

## 5. Verifiable Credentials Integration

### Ecosystem Participants

```
┌─────────┐     issues      ┌─────────┐     presents    ┌──────────┐
│ ISSUER  │ ──────────────▶ │ HOLDER  │ ──────────────▶ │ VERIFIER │
└─────────┘                 └─────────┘                 └──────────┘
    │                           │                           │
    └── Signs with DID ─────────┘── Stores VC ──────────────┘── Verifies
```

### Verifiable Credential Structure

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "id": "http://example.edu/credentials/3732",
  "type": ["VerifiableCredential", "UniversityDegreeCredential"],
  "issuer": {
    "id": "did:example:issuer123",
    "name": "Example University"
  },
  "issuanceDate": "2024-01-01T00:00:00Z",
  "credentialSubject": {
    "id": "did:example:holder456",
    "degree": {
      "type": "BachelorDegree",
      "name": "Bachelor of Science"
    }
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2024-01-01T00:00:00Z",
    "verificationMethod": "did:example:issuer123#keys-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQp..."
  }
}
```

### Verifiable Credential Types

- Educational credentials (degrees, certificates)
- Professional certifications
- Identity documents (passports, driver's licenses)
- Health records
- Supply chain attestations
- Financial credentials

---

## 6. Industry Use Cases

### Healthcare

- **Medical records management** - Secure sharing between providers
- **Vaccination verification** - COVID-19 immunity passports
- **Patient data portability** - Consent-controlled data sharing
- **Prescription tracking** - Fraud prevention

### Supply Chain

- **Product authenticity** - Anti-counterfeiting for luxury goods
- **Chain of custody** - Track shipments across jurisdictions
- **Cold chain verification** - Temperature-sensitive products
- **Supplier credentials** - Verify "trusted supplier" status

### Government & Public Sector

- **Digital ID for refugees** - Identity for stateless persons
- **Permanent resident cards** - eIDAS-compliant credentials
- **Citizen services** - Access to government portals
- **Voting systems** - Secure, verifiable elections

### Education

- **Academic credentials** - Fraud-proof diplomas
- **Professional development** - Training certificates
- **Skill verification** - Instant employer verification
- **Cross-institutional records** - Portable transcripts

### Finance

- **KYC/AML compliance** - Reusable identity verification
- **Credit history** - User-controlled financial data
- **Cross-border payments** - Pseudonymous transactions
- **DeFi identity** - Web3 authentication

### Enterprise

- **Employee credentials** - Role-based access
- **B2B authentication** - Inter-organizational trust
- **Audit trails** - Immutable compliance records
- **Vendor management** - Supply chain trust

---

## 7. Privacy & Security Considerations

### Privacy by Design Principles

1. **Pairwise-pseudonymous DIDs** - Different DIDs for each relationship
2. **Off-chain private data** - Never store PII on public ledgers
3. **Selective disclosure** - Share only necessary information
4. **Zero-knowledge proofs** - Prove attributes without revealing data

### Security Best Practices

- Key rotation mechanisms
- Multi-signature support
- Delegation capabilities
- Revocation registries

---

## 8. DID Development in China

### 8.1 Overview

China has been actively developing distributed digital identity infrastructure with strong government backing. The approach combines government-verified identity with distributed ledger technology.

### 8.2 Key Players in China

#### CTID (居民身份网络可信凭证)

- **Operator:** 公安部第一研究所 (Ministry of Public Security First Research Institute)
- **Type:** Software-based solution
- **Features:**
  - Real identity verification
  - Network credential issuance
  - Multi-factor authentication
  - Based on ID card + biometric verification
- **Scale:**
  - 50+ billion data records
  - 20,000+ requests per second capacity
  - ~0.5 second average response time

#### eID (网络电子身份标识)

- **Operator:** 公安部第三研究所 (Ministry of Public Security Third Research Institute)
- **Type:** Hardware-based solution
- **Features:**
  - Smart security chip as carrier
  - Online and offline authentication
  - Digital signature capabilities
  - Loaded on financial IC cards, SIM cards, and smartphones
- **Partnerships:**
  - Major banks for eID-enabled cards
  - Three telecom operators for SIM-based eID
  - Smartphone manufacturers for built-in eID

### 8.3 BSN (Blockchain Service Network)

#### Overview

BSN is a global blockchain public infrastructure initiated by:
- 国家信息中心 (State Information Center) - Planning & Design
- 中国移动 (China Mobile)
- 中国银联 (China UnionPay)
- 北京红枣科技 (Beijing Red Date Technology)

#### BSN-DID Service

```
┌─────────────────────────────────────────────────────────────┐
│                    BSN-DID Architecture                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐  │
│  │   User      │────▶│   CTID      │────▶│   BSN       │  │
│  │  Identity   │     │ Verification│     │ Yan'an Chain│  │
│  └─────────────┘     └─────────────┘     └─────────────┘  │
│                             │                    │          │
│                             ▼                    ▼          │
│                      ┌─────────────┐     ┌─────────────┐  │
│                      │ Real-name   │     │ DID Document│  │
│                      │ Attestation │     │ Storage     │  │
│                      └─────────────┘     └─────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### BSN Real-name DID Service Features

| Feature | Description |
|---------|-------------|
| Real-name Verification | Based on CTID system (ID + name or ID + name + face recognition) |
| Unified DID | Same person gets same DID across different applications |
| Key Management | Different public keys for different applications |
| Private Key Storage | Held by user or application provider |
| Chain | BSN Yan'an Chain (延安链) |

#### BSN Official Services

| Service | Description |
|---------|-------------|
| **BSN实名DID服务** | Real-name verified DID issuance |
| **全网分布式域名服务** | Distributed domain name system across all chains |
| **可信区块链运行监测** | Trusted blockchain monitoring platform |
| **BSN官方DDC服务** | Official DDC/NFT contract service |
| **私钥托管服务** | Private key escrow service (with ID recovery) |

### 8.4 DIDA (分布式数字身份产业联盟)

#### Overview

DIDA (DID-Alliance) is China's first distributed digital identity industry alliance, formed to promote DID technology standards and applications.

#### Key Information

| Aspect | Details |
|--------|---------|
| Founded | August 2020 |
| White Paper | First comprehensive DID white paper in China |
| Members | 12 founding council members |
| Focus | Technology standards, industry applications, legal compliance |

#### DIDA's Four Major Tasks

1. **Technical Standards** - Establish distributed identity technical specifications
2. **Infrastructure** - Build distributed identity infrastructure
3. **Application Scenarios** - Explore and promote use cases
4. **International Cooperation** - Align with international standards

#### Expert Advisors

- 蔡吉人 (Cai Jiren) - Academician, Chinese Academy of Engineering
- 陈静 (Chen Jing) - Former Director, PBOC Technology Department
- 李京春 (Li Jingchun) - TC260 Security Assessment Group Leader
- 刘多 (Liu Duo) - President, CAICT
- 马智涛 (Ma Zhitao) - VP & CIO, WeBank
- 詹榜华 (Zhan Banghua) - Chairman, Beijing Certificate Authority
- 徐恪 (Xu Ke) - Deputy Head, Tsinghua CS Department

### 8.5 National Standards

#### Standard: 区块链和分布式记账技术分布式身份管理系统概述

| Aspect | Details |
|--------|---------|
| Standard ID | ISO/TR 23249:2022 (adopted) |
| Committee | TC590 (National Blockchain Standardization Committee) |
| Authority | Ministry of Industry and Information Technology |
| Drafter | China Electronics Standardization Institute |
| Status | Under development |

#### Scope

- Classification of distributed identity management systems
- Basic functions and components
- International mainstream system analysis
- Participant roles and responsibilities
- Framework definitions

### 8.6 China vs International Comparison

| Aspect | China | International (EU/US) |
|--------|-------|----------------------|
| **Approach** | Government-led, real-name verified | Market-driven, self-sovereign |
| **Identity Source** | CTID/eID (government) | Multiple issuers |
| **Blockchain** | BSN (national infrastructure) | Various (Ethereum, Hyperledger, etc.) |
| **Privacy Model** | Real-name with controlled disclosure | Pseudonymous by default |
| **Standardization** | Adopting ISO standards | W3C, DIF standards |
| **Key Projects** | BSN-DID, DIDA | eIDAS, NIST guidelines |

### 8.7 Application Areas in China

#### Government Services

- Digital identity for citizens
- Cross-department data sharing
- Administrative service authentication
- Social security integration

#### Financial Services

- Bank account opening (KYC)
- Insurance claims
- Securities trading
- Cross-border payments

#### Healthcare

- Patient identity management
- Medical record sharing
- Prescription verification
- Health certificate issuance

#### Cross-border Applications

- International travel credentials
- Cross-border trade verification
- International education credentials

### 8.8 Challenges in China

| Challenge | Description |
|-----------|-------------|
| **Adoption** | CTID and eID not yet widely adopted |
| **Privacy Balance** | Balancing real-name requirements with privacy protection |
| **KYC Limitations** | Financial institutions cannot access detailed identity info |
| **Interoperability** | Integration between different identity systems |
| **Hardware Dependency** | eID relies on hardware carriers that can be lost |
| **Cross-platform** | Linking CTID identities with business ecosystems |

### 8.9 Future Outlook

1. **Standardization** - National standards alignment with ISO
2. **Infrastructure** - Expansion of BSN network capabilities
3. **Applications** - More government and enterprise use cases
4. **International** - Cross-border identity recognition
5. **Privacy Enhancement** - Zero-knowledge proof integration
6. **Digital Yuan Integration** - CBDC identity verification

---

## 9. Key Standards & Resources

| Resource | URL |
|----------|-----|
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

## 10. Design Goals (W3C Specification)

| Goal | Description |
|------|-------------|
| Decentralization | Eliminate the requirement for centralized authorities or single points of failure in identifier management |
| Control | Give entities the power to directly control their digital identifiers without relying on external authorities |
| Privacy | Enable entities to control the privacy of their information, including minimal, selective, and progressive disclosure |
| Security | Enable sufficient security for requesting parties to depend on DID documents for their required level of assurance |
| Proof-based | Enable DID controllers to provide cryptographic proof when interacting with other entities |
| Discoverability | Make it possible for entities to discover DIDs for other entities |
| Interoperability | Use interoperable standards so DID infrastructure can make use of existing tools and software libraries |
| Portability | Be system- and network-independent and enable entities to use their digital identifiers with any system that supports DIDs |
| Simplicity | Favor a reduced set of simple features to make the technology easier to understand, implement, and deploy |
| Extensibility | Enable extensibility provided it does not greatly hinder interoperability, portability, or simplicity |

---

## 11. Data Model Types

| Data Type | Description |
|-----------|-------------|
| map | A finite ordered sequence of key/value pairs |
| list | A finite ordered sequence of items |
| set | A finite ordered sequence of items that does not contain the same item twice |
| datetime | A date and time value |
| string | A sequence of code units |
| integer | A real number without a fractional component |
| double | A real number with a fractional component |
| boolean | A value that is either true or false |
| null | A value used to indicate the lack of a value |

---

## 12. Summary: Key Takeaways

### Technical Understanding

1. **DID Resolution** is the core process - transforming a DID string into a usable DID document
2. **DID Methods** define how DIDs work on specific infrastructures (blockchain, web, etc.)
3. **Verification Relationships** define what actions a key can perform
4. **DID URLs** extend DIDs with paths, queries, and fragments for resource access

### China's Approach

1. **Government-Led** - CTID and eID backed by Ministry of Public Security
2. **Real-Name Foundation** - Identity verified against national ID system
3. **BSN Infrastructure** - National blockchain network for DID services
4. **DIDA Alliance** - Industry coordination and standardization
5. **Standards Alignment** - Adopting ISO standards while developing national specifications

### Global Comparison

| Factor | Western Approach | China Approach |
|--------|-----------------|----------------|
| Philosophy | Self-sovereign identity | Government-verified identity |
| Privacy | Pseudonymous by default | Real-name with privacy controls |
| Infrastructure | Decentralized networks | BSN national infrastructure |
| Adoption Driver | Market demand | Government policy |

---

*Research compiled on 2026-03-04*
*Last updated with China DID development details*

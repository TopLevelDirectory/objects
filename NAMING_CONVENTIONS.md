# Scale-Invariant Naming Conventions with Proof Metadata Encoding

**Document Version:** 1.0  
**Last Updated:** 2025-12-12 07:15:50 UTC  
**Author:** TopLevelDirectory  

## Overview

This document defines scale-invariant naming conventions for objects within the TopLevelDirectory ecosystem, integrating proof metadata encoding and self-sovereign validation systems. These conventions ensure consistency, discoverability, and verifiable provenance across distributed systems at any scale.

---

## Table of Contents

1. [Fundamental Principles](#fundamental-principles)
2. [Naming Structure](#naming-structure)
3. [Proof Metadata Encoding](#proof-metadata-encoding)
4. [Self-Sovereign Validation System](#self-sovereign-validation-system)
5. [Scale-Invariant Design](#scale-invariant-design)
6. [Implementation Examples](#implementation-examples)
7. [Versioning and Migration](#versioning-and-migration)

---

## Fundamental Principles

### 1. **Content-Addressability**
Names derive from content hashes, ensuring names remain valid regardless of distribution scale.

### 2. **Cryptographic Integrity**
All names include cryptographic proofs enabling verification without external authority.

### 3. **Self-Sovereign Authority**
Objects carry their own validation metadata, eliminating dependency on centralized registries.

### 4. **Scale Invariance**
Naming structures remain consistent whether managing 1 or 1 billion objects.

### 5. **Interoperability**
Names follow standardized formats enabling cross-system recognition and validation.

---

## Naming Structure

### Base Format

```
<PREFIX>-<CONTENT_HASH>-<PROOF_MARKER>-<VALIDATION_CHAIN>
```

### Component Definitions

#### **PREFIX** (Type Identifier)
- Length: 3-8 characters
- Pattern: `^[a-z0-9]+$`
- Examples: `obj`, `asset`, `doc`, `contract`, `identity`
- Purpose: Indicates object type for logical grouping

#### **CONTENT_HASH** (Cryptographic Identifier)
- Algorithm: SHA-256 (default) or SHA-3-256
- Encoding: Base32hex (RFC 4648)
- Length: 52 characters (SHA-256 in Base32hex)
- Format: `[a-z2-7]{52}`
- Immutable property ensuring content integrity

#### **PROOF_MARKER** (Validation Evidence)
- Length: 8 characters
- Encoding: Hexadecimal
- Format: First 4 bytes of proof signature + validation level indicator
- Levels: `00` (unverified), `01` (self-verified), `02` (peer-verified), `03` (notarized)

#### **VALIDATION_CHAIN** (Authority Trail)
- Length: Variable (minimum 16 characters)
- Encoding: Base16 (hex)
- Format: Chain of validator signatures concatenated
- Purpose: Enables traceability of all validation events

### Complete Example

```
doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq-a1b2c3d4-02-e5f6g7h8i9j0k1l2m3n4o5p6
 │   │                                                  │        │  │
 │   │                                                  │        │  └─ Validation chain
 │   │                                                  │        └─ Validation level (peer-verified)
 │   │                                                  └─ Proof marker
 │   └─ SHA-256 content hash (Base32hex)
 └─ Object type prefix
```

---

## Proof Metadata Encoding

### Proof Structure

Proofs are cryptographically signed metadata packages embedded in or associated with named objects.

```json
{
  "proof": {
    "version": "1.0",
    "algorithm": "ECDSA-SHA256",
    "timestamp": "2025-12-12T07:15:50Z",
    "objectName": "doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq",
    "contentHash": "nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq",
    "validator": {
      "did": "did:topdir:validator:v1:a1b2c3d4e5f6g7h8",
      "publicKey": "0x...",
      "jurisdiction": "distributed"
    },
    "validationType": "peer-verified",
    "signature": "e5f6g7h8i9j0k1l2m3n4o5p6...",
    "parentProofs": [
      "nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq"
    ]
  }
}
```

### Proof Levels

| Level | Code | Meaning | Requirements |
|-------|------|---------|--------------|
| Unverified | `00` | No validation performed | Object named and addressable |
| Self-Verified | `01` | Creator confirms content | Creator signature only |
| Peer-Verified | `02` | Independent validation by trusted peer | Peer signature + timestamp |
| Notarized | `03` | Third-party notarization | Notary signature + timestamp + proof-of-work |

### Proof Encoding Algorithm

```
PROOF_MARKER = HEX(FIRST_4_BYTES(SIGNATURE)) + VALIDATION_LEVEL_CODE

Example:
Signature: 0xe5f6g7h8i9j0k1l2m3n4o5p6...
Level: 02 (peer-verified)
Marker: e5f6g702
```

---

## Self-Sovereign Validation System

### Core Components

#### 1. **Decentralized Identifier (DID) System**

Each validator maintains a resolvable DID:

```
did:topdir:<entity_type>:<version>:<unique_identifier>

Examples:
- did:topdir:validator:v1:a1b2c3d4e5f6g7h8
- did:topdir:creator:v1:x9y8z7w6v5u4t3s2
- did:topdir:notary:v1:m1n2o3p4q5r6s7t8
```

#### 2. **Public Key Infrastructure (PKI)**

- **Algorithm**: ECDSA P-256 (default) or Ed25519
- **Storage**: On-chain or distributed ledger
- **Rotation**: Supported with backward compatibility
- **Revocation**: Timestamped revocation list entries

#### 3. **Validation Registry**

Distributed registry maintaining validation records:

```
{
  "objectName": "doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq",
  "validations": [
    {
      "validator": "did:topdir:validator:v1:a1b2c3d4e5f6g7h8",
      "timestamp": "2025-12-12T07:15:50Z",
      "level": "02",
      "signature": "0xe5f6g7h8...",
      "expiresAt": "2026-12-12T07:15:50Z"
    }
  ],
  "consensusThreshold": 2,
  "consensusAchieved": true
}
```

#### 4. **Validation Chain Resolution**

Process for verifying validation chains:

```
1. Parse VALIDATION_CHAIN from object name
2. Extract validator DIDs from chain
3. Resolve each DID to obtain public keys
4. Verify signatures in reverse chronological order
5. Check proof-of-timestamp for notarized proofs
6. Validate against revocation list
7. Return consensus score (0.0 - 1.0)
```

### Validation Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Object Created & Named                                   │
│    - Content hash computed                                  │
│    - Preliminary name assigned (level: 00)                  │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│ 2. Creator Self-Verification                                │
│    - Creator signs proof with private key                   │
│    - Proof marker generated (level: 01)                     │
│    - Name updated with proof marker                         │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│ 3. Peer Validation (Optional)                               │
│    - Peer(s) verify content and creator signature           │
│    - Peer(s) add their signatures to validation chain       │
│    - Proof level upgraded to: 02                            │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│ 4. Notarization (Optional)                                  │
│    - Notary performs proof-of-work                          │
│    - Notary signs validation package                        │
│    - Proof level upgraded to: 03                            │
│    - Timestamp anchor created                               │
└─────────────────────────────────────────────────────────────┘
```

### Consensus Mechanism

```
Consensus Score = (Valid Validations / Required Validations) * 100

Example:
- Required validations: 3
- Valid signatures: 3
- Consensus score: 100% (trusted)

Thresholds:
- < 50%: Untrusted (reject)
- 50-75%: Questionable (use caution)
- 75-99%: Acceptable (normal use)
- 100%: Fully trusted (unrestricted use)
```

---

## Scale-Invariant Design

### Horizontal Scalability

The naming scheme scales without modification:

- **Single system**: Validates against local registry
- **Regional federation**: Validators coordinate via gossip protocol
- **Global network**: Byzantine fault tolerance with k-of-n validation

### Performance Characteristics

| Operation | Complexity | Notes |
|-----------|-----------|-------|
| Name generation | O(1) | Hash computation only |
| Proof verification | O(n) | Linear in validation chain length |
| Registry lookup | O(log n) | Merkle tree structure |
| Consensus achievement | O(k) | k = required validators |

### Distributed Registry Design

```
Merkle Tree Structure:
└── Root Hash
    ├── Level 1 (Validators)
    │   ├── Validator A's tree
    │   ├── Validator B's tree
    │   └── Validator C's tree
    │
    └── Level 2 (Time buckets)
        ├── 2025-12-12 bucket
        ├── 2025-12-13 bucket
        └── ...
```

### Gossip Protocol

```
Node receives validation → Signs → Broadcasts to peer subset
                                ↓
                    Peer node receives → Verifies
                                   ↓
                            Adds to registry
                                   ↓
                    Rebroadcasts to its peer subset
```

---

## Implementation Examples

### Example 1: Creating a Named Document

```python
import hashlib
import base64
from datetime import datetime

class NamedObject:
    def __init__(self, content: bytes, object_type: str = "doc"):
        self.content = content
        self.object_type = object_type
        self.content_hash = self._compute_hash()
        self.name = self._generate_name()
        self.proof_level = "00"  # Unverified
        
    def _compute_hash(self) -> str:
        """Compute SHA-256 hash and encode in Base32hex"""
        sha256 = hashlib.sha256(self.content).digest()
        # Base32hex encoding (RFC 4648)
        return base64.b32encode(sha256).decode().lower().rstrip('=')
    
    def _generate_name(self) -> str:
        """Generate initial name"""
        return f"{self.object_type}-{self.content_hash}"
    
    def self_verify(self, private_key: str) -> str:
        """Self-verify and update name with proof marker"""
        proof_sig = self._sign(private_key)
        proof_marker = proof_sig[:8] + "01"
        self.proof_level = "01"
        self.name = f"{self.name}-{proof_marker}-self"
        return self.name
    
    def _sign(self, private_key: str) -> str:
        """Sign content with private key (simplified)"""
        import hmac
        sig = hmac.new(
            private_key.encode(),
            self.content,
            hashlib.sha256
        ).hexdigest()
        return sig[:8]

# Usage
doc_content = b"Hello, TopLevelDirectory!"
named_doc = NamedObject(doc_content, "doc")
print(f"Unverified name: {named_doc.name}")

verified_name = named_doc.self_verify("my_private_key")
print(f"Self-verified name: {verified_name}")
```

### Example 2: Peer Validation

```python
class ValidationNode:
    def __init__(self, validator_did: str, private_key: str):
        self.validator_did = validator_did
        self.private_key = private_key
        self.validations = {}
    
    def validate_object(self, named_object: NamedObject) -> bool:
        """Peer validates an object"""
        # Verify creator signature (assuming it exists)
        if named_object.proof_level == "00":
            return False  # Cannot validate unverified objects
        
        # Add peer validation
        peer_sig = self._sign(named_object.content)
        validation_entry = {
            "validator": self.validator_did,
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "signature": peer_sig[:8],
            "level": "02"
        }
        
        self.validations[named_object.name] = validation_entry
        return True
    
    def _sign(self, content: bytes) -> str:
        """Sign content"""
        import hmac
        sig = hmac.new(
            self.private_key.encode(),
            content,
            hashlib.sha256
        ).hexdigest()
        return sig

# Usage
validator1 = ValidationNode("did:topdir:validator:v1:a1b2c3d4", "validator1_key")
validator2 = ValidationNode("did:topdir:validator:v1:x9y8z7w6", "validator2_key")

validator1.validate_object(named_doc)
validator2.validate_object(named_doc)
```

### Example 3: Validation Chain Resolution

```python
class ValidationResolver:
    def __init__(self, registry: dict):
        self.registry = registry
    
    def resolve_validation_chain(self, object_name: str) -> dict:
        """Resolve and verify validation chain"""
        parts = object_name.split("-")
        if len(parts) < 4:
            return {"consensus_score": 0, "status": "invalid_format"}
        
        content_hash = parts[1]
        validation_chain_hex = parts[3]
        
        # Fetch validation records from registry
        validations = self.registry.get(object_name, {}).get("validations", [])
        
        valid_count = 0
        for validation in validations:
            if self._verify_signature(validation):
                valid_count += 1
        
        consensus_score = (valid_count / len(validations) * 100) if validations else 0
        
        return {
            "consensus_score": consensus_score,
            "valid_validations": valid_count,
            "total_validations": len(validations),
            "status": self._determine_status(consensus_score)
        }
    
    def _verify_signature(self, validation: dict) -> bool:
        """Verify individual validation signature"""
        # Implementation depends on PKI system
        return True
    
    def _determine_status(self, score: float) -> str:
        if score < 50:
            return "untrusted"
        elif score < 75:
            return "questionable"
        elif score < 100:
            return "acceptable"
        else:
            return "fully_trusted"

# Usage
registry = {
    "doc-nv6fzd2...": {
        "validations": [
            {"validator": "did:topdir:validator:v1:a1b2c3d4", "signature": "valid"},
            {"validator": "did:topdir:validator:v1:x9y8z7w6", "signature": "valid"}
        ]
    }
}

resolver = ValidationResolver(registry)
result = resolver.resolve_validation_chain("doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq-a1b2c3d4-02-e5f6g7h8")
print(result)
```

---

## Versioning and Migration

### Version Management

- **Current Version**: 1.0
- **Compatibility**: Backward compatible within major version
- **Migration Path**: V1 → V2+ with explicit versioning prefix

### Versioning Format

```
<VERSION_MARKER>:<PREFIX>-<CONTENT_HASH>-<PROOF_MARKER>-<VALIDATION_CHAIN>

v1:doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq-a1b2c3d4-02
v2:doc-nv6fzd2hxjytbfbpqr36p4fqnlg5jddj4u7yq2gmzphfyjdmcbq-a1b2c3d4-03
```

### Migration Strategy

1. **Dual Support Phase** (Optional version marker)
   - V1 names without marker still valid
   - New objects use explicit versioning

2. **Transition Phase** (If major changes needed)
   - Old names resolved through translation layer
   - New hash algorithm introduced

3. **Sunset Phase** (For deprecated versions)
   - Legacy support ended with notice period
   - Migration tools provided

---

## Best Practices

### ✅ DO

- Use appropriate prefix for object type
- Include proof level matching actual validation status
- Keep validation chain length reasonable (< 100 validators)
- Regularly rotate validator keys
- Maintain local cache of validation registry
- Document custom validation rules per domain

### ❌ DON'T

- Modify content hash after initial naming
- Skip validation for critical objects
- Trust names without verifying consensus
- Store private keys in object metadata
- Use weak hash algorithms (< SHA-256)
- Ignore revocation notices

### Security Considerations

1. **Key Management**
   - Store private keys in secure hardware (HSM/TEE)
   - Use key derivation functions (PBKDF2, Argon2)
   - Implement key rotation policies

2. **Proof Storage**
   - Replicate proofs across multiple nodes
   - Implement immutable audit logs
   - Use timestamping services (RFC 3161)

3. **Registry Security**
   - Protect registry from unauthorized modifications
   - Implement access control lists (ACL)
   - Monitor for consensus anomalies

---

## References

- RFC 3161: Time-Stamp Protocol (TSP)
- RFC 4648: Base Encoding Data Formats
- RFC 7515: JSON Web Signature (JWS)
- W3C DID Specification
- NIST FIPS 186-4: Digital Signature Standard
- BFT Consensus Algorithms (PBFT, RAFT)

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-12 | Initial release with core naming structure, proof metadata encoding, and self-sovereign validation system |

---

**Document Status:** Active  
**Maintainer:** TopLevelDirectory  
**Last Review:** 2025-12-12  
**Next Review:** 2026-12-12

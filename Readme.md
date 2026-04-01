# Trust Decay Framework (TDF)

A confidence-based classification system for verifiable credentials with time-based trust decay, zero-knowledge proofs, and risk-aware adaptive security.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: Editor's Draft](https://img.shields.io/badge/Status-Editor's%20Draft-orange.svg)]()

---

## 📖 Overview

Trust is not static. A credential that was trustworthy yesterday may not be trustworthy today. The **Trust Decay Framework (TDF)** provides a standardized approach for assessing and expressing the current trustworthiness of digital credentials over time.

Instead of binary valid/invalid decisions, TDF introduces a four-tier confidence hierarchy where trust naturally diminishes without ongoing evidence of security and identity consistency. Credentials can be refreshed through cryptographic proofs, heartbeat mechanisms, and risk assessment—all while preserving privacy through pairwise identifiers and zero-knowledge proofs.

Built on established standards like W3C Decentralized Identifiers (DIDs) and Verifiable Credentials (VCs), TDF enables issuers to create credentials with an initial trust level, and verifiers to evaluate the current trust level based on age, cryptographic strength, proofs, and risk signals.

---

## 🎯 Core Concepts

### Confidence Levels

| Level | Name | Half-Life | Typical Use Cases |
|-------|------|-----------|-------------------|
| **Level 4** | Maximum Trust | 30 days | Government services, critical systems |
| **Level 3** | High Trust | 90 days | High-value banking, corporate access |
| **Level 2** | Standard Trust | 90 days | Enterprise applications, retail banking |
| **Level 1** | Basic Trust | 7 days | Anonymous access, low-risk services |

**Half-Life Explained:** The period after which confidence decreases by 50% without refreshment. Higher trust levels require more frequent maintenance because they grant access to more sensitive resources.

### Trust Operations

- **Demotion** — Trust decreases due to time decay or detected risks
- **Promotion** — Trust increases when valid heartbeats are provided (limited to original issuance level)

### Key Mechanisms

| Mechanism | Description |
|-----------|-------------|
| **Time-Based Decay** | Trust diminishes predictably according to a mathematical formula |
| **Zero-Knowledge Proofs** | Prove security status without revealing sensitive underlying data |
| **Heartbeats** | Cryptographic proofs that refresh or restore trust (biometric, proof-of-possession, liveness) |
| **Risk Signals** | Real-time anomalies that trigger immediate trust reduction |
| **Pairwise Identifiers** | Unique IDs per relationship prevent correlation across services |

---

## 📋 Specification Structure

### 1. Introduction
Defines the problem statement, design principles, and usage examples. Trust should not be permanent—credentials require ongoing verification.

### 2. Terminology
Standardized definitions for confidence levels, half-life, heartbeats, risk signals, zero-knowledge proofs, and pairwise identifiers.

### 3. Trust Levels
Specifies the four-tier hierarchy with half-lives, proof requirements, and demotion/promotion rules.

### 4. Credential Format
Extends W3C Verifiable Credentials with confidence level information, assessment policy references, and validity timestamps.

### 5. Trust Decay Calculation
Mathematical formula for computing current trust level:
```
DecayFactor = 2 ^ (-(EffectiveAge × CryptoMultiplier) / HalfLife)
```
- **EffectiveAge** = Actual age minus time offset from valid heartbeats
- **CryptoMultiplier** = 1 for quantum-resistant algorithms, 5 for quantum-vulnerable
- **HalfLife** = Based on initial confidence level

### 6. Proofs and Heartbeats
Defines required proof types for each trust level and heartbeat mechanisms:
- **No High-Risk Activity (24h)** — Required for Level 2+
- **Device Consistency (7d)** — Enables Level 3
- **Device Consistency (30d)** — Enables Level 4
- **Stable Location (72h)** — Enables Level 4

Heartbeat types include biometric verification, proof of possession, and liveness checks.

### 7. Risk Signals
Specifies observable events that may indicate compromised security:
- Device Change — Demote to Level 2
- Geographic Anomaly — Temporary demotion (2 hours)
- Behavioral Anomaly — Require heartbeat (15 min)
- Concurrent Session — Severity-dependent action
- Velocity Anomaly — Severity-dependent action

Severity levels range from Low to Critical, with corresponding actions.

### 8. Cryptographic Requirements
Defines supported algorithms and quantum considerations:
- **Ed25519** — REQUIRED (classical)
- **ML-DSA-44** — RECOMMENDED (post-quantum)
- **ML-DSA-65/87** — OPTIONAL (post-quantum)
- **Hybrid Modes** — OPTIONAL

Quantum-vulnerable algorithms incur a 5× decay multiplier.

### 9. Privacy Considerations
Privacy-preserving mechanisms:
- **Pairwise Identifiers** — Derived per relationship: `HMAC(RootKey, RelationshipContext || ServiceIdentifier)`
- **Zero-Knowledge Proofs** — Reveal only that conditions are satisfied, not the underlying data
- **Nullifiers** — Prevent replay attacks while maintaining unlinkability

### 10. Implementation Guidelines
Recommendations for state management, multi-tier caching (L1 memory, L2 local, L3 distributed), policy evaluation, and decision types (Granted, Reauthentication Required, Denied).

### 11. Security Considerations
Attack mitigations and key rotation protocol (pre-rotation, rotation, post-rotation phases).

---

## 🔒 Privacy Features

### Pairwise Identifiers
Instead of a global identifier, each relationship uses a unique derived identifier. The same holder interacting with different services appears as different entities, preventing correlation without the root key.

### Zero-Knowledge Proofs
- **NoHighRisk24h** — Proves no high-risk alerts exist without revealing which alerts
- **DeviceConsistency** — Proves device stability without revealing device fingerprint
- **GeoStable** — Proves location stability without revealing actual coordinates

### Nullifiers
Each proof includes a unique nullifier derived from the holder's root key and context, ensuring proofs cannot be replayed or correlated across different contexts.

---

## 🔐 Cryptographic Support

| Algorithm | Type | Quantum Resistance |
|-----------|------|-------------------|
| Ed25519 | Classical | No |
| ML-DSA-44 | Post-Quantum | Yes |
| ML-DSA-65 | Post-Quantum | Yes |
| ML-DSA-87 | Post-Quantum | Yes |
| Hybrid Ed25519-ML-DSA | Hybrid | Yes |

Quantum-resistant algorithms receive no decay penalty; quantum-vulnerable algorithms decay 5× faster.

---

## 📊 Trust Decay Example

A Level 4 credential (30-day half-life) issued 45 days ago with no heartbeats:

- EffectiveAge = 45 days
- HalfLife = 30 days
- DecayExponent = -(45 × 1) / 30 = -1.5
- DecayFactor = 2^-1.5 = 0.3535
- 0.3535 > 0.25 → Demote two levels
- Level 4 → Level 2

After a valid biometric heartbeat, trust may be restored or decay reset.

---

## 🏛️ Standards Alignment

The Trust Decay Framework is built on and compatible with:

| Standard | Relevance |
|----------|-----------|
| **W3C DID Core 1.0** | Decentralized Identifiers for subjects and issuers |
| **W3C VC Data Model 2.0** | Verifiable Credential structure and proofs |
| **W3C VC ZKP** | Zero-knowledge proof integration |
| **RFC 8032 (Ed25519)** | Digital signatures |
| **Universal DID-Native Addressing (UDNA)** | Identity-based network addressing |

---

## 📄 License

This specification and related materials are licensed under the **MIT License** — freely implementable without restrictions.

---

## 🙏 Acknowledgments

- W3C DID Working Group for Decentralized Identifiers
- W3C VC Working Group for Verifiable Credentials
- UDNA Community Group for decentralized addressing standards
- Sirraya Labs for open-source commitment

---

## 📮 Contact & Resources

- **Specification Home**: [https://spec.sirraya.com/tdf](https://spec.sirraya.com/tdf)
- **GitHub Repository**: [https://github.com/sirrayalabs/trust-decay-framework](https://github.com/sirrayalabs/trust-decay-framework)
- **Issues & Discussions**: [https://github.com/sirrayalabs/trust-decay-framework/issues](https://github.com/sirrayalabs/trust-decay-framework/issues)

---

**Trust Decay Framework** — Making digital trust dynamic, privacy-preserving, and quantum-ready.

*Version 1.0.0 • Editor's Draft • April 2026*
```
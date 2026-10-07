<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 40. Cryptographic Certification Roadmap

Customers in regulated and national-security sectors may require evaluated cryptography and certified products. This chapter describes how SANKET approaches certification. No certification has been achieved at the date of this document.

## 40.1 Types of Requirement

- **Nationally approved cryptographic algorithms** or modules mandated by a national cryptographic authority.
- **Independent cryptographic evaluation** of the implementation and its use of primitives.
- **Product security certification** under a recognised scheme.
- **Sector-specific assurance**, such as security audits required for government deployment.

## 40.2 SANKET's Approach

SANKET's foundations are chosen to be evaluable: published, peer-reviewed protocols; the official libsignal implementation used without modification; standard primitives (AES-256-GCM, X25519, Ed25519, ECDSA P-256, SHA-256, HKDF, Argon2id, ML-KEM family, including the hybrid X25519MLKEM768 TLS group); and a small number of clearly defined places where cryptography is invoked. The crypto-agility mechanisms in Chapter 39 allow non-message layers to adopt approved primitives where a customer requires them.

Every cryptographic library is held to SANKET's rule of a published independent audit. Where no audited option exists, the exception is reviewed, approved and recorded, and the affected components are identified to evaluators in the controlled edition so that an evaluation can focus on them. Hybrid post-quantum signatures (Ed25519 with ML-DSA) for licences and release manifests are held until an ML-DSA implementation has a published independent audit or an issued validation certificate.[^75]

## 40.3 India-Specific Pathway

For Indian government and defence customers, Tosh Defence intends to pursue evaluation by the relevant national bodies, including security audit by a CERT-In empanelled auditor and evaluation of cryptographic implementations through the appropriate national scheme. These are CERTIFICATION TARGETS. Where national algorithms are mandated, they would be introduced as a separately evaluated protocol profile, not as an unreviewed change to the Signal Protocol implementation.

> [!WARNING]
> **Important - Status**
>
> SANKET holds no independent security certification and has not been formally accredited by any authority at the date of this document. The security reviews performed to date are internal engineering reviews. Customers should verify current certification status with Tosh Defence for the release they deploy.

---

[^75]: NIST, "Module-Lattice-Based Digital Signature Standard" (ML-DSA), FIPS 204, August 2024.

---

[Previous: 39. Crypto-Agility and Post-Quantum Readiness](39-crypto-agility-and-post-quantum-readiness.md) | [Contents](../README.md) | [Next: 41. Compliance, Assurance and Security Testing](41-compliance-assurance-and-security-testing.md)

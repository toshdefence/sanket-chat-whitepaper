<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix I - Glossary

| Term | Definition |
| --- | --- |
| **ABAC** | Attribute-based access control: decisions based on attributes of user, resource and context |
| **Account list key** | A per-account libsignal key pair, generated on the primary device and protected by its hardware key, that signs the account's device list |
| **AEAD** | Authenticated encryption with associated data: encryption that also guarantees integrity |
| **AES-GCM** | AES in Galois/Counter Mode, an AEAD mode; SANKET uses 256-bit keys |
| **Air gap** | Physical or logical isolation of a network from the internet and other untrusted networks |
| **Broadcast-sender account** | A dedicated SANKET account with a Sanket ID, assigned to named administrators, whose approved desktop sends end-to-end encrypted broadcasts |
| **Content envelope** | The versioned structure inside every libsignal message that carries a message, reaction or broadcast with its message id and optional classification label |
| **Crypto-agility** | The ability to change cryptographic algorithms and parameters without redesigning the system |
| **DAST** | Dynamic application security testing of a running system |
| **Double Ratchet** | Signal's algorithm deriving a new key for every message, combining a symmetric ratchet and a Diffie-Hellman ratchet |
| **E2EE** | End-to-end encryption: only the communicating endpoints hold the keys |
| **FIDO2** | FIDO Alliance standards for phishing-resistant authentication with hardware or platform authenticators |
| **HKDF** | HMAC-based extract-and-expand key derivation function (RFC 5869) |
| **HSM** | Hardware security module: tamper-resistant hardware for key storage and operations |
| **Key Attestation** | Android mechanism by which the hardware key store certifies, in an X.509 chain to a vendor root, that a key was generated in secure hardware |
| **MFA** | Multi-factor authentication |
| **ML-KEM** | Module-lattice key encapsulation mechanism (FIPS 203), the standardised form of Kyber |
| **mTLS** | Mutual TLS: both sides of a TLS connection present certificates |
| **Notice broadcast** | A broadcast whose content the server can read, for non-sensitive announcements; labelled as not end-to-end encrypted |
| **PCS** | Post-compromise security: recovery of confidentiality after a compromise ends |
| **PFS** | Perfect forward secrecy: past sessions stay confidential if long-term or current keys are later compromised |
| **PKI** | Public key infrastructure: CAs, certificates and revocation |
| **PQC** | Post-quantum cryptography: algorithms believed secure against quantum computers |
| **PQXDH** | Signal's post-quantum extension of X3DH, adding a KEM to the initial key agreement |
| **PROXY protocol** | A header by which a proxy passes the original client address to the server behind it |
| **RBAC** | Role-based access control |
| **SAST** | Static application security testing of source code |
| **SBOM** | Software bill of materials: inventory of components in a release |
| **Secure Enclave** | Apple's hardware security subsystem, whose keys cannot be exported from it |
| **SIEM** | Security information and event management system |
| **Signed device list** | An account's list of devices, signed with its account list key and kept in an append-only log, which senders verify before encrypting |
| **SPQR** | Sparse Post-Quantum Ratchet, Signal's ML-KEM-based ratchet run alongside the Double Ratchet |
| **StrongBox** | An Android hardware key store in a separate secure element, stronger than the TEE |
| **TEE** | Trusted execution environment: an isolated area of the main processor that protects keys |
| **TLS** | Transport Layer Security |
| **TPM** | Trusted Platform Module: hardware root of trust for keys and measured boot |
| **TURN** | Traversal Using Relays around NAT: a relay that carries media when a direct path is blocked |
| **WebAuthn** | W3C API for public-key credentials used by FIDO2 |
| **WORM** | Write once, read many: storage that prevents modification after writing |
| **X25519** | Diffie-Hellman function over Curve25519 (RFC 7748) |
| **X3DH** | Extended Triple Diffie-Hellman, Signal's original asynchronous key agreement |
| **Zero trust** | Security model in which no implicit trust is granted by network location or ownership (NIST SP 800-207) |

---

[Previous: Appendix H - Network and Protocol Categories](appendix-h-network-and-protocol-categories.md) | [Contents](../README.md) | [Next: Appendix J - Standards and References](appendix-j-standards-and-references.md)

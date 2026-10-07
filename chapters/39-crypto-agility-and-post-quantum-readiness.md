<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 39. Crypto-Agility and Post-Quantum Readiness

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Cryptographic algorithms age. A platform intended to protect information for years must be able to change its primitives without being rebuilt,[^68] and must plan now for the arrival of cryptographically relevant quantum computers.

## 39.1 Crypto-Agility in the Current Architecture

*Table 62: Where SANKET can change cryptography without redesign*

| Area | Agility mechanism |
| --- | --- |
| **Message protocol** | libsignal is a versioned library with versioned message and key formats; upgrading the library brings new primitives (as PQXDH and SPQR did) without changes to SANKET's protocol code |
| **Call media** | Call crypto is negotiated as a numbered profile; the server refuses devices and offers below the current profile, so the required profile can be raised across the whole platform in a single, server-enforced step |
| **Transport** | TLS suites and key-exchange groups are configuration at the edge and in a single outbound TLS policy module; the hybrid post-quantum group was added this way, with the suites unchanged |
| **Symmetric primitives** | All application AES-GCM goes through one strict helper, so a change of primitive or key size is made in one place and enforced by a pre-commit check |
| **Signing** | Licence and release verification use trust bundles with key identifiers, active, previous and revoked keys |
| **Content envelope** | Message content is a versioned envelope inside libsignal; a new version is switched on per tenant only when every platform's minimum app version can read it, and older clients ask to be updated instead of dropping the message |
| **Server key custody** | OpenBao Transit keys are versioned; key types can be added without application redesign |
| **Certificates** | Edge and SIEM certificates come from the customer PKI and can follow its algorithm profile |

## 39.2 National Cryptographic Modules

Some customers require nationally approved algorithms or cryptographic modules. SANKET's non-message layers (transport, local storage, server-side secrets, signing, backups) are structured so that a provider or algorithm can be substituted in a defined set of places. The message layer is different: SANKET deliberately uses only Signal's audited libsignal implementation for end-to-end messaging, and libsignal fixes its primitives. Substituting a national cipher inside the Signal Protocol would mean departing from the audited implementation, which SANKET will not do as an unreviewed change. Any such requirement would be addressed as a separately specified, independently evaluated protocol profile, in agreement with the customer's cryptographic authority.

## 39.3 Post-Quantum Readiness

### 39.3.1 Why it matters now

An adversary can record encrypted traffic today and decrypt it later if a sufficiently capable quantum computer becomes available ("harvest now, decrypt later"). Information that must stay confidential for many years is therefore already at risk from classical-only key agreement. NIST has standardised ML-KEM for key establishment and ML-DSA and SLH-DSA for signatures, and has published a transition timeline that deprecates quantum-vulnerable algorithms over the coming decade.[^69][^70][^71][^72]

### 39.3.2 Current security

*Table 63: Post-quantum status by layer*

| Layer | Current algorithm | Post-quantum status |
| --- | --- | --- |
| **Message session establishment** | PQXDH: X25519 plus Kyber-1024 | Hybrid post-quantum key agreement IMPLEMENTED with single-use one-time Kyber prekeys; the last-resort Kyber prekey is used only when none remain |
| **Message ratchet** | Triple Ratchet: Double Ratchet (X25519) with SPQR (ML-KEM) | Hybrid post-quantum ratchet IMPLEMENTED through the current libsignal release |
| **Message authentication and signed device lists** | XEdDSA (Curve25519) | Classical only |
| **Transport (TLS) at the edge** | X25519MLKEM768 offered first, then X25519 and secp384r1; AES-256-GCM suites | Hybrid post-quantum key exchange IMPLEMENTED wherever the client platform's TLS stack offers it |
| **Transport (TLS) outbound** | Server runtime TLS offering X25519MLKEM768, X25519 and secp384r1 | Hybrid IMPLEMENTED where the remote server supports it; classical otherwise |
| **TURN relay hop** | TLS 1.3 at the edge, TLS_AES_256_GCM_SHA384; the hybrid group is offered | Not claimed: depends on the TLS client inside each platform's WebRTC stack |
| **Device client certificates, administrator security keys** | ECDSA P-256; ES256 or EdDSA | Classical |
| **Call media keys** | Delivered over libsignal; AES-256-GCM frames | Inherits libsignal session protection; DTLS-SRTP is classical |
| **Licence, release, timestamp, backup-manifest and audit-anchor signatures** | Ed25519 | Classical; hybrid Ed25519 plus ML-DSA ROADMAP (held until an audited ML-DSA implementation exists) |
| **Local cache envelope** | X25519 | Classical |
| **Symmetric encryption** | AES-256 | Considered adequate against known quantum attacks at 256-bit key size |

### 39.3.3 Hybrid key exchange in transport

The edge offers the hybrid group X25519MLKEM768 first in TLS 1.3, followed by X25519 and secp384r1; the cipher suites are unchanged (AES-256-GCM only). The hybrid group combines X25519 with ML-KEM-768,[^73] so a session is protected unless both components are broken: a harvested session cannot be decrypted later with a quantum computer, and a cryptographic weakness in ML-KEM cannot make it weaker than X25519 alone; the residual risk is an implementation defect in the newer code. Its adoption is a reviewed and recorded decision under SANKET's audited-library policy (Chapter 40).

Post-quantum key exchange is used wherever the client platform's TLS stack supports it. SANKET relies on each platform's own TLS stack rather than bundling a separate TLS provider, and the server offers the hybrid group on every outbound connection where the remote server supports it.

The edge records only the negotiated group name for each connection, with no client address, URI or user, so the share of post-quantum connections can be measured without logging who connected. TLS 1.3 authenticates the whole handshake transcript, including the groups the client offered, so a middlebox that tampers with the offered groups is detected.

### 39.3.4 Post-quantum roadmap

- Hybrid post-quantum key exchange on the remaining client paths, as their platform TLS stacks support it.
- Hybrid Ed25519 plus ML-DSA signatures for licences, release manifests and the trust bundle, both signatures over the same domain-separated message and both required. The design is complete; it is held until an ML-DSA implementation has a published independent audit or an issued CMVP certificate, in line with SANKET's rule that every cryptographic library must have one.[^74]

> [!TIP]
> **Security Property - Post-quantum position**
>
> SANKET messaging is protected by hybrid post-quantum cryptography at both session establishment (PQXDH) and in the ongoing ratchet (SPQR), so an adversary must break both the classical and the post-quantum components. Transport key exchange is hybrid post-quantum at the edge for every client whose platform offers it, and on the server's outbound connections. Symmetric encryption uses 256-bit keys throughout. Signatures, transport for the remaining client paths and some auxiliary key exchanges remain classical and follow the roadmap above.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[^68]: E. Barker, A. Roginsky, "Transitioning the Use of Cryptographic Algorithms and Key Lengths", NIST SP 800-131A Rev. 2, March 2019.
[^69]: NIST, "Module-Lattice-Based Key-Encapsulation Mechanism Standard" (ML-KEM), FIPS 203, August 2024.
[^70]: NIST, "Module-Lattice-Based Digital Signature Standard" (ML-DSA), FIPS 204, August 2024.
[^71]: NIST, "Stateless Hash-Based Digital Signature Standard" (SLH-DSA), FIPS 205, August 2024.
[^72]: NIST, "Transition to Post-Quantum Cryptography Standards", NIST IR 8547 (Initial Public Draft), November 2024.
[^73]: NIST, "Module-Lattice-Based Key-Encapsulation Mechanism Standard" (ML-KEM), FIPS 203, August 2024.
[^74]: NIST, "Module-Lattice-Based Digital Signature Standard" (ML-DSA), FIPS 204, August 2024.

---

[Previous: 38. Information Classification Support](38-information-classification-support.md) | [Contents](../README.md) | [Next: 40. Cryptographic Certification Roadmap](40-cryptographic-certification-roadmap.md)

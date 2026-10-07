<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix A - Cryptographic Algorithm Reference

*Table 74: Algorithms and libraries used in the described release*

| Purpose | Algorithm / construction | Library or component | Reference |
| --- | --- | --- | --- |
| **Message key agreement** | PQXDH: X25519 + Kyber-1024, HKDF | libsignal (current release) | PQXDH rev. 3; RFC 7748 |
| **Message encryption** | Triple Ratchet (Double Ratchet + SPQR); AES-256-CBC + HMAC-SHA256 per message, inside a versioned content envelope | libsignal (current release) | Double Ratchet spec; SPQR |
| **Identity signatures** | XEdDSA over Curve25519 | libsignal | XEdDSA spec |
| **File encryption** | AES-256-GCM, 96-bit IV, 128-bit tag, random key per file | noble ciphers library (mobile); Node.js crypto (desktop) | FIPS 197; SP 800-38D |
| **Call frames** | AES-256-GCM with HKDF-SHA256 key ratchet | Patched WebRTC SDK (mobile); livekit-client (desktop) | RFC 5869 |
| **Call transport** | DTLS-SRTP, AEAD_AES_256_GCM | SANKET LiveKit build (pion) | RFC 5764; RFC 7714 |
| **TLS** | TLS 1.3 AES-256-GCM-SHA384; TLS 1.2 ECDHE AES-256-GCM; TURN over TLS on TCP 443: TLS 1.3 TLS_AES_256_GCM_SHA384 only | nginx; Node.js | RFC 8446; RFC 5289 |
| **TLS key exchange** | Hybrid X25519MLKEM768 (X25519 + ML-KEM-768) offered first, then X25519 and secp384r1 | nginx with a current OpenSSL release; Node.js LTS | FIPS 203; RFC 7748 |
| **Device mutual TLS** | ECDSA P-256 key generated on the device; X.509 certificate issued under the customer CA with an opaque name; CRL at the edge | Platform crypto (device); OpenBao PKI (issuing); nginx (verification) | RFC 5280 |
| **Local database** | SQLCipher (AES-256, HMAC-SHA256 page authentication) | SQLCipher | SQLCipher design |
| **Local key envelope** | X25519 ECDH + HKDF-SHA256 + AES-256-GCM | noble curves and ciphers; Node.js crypto | RFC 7748; RFC 5869 |
| **Push payloads** | AES-256-GCM, fixed-size padding | Node.js crypto | SP 800-38D |
| **Locate** | HPKE (DHKEM X25519, HKDF-SHA256, AES-256-GCM) | libsignal (phone); noble (browser) | RFC 9180 |
| **Hardware key protection** | iOS: Secure Enclave P-256 ECDH + HKDF-SHA256, deriving an AES-256-GCM key; Android: Keystore key in StrongBox or the TEE | Platform crypto | RFC 5869; SP 800-38D |
| **Android Key Attestation** | X.509 attestation chain verified offline to vendor roots shipped in the release, with a per-release revocation snapshot | SANKET API | RFC 5280 |
| **Signed device lists** | XEdDSA signatures by the account list key over each list version, each list chained to the hash of the previous one | libsignal | XEdDSA spec |
| **Passwords** | Argon2id (64 MiB, t=3, p=4) | argon2 | RFC 9106 |
| **Second factor** | TOTP, HMAC-SHA256, 6 digits, 30 s | SANKET implementation | RFC 6238 |
| **Administrator security keys** | WebAuthn assertions with ES256 (ECDSA P-256, SHA-256) or EdDSA (Ed25519); user verification required | Node.js crypto | W3C WebAuthn Level 2; RFC 8032 |
| **Federated sign-in** | OIDC: authorisation code with PKCE S256, ID token signature verified with asymmetric algorithms only (no none or HMAC). SAML 2.0: assertion signature by the pinned certificate, RSA-SHA256 or stronger | SANKET API | OpenID Connect Core 1.0; SAML 2.0 |
| **Passcode and vault** | PBKDF2-HMAC-SHA256, 600,000 iterations; AES-256-GCM | Platform crypto | SP 800-132 (PBKDF2) |
| **Session tokens** | JWT HS256 (HMAC-SHA256) | SANKET API | RFC 7519; RFC 8725 |
| **Webhooks** | HMAC-SHA256 over timestamp and body | Node.js crypto | RFC 2104 |
| **Integration secrets, invite codes** | SHA-256 hashes; HMAC-SHA256 for activation codes | Node.js crypto | FIPS 180-4 |
| **Licences, releases (with sequence number and expiry), release timestamps, status reports, backup manifests, audit anchors** | Ed25519 (release timestamps under a key separate from the release key) | OpenBao Transit (signing); noble curves / Node.js (verification) | RFC 8032 |
| **Audit chain** | SHA-256 hash chain, head signed periodically (Ed25519 anchor) | SANKET API; OpenBao Transit | FIPS 180-4; RFC 8032 |
| **Backups (released)** | AES-256-GCM per artefact; recovery key under scrypt; data keys wrapped by OpenBao Transit; Ed25519-signed SHA-256 manifest | Node.js crypto; OpenBao Transit | SP 800-38D; RFC 7914; RFC 8032 |
| **TOTP secrets at rest** | AES-256-GCM, key derived with HKDF-SHA256 from a dedicated installation secret, bound to the account | Node.js crypto | SP 800-38D; RFC 5869 |

*Libraries are chosen under SANKET's audited-library policy (Chapter 40). libsignal is used without modification.*

---

[Previous: 50. Technical FAQ](50-technical-faq.md) | [Contents](../README.md) | [Next: Appendix B - Key Hierarchy](appendix-b-key-hierarchy.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 10. Cryptographic Architecture

SANKET layers several independent cryptographic mechanisms. Each protects a different asset at a different point, and each is described here as implemented in the baseline release, with the primary specification it follows.

*Table 16: Cryptographic layers*

| Layer | Protects | Mechanism | Status |
| --- | --- | --- | --- |
| **Transport** | All client to server traffic, including the TURN media relay on TCP 443 | TLS 1.3 (TLS_AES_256_GCM_SHA384) or TLS 1.2 (ECDHE with AES-256-GCM); hybrid post-quantum key exchange (X25519MLKEM768) offered first in TLS 1.3; the TURN relay hop TLS 1.3 with TLS_AES_256_GCM_SHA384 only | **IMPLEMENTED** |
| **Device authentication at the edge** | Which device a connection comes from | Mutual TLS with a per-device client certificate (ECDSA P-256 key generated on the device, issued by the installation's OpenBao PKI under the customer CA) | **CONFIGURABLE** |
| **Message E2EE** | Message, reaction and encrypted-broadcast content, including classification labels | The current audited libsignal release: PQXDH + Double Ratchet with SPQR; AES-256-CBC + HMAC-SHA256 per message; a versioned content envelope inside every message | **IMPLEMENTED** |
| **File E2EE** | Attachment content, names, types | AES-256-GCM with a random 256-bit key per file, key carried inside the libsignal message | **IMPLEMENTED** |
| **Call E2EE** | Voice and video frames | AES-256-GCM frame encryption with per-sender keys ratcheted by HKDF-SHA256 and delivered over libsignal | **IMPLEMENTED** |
| **Call transport** | Media between client and SFU | DTLS-SRTP with AEAD_AES_256_GCM only | **IMPLEMENTED** |
| **Push payload** | Android wake-up content | AES-256-GCM per device, fixed-size padding | **IMPLEMENTED** |
| **Local storage** | Data at rest on endpoints | SQLCipher (AES-256 page encryption, HMAC-SHA256 page authentication) and AES-256-GCM files under a wrapped 256-bit key; on phones the protocol store and device secrets encrypted with AES-256-GCM under a hardware-backed key (Secure Enclave on iOS, StrongBox or the TEE on Android) | **IMPLEMENTED** |
| **Server secrets** | Credentials and keys at rest on server | Argon2id hashing; AES-256-GCM field encryption; OpenBao Transit (non-exportable); identity-provider secrets in OpenBao | **IMPLEMENTED** |
| **Signatures** | Licences, releases, status reports, prekeys, device links, device lists | Ed25519; XEdDSA (libsignal), including the per-account device-list key | **IMPLEMENTED** |
| **Administrator authentication** | Administrator console sign-in | FIDO2 security keys (WebAuthn): ES256 (ECDSA P-256 with SHA-256) or EdDSA (Ed25519) assertions verified by the installation | **IMPLEMENTED** |
| **Backups** | Full-installation archives; scheduled database backups | Archives and scheduled backups: AES-256-GCM per artefact; the recovery key under a scrypt passphrase-derived key, data keys wrapped by OpenBao Transit; Ed25519-signed manifest | **IMPLEMENTED** |

> [!IMPORTANT]
> **Key Point - AES-256 or fail**
>
> SANKET's engineering policy is that every message, attachment, call frame, SRTP media stream and local cache is protected with AES-256, and that every TLS session the installation terminates or opens (the edge, the TURN relay hop and outbound connections) uses AES-256-GCM, with no AES-128 fallback and no compatibility mode. The policy is enforced in code: a shared helper refuses any AES-GCM key that is not exactly 32 bytes or any nonce that is not 12 bytes, a pre-commit check rejects code that constructs AES-GCM any other way, and the server refuses any call participant that does not support the AES-256 media profile.
>
> The one upstream exception is the cipher suite of the WebRTC DTLS handshake, which protects no voice, video or message data; Chapter 19 explains why it is accepted and how every call records it.

## 10.1 Transport Protection

All client connections, REST and WebSocket, terminate at the edge over TLS.[^5] The edge configuration offers exactly the following:

*Table 17: Edge TLS configuration*

| Parameter | Setting |
| --- | --- |
| **Protocol versions** | TLS 1.3 and TLS 1.2. Nothing below TLS 1.2. |
| **TLS 1.3 cipher suites** | TLS_AES_256_GCM_SHA384 only |
| **TLS 1.2 cipher suites** | ECDHE-ECDSA-AES256-GCM-SHA384 and ECDHE-RSA-AES256-GCM-SHA384 only[^6] |
| **Excluded** | AES-128 suites, ChaCha20-Poly1305, CBC-mode suites, static RSA key exchange |
| **Key exchange groups** | X25519MLKEM768 (hybrid X25519 plus ML-KEM-768, TLS 1.3 only) offered first, then X25519 and secp384r1. A client that offers the hybrid group negotiates it; a classical client is served classically and never refused |
| **TURN relay on TCP 443** | Terminated by the edge with TLS 1.3 and TLS_AES_256_GCM_SHA384 only; AES-128, ChaCha20 and TLS 1.2 refused on this hop. The edge reads only the TLS server name to route the TURN name to this terminator and everything else to the HTTPS hosts, without decrypting anything for that decision |
| **Client certificates** | Device client certificates are requested and verified on the device routes (see Mutual TLS below) |
| **Session tickets** | Disabled |
| **HSTS** | max-age of two years, includeSubDomains, preload[^7] |
| **Other headers** | Content-Security-Policy default-src 'none' for API responses; X-Frame-Options DENY; Referrer-Policy no-referrer |
| **Outbound TLS** | Syslog, webhook, SMTP relay and identity-provider connections from the installation use a minimum of TLS 1.2 with the same AES-256-GCM-only policy and refuse servers that offer only AES-128 or ChaCha20; the server runtime offers the hybrid post-quantum group on these connections too, and the SMTP relay's certificate is verified (optionally against a configured CA) |
| **Negotiated-group log** | The edge records only the negotiated key-exchange group name per connection, with no client address, URI or user, so the share of post-quantum connections can be measured |
| **Administrator plane (vendor console)** | TLS 1.3 only |

### 10.1.1 Handshake, authentication and forward secrecy

In both TLS 1.3 and the permitted TLS 1.2 suites the session key is established with ephemeral elliptic-curve Diffie-Hellman, so a later compromise of the server's certificate private key does not decrypt recorded sessions. TLS 1.3 additionally encrypts the server certificate and most of the handshake. Server authentication is by X.509 certificate.[^8] TLS 1.2[^9] is retained solely for client platform network stacks that do not yet support TLS 1.3, and only with AEAD AES-256-GCM suites and forward secrecy. This conforms to current IETF guidance, which recommends TLS 1.3 and permits TLS 1.2 with AEAD suites and forward secrecy.[^10]

In TLS 1.3 the edge offers the hybrid group X25519MLKEM768 first. Its shared secret combines an X25519 agreement with an ML-KEM-768 encapsulation,[^11] so a recorded session stays confidential unless both are broken: an attacker who later obtains a quantum computer cannot recover it from X25519 alone, and a cryptographic weakness in ML-KEM cannot make the session weaker than X25519 alone; the residual risk is an implementation defect in the newer code. Clients whose TLS stack offers the group use it; others negotiate X25519 or secp384r1, and message content stays post-quantum protected by libsignal in either case (Chapter 39).

### 10.1.2 Certificates and private PKI

The edge certificate is installed in one of two modes. In automatic mode it is obtained by ACME DNS-01 validation, which needs no inbound port 80. In import mode the operator supplies a certificate and key issued by the customer's own certificate authority, which is the mode used for closed and air-gapped networks. Internal services can be given an additional trusted CA bundle. Chapter 26 describes the recommended PKI hierarchy.

### 10.1.3 Certificate validation and pinning

Clients validate the server certificate chain against their trust store; on managed devices that trust store carries the customer CA. The desktop client can additionally enforce certificate pinning: when pinning is required, the presented certificate's SHA-256 fingerprint must match a configured pin, and chain-validation failures are always rejected. Mobile clients pin the edge natively: the SHA-256 of the edge certificate's public key (SubjectPublicKeyInfo), or of its issuing CA, a current and at least one backup pin, compiled into the signed app for the deployment's own domain and its subdomains, the media server included. On Android the platform network security configuration enforces them for every connection; on iOS App Transport Security enforces them for the HTTPS API, and a native check in the WebSocket layer enforces the same pins for the real-time connection. A handshake that presents no pinned key is refused before any request is sent, and the app refuses to transmit at all if the native pinning layer is absent.[^12]

### 10.1.4 Mutual TLS

Every approved mobile and desktop device holds its own client certificate. The device generates an ECDSA P-256 key pair (on phones inside the hardware-backed store, Chapter 11), builds the certificate signing request itself and sends only the request; the installation's OpenBao PKI issues a short-lived certificate under the customer CA. The certificate carries an opaque random name and no user or device identifier, because in TLS 1.2 a client certificate travels unencrypted. Devices present it on the REST API, the chat WebSocket and the media signalling connection on iOS, Android and desktop; on desktop it is used only by the Electron main process and the renderer never sees the key. The edge verifies the certificate against the PKI CA and its certificate revocation list, and the API binds the verified fingerprint to the device record, so a token stolen from one device cannot be used with another device's certificate.

Enforcement is configurable per tenant. Device-bound tokens continue to be checked on every request (Chapter 13); the certificate is an additional, independent proof of the device. Chapter 14 describes the certificate lifecycle and Chapter 26 its place in the PKI.

## 10.2 End-to-End Message Encryption

SANKET uses Signal's official libsignal library[^13] for all message encryption, at the same release on every platform: the Swift package on iOS, the Java/Android packages on Android, and the Node.js package on desktop and server. On mobile a thin native module calls the library; on desktop it runs in the Electron main process. The server uses libsignal only to verify signatures: on uploaded keys, device-link approvals, restore challenges and signed device lists. SANKET contains no reimplementation of the Signal Protocol and no alternative message cipher.

### 10.2.1 Keys per device

*Table 18: libsignal key material per device*

| Key | Type | Lifetime in SANKET | Purpose |
| --- | --- | --- | --- |
| **Identity key pair** | Curve25519 (X25519 for agreement; XEdDSA signatures)[^14] | Long-term; account-wide rotation supported with proof of the old key and TOTP step-up | Authenticates the device in key agreement; signs prekeys, device-link approvals and restore challenges |
| **Signed prekey** | Curve25519, signed by identity key | Rotated regularly | Medium-term key for asynchronous session start |
| **Kyber prekeys** | Kyber-1024 KEM keys, signed by identity key | One-time Kyber prekeys, replenished automatically, deleted by the server when handed out and by the device once used; plus a last-resort key, rotated regularly with the signed prekey and used only when no one-time key remains | Post-quantum component of the initial key agreement |
| **One-time prekeys** | Curve25519 | Uploaded in batches and replenished automatically; deleted by the server when handed out | Adds a single-use DH contribution to session start |
| **Account list key pair** | Curve25519 (XEdDSA signatures), a separate libsignal key pair per account | Long-term; generated on the primary device; sealed in the encrypted vault backup for recovery | Signs the account's device list, which senders verify before encrypting (Chapter 13) |
| **Registration ID** | Random integer | Per installation of the app | Detects reinstallation |
| **Session state** | Root key, chain keys, ratchet keys | Per session, continuously ratcheted | Derives a fresh key for every message |

The server accepts a prekey bundle only from a device in the trusted state, refuses legacy or malformed bundles, and verifies both the signed-prekey and Kyber-prekey signatures against the identity key with libsignal before storing them. Every bundle must contain the Kyber last-resort prekey; one-time Kyber prekeys are verified in the same way, and the server refuses an id that is already published for the device with another key.

### 10.2.2 Session establishment: PQXDH

Sessions are established with PQXDH, the post-quantum extension of X3DH.[^15][^16] The initiator fetches the recipient device's bundle and computes three or four X25519 agreements[^17] (identity-to-signed-prekey, ephemeral-to-identity, ephemeral-to-signed-prekey, and ephemeral-to-one-time-prekey when one is available) plus a Kyber encapsulation to one of the recipient device's one-time Kyber prekeys, or to its last-resort Kyber prekey when none remains. The shared secret is derived from all of these inputs with HKDF.[^18] The first message carries the initiator's identity and ephemeral public keys and the KEM ciphertext, so the recipient can complete the agreement while offline when the message was sent.

```text
SK = KDF( DH(IK_A, SPK_B) || DH(EK_A, IK_B) || DH(EK_A, SPK_B) [ || DH(EK_A, OPK_B) ] || SS )
     where SS = Kyber shared secret encapsulated to PQPK_B   (PQXDH, Revision 3)
```

The security properties follow from the specification. The X25519 components provide mutual authentication and classical secrecy; the Kyber component means that an adversary who records traffic today and later obtains a quantum computer cannot recover the session key from the recorded handshake alone. Authentication remains classical: PQXDH does not protect against an active quantum adversary impersonating a party. PQXDH has been formally analysed.[^19] The server hands out each one-time Kyber prekey once and the device deletes it after use, so the post-quantum component of a handshake is single-use, and devices replenish their stock automatically.

### 10.2.3 Message encryption: the Double Ratchet

After session start, messages are encrypted with the Double Ratchet.[^20] A symmetric-key ratchet advances a chain key with HMAC-SHA256 for every message, producing a fresh message key; a Diffie-Hellman ratchet mixes a new X25519 agreement into the root key whenever the direction of conversation changes. Each message key is expanded with HKDF into an AES-256 key, an HMAC-SHA256 key and an IV; the message is encrypted with AES-256[^21] in CBC mode with PKCS#7 padding and authenticated with HMAC-SHA256[^22] computed over the identity keys and the message. Message keys are deleted after use. Out-of-order messages are handled by retaining skipped message keys within libsignal's limits.

The libsignal release in use also runs Signal's Sparse Post-Quantum Ratchet (SPQR) alongside the Double Ratchet for all sessions, mixing ML-KEM-derived secrets into message keys, a combination Signal calls the Triple Ratchet.[^23] An attacker must therefore break both the elliptic-curve and the post-quantum components to recover message keys (Chapter 39).

### 10.2.4 Content envelope

Inside every libsignal message the plaintext is a versioned content envelope with three kinds: an ordinary message, a reaction (the target message id and the emoji, or its removal) and an encrypted broadcast. The envelope carries a message id generated by the sender, identical in every per-device copy, and an optional classification label for the message and for each attachment (Chapter 38), so the label is end-to-end encrypted and the server can neither read nor alter it. An encrypted broadcast's priority, acknowledgement deadline, sent time and expiry are also inside its envelope, so a hostile server cannot downgrade a critical broadcast, move its deadline or replay an old copy: clients refuse a copy whose envelope disagrees with the server's record. The sender is always the libsignal-authenticated sender of the message, never a field in the content. Mobile and desktop produce byte-identical envelopes.

### 10.2.5 Message authentication, replay and identity

- **Integrity and origin.** Every message carries a MAC under a key that only the two session endpoints can derive; any modification causes decryption to fail.
- **Replay.** A replayed message fails because its message key has already been consumed; libsignal reports it as a duplicate and SANKET discards it.
- **Identity pinning.** The first identity key seen for a peer device is stored; a later different key is refused by the client's identity store (trust on first use, then fail closed).
- **Signed device lists.** Before encrypting, a sender checks the recipient account's device list, signed by that account's list key, and sends no copy to a device that is not on it (Chapter 13).
- **Verification.** On mobile, users can compare a 60-digit safety number, or scan it as a QR code. Each party's half is derived with SHA-256 from that party's account identifier and libsignal identity key, contributing 96 bits of hash output per party.
- **Identity-change notice.** When a contact's identity key changes, the server tells everyone who shares a conversation with them. Each of those conversations shows "&lt;name&gt;'s security code changed. Verify it before sharing sensitive information." with a link to verification, and the contact is marked unverified until the safety number is confirmed again, on mobile and desktop alike. A device that was offline when the key changed receives the notice when it next connects, because the server keeps it for each recipient device until that device acknowledges it. The notice only informs: libsignal's own trust handling is unchanged, and an event about someone the user shares no conversation with is ignored.
- **Session repair.** A session is reset only after repeated decryption failures on recent messages, excluding duplicates and missing-session errors; the peer is told through a rate-limited, membership-checked channel.

### 10.2.6 No fallback

A client refuses to send if libsignal encryption cannot be performed; devices that do not publish a libsignal identity key are skipped rather than sent plaintext; legacy plaintext markers are rejected; and the server rejects key bundles and devices that do not follow the libsignal format. There is no plaintext or alternative-cipher path for message content: no client derives a static per-conversation key, and conversation previews show only what libsignal decrypted on the device.

## 10.3 Group Encryption

SANKET encrypts group messages by pairwise fan-out: the sending device encrypts the message separately for every trusted device of every active group member, each through its own libsignal session, and submits all copies in one request. It does not use libsignal Sender Keys.[^24] The consequences are:

- **Membership changes need no re-keying.** A removed member's devices are simply no longer in the fan-out list. Membership is locked inside the send transaction so a message is never addressed to a device that has just left.
- **Each copy has full Double Ratchet properties**, including forward secrecy and post-compromise recovery per device pair.
- **Authenticity is per sender device**, because each copy is authenticated by its own session.
- **Cost grows with group size times devices.** The server caps fan-out size, and the group size limit is configurable.
- **Compromised member handling.** Revoking a compromised member's device removes it from all future fan-outs immediately. Messages that device already decrypted are not recoverable by any design.

Multi-device delivery follows the per-device session model of Signal's Sesame specification: each device has its own address and its own sessions.[^25] The list of a member's devices is supplied by the server, but every account's device list is signed by that account's list key, and the sending device verifies the signed list before it encrypts on every fan-out path (one-to-one and group messages, reactions, identity rotation, call frame keys, locate, encrypted broadcasts and the file keys inside messages). A device the server lists but the signed list does not receive no copy, and the user is told why. The server can still withhold delivery, which is an availability matter (Chapters 14 and 28).

Reactions and encrypted broadcasts use the same pairwise fan-out. A reaction is a small libsignal message to every device in the conversation. An encrypted broadcast is encrypted by the sending desktop of a broadcast-sender account for every recipient device, each through its own Double Ratchet session, with no sender keys (Chapter 15).

## 10.4 File Encryption

Attachments are encrypted on the sending device before upload. For each file the client generates a random 256-bit key and a random 96-bit IV, optionally compresses the file, and encrypts it with AES-256-GCM, producing IV, ciphertext and a 128-bit authentication tag.[^26] File name and media type are encrypted separately. The file key and file identifier are placed inside the message body, which libsignal encrypts to each recipient device. Because every file has a fresh random key, a GCM key is never reused, which removes the nonce-reuse risk that limits GCM.[^27] Integrity is the GCM tag; a modified object fails to decrypt. Clients never send the file key to the server, the server discards any copy it receives, and a file is always opened with the key carried in its message. Chapter 18 describes the full lifecycle.

## 10.5 Endpoint Data at Rest

Each user's local database on mobile and desktop is a SQLCipher database[^28] keyed with a random 256-bit data-encryption key (DEK). The mobile app refuses to open the database unless it can prove at run time that SQLCipher is compiled in, which prevents a silent fallback to unencrypted SQLite. Cached attachments, thumbnails, voice notes and avatars are stored as AES-256-GCM files under the same DEK. On iOS the attachment, thumbnail and voice files, and the short-lived decrypted copies a viewer needs, additionally carry the Complete data-protection class, so they are unreadable while the device is locked. Android keeps app files in credential-encrypted storage.

The DEK is never stored in the clear. It is wrapped with an envelope: an ephemeral X25519 key pair is agreed with the user's device X25519 public key, the shared secret is passed through HKDF-SHA256 with a salt bound to the Sanket ID and tenant, and the result wraps the DEK with AES-256-GCM. The device private key is in the phone's secure store with this-device-only accessibility, itself encrypted with AES-256-GCM under a hardware-backed key: on iOS a key derived from a Secure Enclave P-256 key (ECDH and HKDF-SHA256), on Android a StrongBox key where the phone has one and a TEE key otherwise (Chapter 11). On desktop, the corresponding secrets are held in the operating system's protected credential store and can be wrapped a second time under a passcode-derived key held only in memory. On sign-out the DEK is zeroed and the per-user keys deleted; a remote wipe erases the databases, files and keys.

## 10.6 Server Data at Rest

- **Content** on the server is already end-to-end ciphertext, so its confidentiality does not depend on server-side encryption.
- **Passwords** are hashed with Argon2id (64 MiB memory, 3 iterations, parallelism 4).[^29]
- **SMTP and SMS credentials and webhook secrets** are encrypted with AES-256-GCM under a server-held key.
- **TOTP secrets** are encrypted with AES-256-GCM under a key held outside the database, derived from a dedicated installation secret, and each ciphertext is bound to its account, so it cannot be moved to another account. The server refuses to start without that secret.
- **Signing and wrapping keys** live in OpenBao as non-exportable Transit keys.[^30] The device certificate authority is an OpenBao PKI mount whose signing key stays inside OpenBao.
- **Identity-provider secrets** are held in the secrets service, never in the tenant configuration. Administrator security keys are stored only as public keys with their sign counters.
- **Volumes.** Database and object-store volumes rely on disk encryption provided by the host, which is part of the deployment baseline (Chapter 42). Object-store server-side encryption is configured with a KMS secret but is not relied upon for confidentiality, because objects are client-encrypted.

## 10.7 Backup Encryption

The backup tooling writes the database dumps, the attachment bucket, OpenBao data and configuration as separate artefacts, each under its own AES-256-GCM key. The recovery artefact has its key wrapped under a key derived from an operator passphrase with scrypt; every other artefact's key is wrapped by the installation's OpenBao. A manifest of SHA-256 checksums is signed with a non-exportable Ed25519 key, and the restore tool verifies the signature and every artefact before changing anything. Because message and file content inside the archive is already end-to-end encrypted, the backup never contains readable communications.

Scheduled backups use envelope encryption. Each artefact is encrypted with its own randomly generated 256-bit key in AES-256-GCM; that key is wrapped by the installation's OpenBao Transit engine under a non-exportable key, and only the wrapped form is stored. Each run produces a manifest (source, copies, sizes, SHA-256 checksums, wrapped keys) signed with a non-exportable Ed25519 key in the same OpenBao and stored next to every copy. A restore verifies the manifest signature against that key before anything else, then the artefact checksum, and the authentication tag protects the data while it is decrypted; a manifest signed by any other key, an edited manifest or an altered artefact is refused. A stolen set of backup copies is therefore useless without the installation's OpenBao. Adapter credentials are themselves stored encrypted by OpenBao. Chapter 32 covers backup operations.

---

[^5]: E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.3", IETF RFC 8446, August 2018.
[^6]: E. Rescorla, "TLS Elliptic Curve Cipher Suites with SHA-256/384 and AES Galois Counter Mode (GCM)", IETF RFC 5289, August 2008.
[^7]: J. Hodges, C. Jackson, A. Barth, "HTTP Strict Transport Security (HSTS)", IETF RFC 6797, November 2012.
[^8]: D. Cooper et al., "Internet X.509 Public Key Infrastructure Certificate and CRL Profile", IETF RFC 5280, May 2008.
[^9]: T. Dierks, E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", IETF RFC 5246, August 2008.
[^10]: Y. Sheffer, P. Saint-Andre, T. Fossati, "Recommendations for Secure Use of TLS and DTLS", IETF RFC 9325 (BCP 195), November 2022.
[^11]: NIST, "Module-Lattice-Based Key-Encapsulation Mechanism Standard" (ML-KEM), FIPS 203, August 2024.
[^12]: OWASP Foundation, "Pinning Cheat Sheet", cheatsheetseries.owasp.org.
[^13]: Signal Messenger LLC, "libsignal" (Rust implementation with Java, Swift and TypeScript bindings), AGPL-3.0, github.com/signalapp/libsignal.
[^14]: T. Perrin, "The XEdDSA and VXEdDSA Signature Schemes", Signal specification, Revision 1, October 2016. signal.org/docs/specifications/xeddsa/
[^15]: E. Kret, R. Schmidt, "The PQXDH Key Agreement Protocol", Signal specification, Revision 3, May 2023 (updated January 2024). signal.org/docs/specifications/pqxdh/
[^16]: M. Marlinspike, T. Perrin, "The X3DH Key Agreement Protocol", Signal specification, Revision 1, November 2016. signal.org/docs/specifications/x3dh/
[^17]: A. Langley, M. Hamburg, S. Turner, "Elliptic Curves for Security" (X25519, X448), IETF RFC 7748, January 2016.
[^18]: H. Krawczyk, P. Eronen, "HMAC-based Extract-and-Expand Key Derivation Function (HKDF)", IETF RFC 5869, May 2010.
[^19]: K. Bhargavan, C. Jacomme, F. Kiefer, R. Schmidt, "Formal verification of the PQXDH Post-Quantum key agreement protocol for end-to-end secure messaging", USENIX Security 2024.
[^20]: T. Perrin, M. Marlinspike, "The Double Ratchet Algorithm", Signal specification. signal.org/docs/specifications/doubleratchet/
[^21]: NIST, "Advanced Encryption Standard (AES)", FIPS PUB 197 (updated 2023).
[^22]: H. Krawczyk, M. Bellare, R. Canetti, "HMAC: Keyed-Hashing for Message Authentication", IETF RFC 2104, February 1997.
[^23]: Signal, "Signal Protocol and Post-Quantum Ratchets" (Sparse Post-Quantum Ratchet, SPQR), signal.org/blog/spqr/, October 2025.
[^24]: Signal Messenger LLC, libsignal source, group cipher (Sender Key) implementation, github.com/signalapp/libsignal (rust/protocol/src/group_cipher.rs).
[^25]: M. Marlinspike, T. Perrin, "The Sesame Algorithm: Session Management for Asynchronous Message Encryption", Signal specification, April 2017. signal.org/docs/specifications/sesame/
[^26]: M. Dworkin, "Recommendation for Block Cipher Modes of Operation: Galois/Counter Mode (GCM) and GMAC", NIST SP 800-38D, November 2007.
[^27]: M. Dworkin, "Recommendation for Block Cipher Modes of Operation: Galois/Counter Mode (GCM) and GMAC", NIST SP 800-38D, November 2007.
[^28]: Zetetic LLC, "SQLCipher Design" (AES-256 page encryption with HMAC page authentication), zetetic.net/sqlcipher/design/.
[^29]: A. Biryukov, D. Dinu, D. Khovratovich, S. Josefsson, "Argon2 Memory-Hard Function for Password Hashing and Proof-of-Work Applications", IETF RFC 9106, September 2021.
[^30]: OpenBao project (Linux Foundation), "OpenBao: secrets management, encryption as a service and privileged access management", openbao.org.

---

[Previous: 9. Data Architecture](09-data-architecture.md) | [Contents](../README.md) | [Next: 11. Key Management Architecture](11-key-management-architecture.md)

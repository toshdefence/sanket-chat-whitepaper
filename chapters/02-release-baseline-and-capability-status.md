<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 2. Release Baseline and Capability Status

This chapter fixes the release that the whitepaper describes and summarises, in one place, the status of every major security capability and the design boundaries that an evaluator should understand.

## 2.1 Baseline Described

*Table 2: Software baseline described in this whitepaper*

| Component | Baseline |
| --- | --- |
| **Platform release** | SANKET platform as built at the date of this edition (October 2026), including the versioned end-to-end encrypted content envelope, encrypted broadcasts, classification labels, administrator security keys, signed device lists, device mutual TLS and the high-availability profile |
| **Mobile application** | SANKET for iOS and Android (React Native, Expo) |
| **Desktop application** | SANKET for desktop (Electron, on a supported release line), companion device linked from a mobile device |
| **Messaging cryptography** | The current Signal libsignal release on iOS (LibSignalClient), Android (libsignal-client / libsignal-android), desktop and server (@signalapp/libsignal-client) |
| **Media server** | SANKET build of the open-source LiveKit server, pinned at a fixed source commit, patched to accept only the AEAD_AES_256_GCM SRTP profile and to restrict TURN relay peers to the node's own media address |
| **Server runtime** | Containerised services on Docker Compose: nginx edge, SANKET API and WebSocket, notifier, scheduler and analytics processor on a supported LTS runtime, PostgreSQL, Redis, MinIO, OpenBao, ntfy, Postfix, LiveKit |
| **Deployment profile** | Single-host ("Edge") profile, and a multi-host high-availability profile with an asynchronous second site, both on Docker Compose (Chapter 32). Kubernetes is on the roadmap. |

This public edition does not state exact component versions. They are given to evaluators in the controlled edition.

Capabilities are verified by automated unit, route and integration tests, an end-to-end suite run against a running installation, release freeze and rollback tests, and media-server proof tests that perform real encrypted-media handshakes.

## 2.2 Capability Status Summary

*Table 3: Status of principal security capabilities in the described baseline*

| Area | Capability | Status | Notes |
| --- | --- | --- | --- |
| **Messaging** | libsignal PQXDH + Double Ratchet, one ciphertext per recipient device, 1:1 and groups | **IMPLEMENTED** | One-time Kyber (ML-KEM family) prekeys served first, each once, with the last-resort Kyber prekey only when none remain; server verifies prekey signatures with libsignal |
| **Messaging** | Post-quantum ratchet (SPQR, "Triple Ratchet") | **IMPLEMENTED** | Provided by the current libsignal release, in which SPQR runs alongside the Double Ratchet for all sessions (Chapter 39) |
| **Messaging** | Versioned content envelope inside every libsignal message (messages, reactions, encrypted broadcasts) | **IMPLEMENTED** | Carries a sender-generated message id identical in every per-device copy and the optional classification label; mobile and desktop produce byte-identical envelopes (Chapter 17) |
| **Messaging** | Reactions end-to-end encrypted | **IMPLEMENTED** | Each reaction is a small libsignal message; the server stores only opaque per-device rows and each device computes the counts. A tenant option lets the server keep an emoji-free count for very large groups (Chapters 17 and 20) |
| **Messaging** | Sender identity visible to the server | **BY DESIGN** | An organisational platform authorises, rate-limits and audits every sender; the operator is the customer itself. Sealed sender is a roadmap item (Chapter 20) |
| **Messaging** | Identity pinning (trust on first use, fail closed) and safety-number verification | **IMPLEMENTED** | When a contact's identity key changes, every conversation shared with them shows a notice, also on devices that were offline at the time, and the contact stays unverified until the safety number is compared again (mobile and desktop) |
| **Messaging** | Signed device lists (self-hosted key transparency) | **IMPLEMENTED** | Each account's device list is signed with an account list key held on the primary device; the installation keeps an append-only log and refuses a list that does not extend the previous one; senders verify the signed list before encrypting on every fan-out path, so a device added without the account's signature receives nothing (Chapters 14 and 28) |
| **Messaging** | Server strips non-essential message metadata | **IMPLEMENTED** | Only the idempotency key and a link-preview dismissal are kept beside the ciphertext; replies, locations, contact cards, link previews and forwards travel inside it (Chapter 20) |
| **Files** | Per-file AES-256-GCM on device, key delivered inside the libsignal message | **IMPLEMENTED** | Chapter 18 |
| **Calls** | 1:1 and group voice and video through a self-hosted SFU | **IMPLEMENTED** | Group calls off by default |
| **Calls** | AES-256-GCM frame encryption with per-sender keys delivered over libsignal; SRTP AEAD_AES_256_GCM only | **IMPLEMENTED** | Server refuses any device or offer below the AES-256 profile; clients end any call whose media is not AES-256-GCM; every call, one-to-one included, has its own server-issued call id that names its media room and binds its frame keys (Chapter 19) |
| **Calls** | Key renewal: five new sender keys per second for every participant, plus a fresh key on every join and leave | **IMPLEMENTED** | Chapter 19 |
| **Calls** | Operation on 3G, 4G and constrained links: Opus with DTX at 24 or 12 kbps, VP8 simulcast, automatic adaptation | **IMPLEMENTED** | Chapter 19 |
| **Calls** | Media over TCP 443 and TURN over TLS | **IMPLEMENTED** | One public TCP 443 carries HTTPS and TURN, routed by TLS server name without decryption; TURN over TLS is terminated with TLS 1.3 and TLS_AES_256_GCM_SHA384 only, and relays only to the installation's own media address. On by default for internet-facing installations, off for air-gapped ones; UDP media remains the primary path (Chapter 25) |
| **Clients** | Native apps for Android, iOS, Windows, macOS and Linux; no end-user web client by design | **IMPLEMENTED** | Chapter 7 |
| **Transport** | TLS 1.3 (TLS_AES_256_GCM_SHA384 only) and TLS 1.2 (ECDHE AES-256-GCM only); HSTS | **IMPLEMENTED** | TLS 1.2 retained only for client platform stacks that do not yet offer TLS 1.3, with the same AES-256-GCM-only policy; outbound syslog and webhooks use the same policy |
| **Transport** | Hybrid post-quantum key exchange at the edge (X25519MLKEM768 first, then X25519 and secp384r1) | **IMPLEMENTED** | Negotiated wherever the client platform's TLS stack supports it, with platform coverage widening on the roadmap. Cipher suites unchanged; the server's outbound TLS offers the hybrid group too (Chapter 39) |
| **Transport** | Certificate pinning | **IMPLEMENTED** | Desktop: configurable certificate pinning. Mobile: native pinning of the edge public key on Android and iOS (REST and WebSocket), configured in the customer build |
| **Transport** | Device mutual TLS: a client certificate for every approved mobile and desktop device | **IMPLEMENTED** | P-256 key generated on the device (hardware-backed on phones), issued under the customer CA with an opaque name, short-lived, revoked through a CRL at the edge; presented on REST, the chat WebSocket and media signalling. Configurable per tenant (Chapter 16) |
| **Identity** | Argon2id passwords, TOTP (SHA-256), device-bound tokens, refresh rotation with reuse detection | **IMPLEMENTED** | TOTP as the users' second factor; each code accepted once; TOTP secrets AES-256-GCM bound to the account; device restore requires a libsignal signature over a single-use server challenge |
| **Identity** | Administrator FIDO2 security keys (WebAuthn) | **IMPLEMENTED** | Verified by the installation itself with no external service; ES256 and EdDSA; user verification required; synced passkeys refused by default; suspected clones refused. Policy can require a security key for every administrator (Chapter 13) |
| **Identity** | Administrator sign-in through the customer's own OIDC or SAML 2.0 identity provider; LDAP check for end users at enrolment, restore and new-device sign-in | **OPTIONAL** | The identity provider is a first factor only, the local second factor is still required; public identity providers refused; no just-in-time accounts (Chapter 13) |
| **Identity** | SCIM 2.0 user provisioning; scoped Integration API | **IMPLEMENTED** | Users resource; OpenAPI documented |
| **Devices** | Device approval, caps, revocation, server-confirmed remote wipe, lost mode, locate, duress PIN, app passcode, wipe after failures, MDM managed configuration | **IMPLEMENTED** | Chapter 14 |
| **Devices** | Device posture (root / jailbreak) enforcement | **IMPLEMENTED** | Local root and jailbreak detection on the phone, reported and audited, with configurable enforcement; MDM managed configuration remains the authoritative posture source |
| **Devices** | Hardware-backed device keys (Secure Enclave on iOS; StrongBox, or the TEE where absent, on Android) and Android Key Attestation | **IMPLEMENTED** | The libsignal store, device secrets, the device certificate key and the account list key are protected by a hardware key; protection level recorded per device and policy can require one; attestation verified offline against vendor roots shipped in the release, with no vendor service contacted (Chapters 11, 14 and 33) |
| **Local data** | SQLCipher database and AES-256-GCM file cache under a wrapped data-encryption key, mobile and desktop; decrypted viewer copies on mobile deleted when the viewer closes, when the app backgrounds and at start-up | **IMPLEMENTED** | Chapters 10 and 18 |
| **Groups** | Group creation by users or, under the administrators-only policy, by administrators from the console, audited | **IMPLEMENTED** | Chapters 15 and 27 |
| **Messaging** | Disappearing messages: one shared duration table, 30 seconds to 90 days | **IMPLEMENTED** | A forced tenant duration is a ceiling: the shorter of it and the user's own choice applies (Chapter 17) |
| **Content handling** | Clipboard copy and file forwarding and sharing policy, mobile and desktop | **IMPLEMENTED** | Configurable per tenant; enforced in the mobile and desktop apps, with documents still opening inside the app (Chapters 15 and 18) |
| **Classification** | Customer-defined ranked classification labels on messages and attachments, carried inside the end-to-end encrypted envelope | **IMPLEMENTED** | The server can neither read nor alter a label. Content above the lowest rank cannot be forwarded, exported or downloaded on mobile or desktop; an unknown label is treated as the highest rank; a forward never lowers a label. Conversation-level labels on the roadmap; separation between accredited levels by installation remains recommended (Chapter 38) |
| **Broadcast** | End-to-end encrypted broadcasts with priority, acknowledgement tracking and full-screen flash alerts | **IMPLEMENTED** | Sent from a dedicated broadcast-sender account's single approved desktop, encrypted for every recipient device with libsignal; priority, deadline and expiry are also inside the envelope, so the server cannot downgrade, move or replay them; lock-screen text is generic by priority; unacknowledged urgent and critical broadcasts re-notified at the deadline with an administrator alert (Chapter 20) |
| **Broadcast** | Notice broadcasts for non-sensitive announcements | **BY DESIGN** | Server-readable; sent and scheduled from the administration console and labelled "Notice (not end-to-end encrypted)" to sender and recipients (Chapter 20) |
| **Audit** | SHA-256 hash-chained audit log with periodic Ed25519 anchors signed in OpenBao, verifier, syslog (RFC 5424 over TLS), pull API, signed webhooks | **IMPLEMENTED** | Chapter 21 |
| **Detection** | Account lockout, rate limiting, refresh-token reuse detection and real-time alerting to the administration console and the customer SIEM | **IMPLEMENTED** | Chapter 22 |
| **Analytics** | First-party usage analytics with an organisation switch and an installation lock | **IMPLEMENTED** | Counts only, pseudonymous, never content, no third party; on by default. The organisation can switch it off (step-up authentication, high-severity audit) and a lock set at provisioning forces it off; when off, clients collect nothing and collected analytics can be deleted. Audit logs are not analytics and stay on |
| **Server data** | Non-exportable signing and wrapping keys in OpenBao; field encryption of secrets | **IMPLEMENTED** | Chapter 10 |
| **Supply chain** | Ed25519-signed release manifest with per-image SHA-256, a sequence number, an expiry and per-platform minimum app versions; signed timestamp for connected upgrades; offline release package; automatic rollback | **IMPLEMENTED** | Install and upgrade refuse an unsigned, expired or lower-sequence manifest and any downgrade; two releases claiming one sequence are always refused; a running installation never stops because its manifest expires; CycloneDX SBOM verified on offline and connected upgrades (Chapter 30) |
| **Release safety** | Per-platform minimum app versions, encrypted restore point before every upgrade, health-gated cutover, encrypted outbox for text, attachments and voice notes | **IMPLEMENTED** | Below-minimum apps can be refused per platform (configurable); every upgrade failure rolls back automatically; calls survive an API-only upgrade (Chapter 30) |
| **Containers** | SANKET containers non-root with all capabilities dropped and a read-only root filesystem in the Edge profile; every image pinned by digest | **IMPLEMENTED** | Hardening exceptions are recorded and justified in the controlled edition (Chapter 42) |
| **Licensing** | Ed25519-signed licence verified offline; trusted-time rollback detection | **IMPLEMENTED** | A licence may state a minimum app version, enforced on every platform (Chapter 31) |
| **Availability** | Single-host profile with health checks; per-artefact AES-256-GCM backups (data keys wrapped by OpenBao, the recovery artefact's key by a passphrase) with Ed25519-signed manifests, verified before restore; scheduled database backups to NAS, S3-compatible storage or SFTP | **IMPLEMENTED** | Chapter 32 |
| **Availability** | High-availability multi-host profile on Docker Compose with an asynchronous second site | **IMPLEMENTED** | Two edge hosts, N+1 API and WebSocket nodes, PostgreSQL streaming replication with fenced failover, Redis with Sentinel, distributed MinIO with site replication, three-node OpenBao, multiple media nodes. Reference topology designed for RPO 5 minutes and RTO 1 hour; each deployment's disaster-recovery drill records its measured figures. Kubernetes on the roadmap (Chapter 32) |
| **Watermarking** | Visible watermark in the desktop and mobile file viewers | **IMPLEMENTED** | Configurable; forensic (invisible) watermarking on the roadmap |

## 2.3 Design Boundaries and Their Rationale

Every secure platform makes deliberate choices about what the server must process in order to deliver the service. The boundaries below are stated so that evaluators can scope risk precisely; each is accompanied by the reason for it and the controls available to the customer.

*Table 4: Design boundaries, rationale and customer controls*

| Boundary | Rationale | Customer control | Chapter |
| --- | --- | --- | --- |
| **Routing and account metadata (sender account and device, recipient devices, time, size band, membership) is visible to the server** | Required to authorise, deliver, rate-limit and audit communications inside an accountable organisation; in a self-hosted deployment the operator is the customer. Sealed sender is on the roadmap | Retention windows; separation of database and audit access | 20 |
| **Optional large-group reaction counts reveal who reacted to which message** | For very large groups, a tenant option lets the server keep an emoji-free count per message so that such groups do not depend on every device holding every reaction | Leave the option off; disable reactions by policy | 17, 20 |
| **Notice broadcasts are server-readable by design** | An administrator announcement channel for non-sensitive content that can be scheduled from the console; it is labelled "Notice (not end-to-end encrypted)" to sender and recipients | Use encrypted broadcasts or conversations for sensitive content; notice retention is configurable (90 days by default) | 20, 37 |
| **The optional identity vault stores an encrypted copy of the user's identity key** | Lets users recover their account after device loss; protected by a key derived on the device (PBKDF2-HMAC-SHA256, 600,000 iterations, AES-256-GCM) | Enable or disable the vault by policy and licence | 11, 13 |
| **Out-of-band assurance of a contact's identity comes from safety-number comparison** | Device additions are covered by signed device lists; identity keys are pinned and any change is flagged until compared again, the model used by mainstream Signal-protocol deployments | Safety-number comparison; identity-change notice in every shared conversation; device inventories; administrator device approval | 14, 28 |
| **Connected installations may use Apple push for iOS wake-ups** | iOS background wake-up is available only through Apple; it carries no user data. The media server never uses public STUN: its address is set at installation | Run iOS without Apple push; air-gapped installations do not use it | 19, 43 |
| **High availability across sites is asynchronous** | Synchronous replication between distant sites would tie every write to the inter-site link; the reference topology is designed for RPO 5 minutes and RTO 1 hour | Each disaster-recovery drill records the measured figures; zero data loss across sites is not part of the built profile | 32 |

---

[Previous: 1. Executive Summary](01-executive-summary.md) | [Contents](../README.md) | [Next: 3. Who SANKET Is For](03-who-sanket-is-for.md)

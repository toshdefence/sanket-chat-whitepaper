<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 9. Data Architecture

This chapter classifies every major category of data held by an installation according to what protects it. The classification is the basis for the server-compromise analysis in Chapter 28.

## 9.1 Data Stores

*Table 14: Data stores and their contents*

| Store | What it holds | Protection |
| --- | --- | --- |
| **PostgreSQL** | Accounts, devices (including app version and hardware protection level), public key directory, append-only log of signed device lists, per-device message and reaction ciphertext and routing data, groups and membership, policy, broadcast metadata and per-device encrypted broadcast copies, notice broadcasts, security-key public keys, call logs, audit chain, integration records | Content E2EE; secrets field-encrypted or hashed; no host port; disk encryption is a deployment control; in the high-availability profile, streaming replication with fenced failover and WAL archiving to the second site |
| **Redis** | Two instances: a cache for data that can be rebuilt, and a state instance for short-lived session, real-time delivery and call state. The usage-analytics event stream | Password authentication; internal network; append-only persistence on the state instance; Sentinel failover in the high-availability profile |
| **MinIO** | Encrypted attachment objects, including encrypted broadcast attachments (uploaded once); encrypted identity-vault blobs (versioned) | Client-side AES-256-GCM before upload; server-side AES-256-GCM at rest; lifecycle expiry for attachments; distributed per site with site replication in the high-availability profile |
| **OpenBao** | Non-exportable signing and wrapping keys; issuance of device client certificates | Sealed at rest; Shamir unseal; internal network only; in the high-availability profile, a three-node raft cluster with TLS between nodes and encrypted snapshots shipped to the second site |
| **Licence and trust bundle** | Signed licence and the public keys used to verify it | Ed25519 signatures; mounted read-only |
| **Threat database (optional)** | Security event stream and alerts for the optional processor | Separate PostgreSQL instance |
| **Endpoint storage** | Message history, attachments, protocol state, keys | SQLCipher, AES-256-GCM file cache, platform key stores (Chapter 11) |

## 9.2 Classification of Server-Held Data

*Table 15: Server-held data by protection class*

| Class | Examples | Readable by a database operator? |
| --- | --- | --- |
| **End-to-end encrypted content** | Message bodies (libsignal ciphertext, one row per recipient device); reactions (opaque per-device rows); encrypted broadcasts, including the priority, deadline, sent time and expiry carried inside them; classification labels of messages and attachments; attachment objects; file names and types; vault blobs | No |
| **Server-encrypted secrets** | TOTP secrets (AES-256-GCM, bound to the account); SMTP and SMS credentials and webhook secrets (AES-256-GCM); backup key wraps (OpenBao Transit) | Only with the corresponding server-held key |
| **Secret-derived verifiers** | Password hashes (Argon2id); vault proof verifier (SHA-256); invitation and activation code hashes; integration secret hashes (SHA-256) | Hash only; resistant to reversal subject to secret strength |
| **Public key material** | Identity keys, signed prekeys, Kyber prekeys, one-time prekeys, identity revocations; signed device lists and account list public keys; security-key public keys; device client certificates (opaque random names) | Yes, by design (public) |
| **Account and organisation metadata** | Sanket ID, display name, email, phone, profile fields, roles, group names and descriptions, membership, device information, app version, platform and build, hardware protection level | Yes |
| **Communication metadata** | Sender account and device, recipient device, conversation, timestamps, delivery status, ciphertext size band, call participants and durations; that a reaction was sent, by whom and to which devices, never the emoji; replies, link previews, locations, contacts and forwards are inside the ciphertext | Yes |
| **Large-group reaction counts** | Only when the tenant option for very large groups is on: an emoji-free count per message, so the server learns who reacted to which message but not with what | Yes |
| **Broadcast metadata** | Encrypted broadcasts: sender account, audience, priority, deadline, expiry and receipts | Yes |
| **Notice broadcasts** | Title, body and attachments of notice-type broadcasts from the administrator console, labelled to senders and recipients as not end-to-end encrypted | Yes |
| **Audit metadata** | Actor, action, target, IP address, user agent, device, result, hash links | Yes, to holders of the audit-read permission and database operators |

## 9.3 Lifecycle of Stored Ciphertext

Message ciphertext is held on the server only as long as delivery requires. When every recipient device copy of a message has been delivered, the scheduler blanks the ciphertext, initialisation-vector field and any metadata of all copies after a short window, leaving a stub for ordering and receipts; stubs are later hard-deleted. Messages with a disappearing-message expiry are deleted when that time passes. Whole message partitions older than the hot-retention window are dropped. These windows are configurable within limits (Chapter 44). Reactions follow the same lifecycle as other messages. Each per-device copy of an encrypted broadcast is deleted as soon as that device confirms it has stored the broadcast, and any copy still undelivered is deleted when the broadcast expires.

> [!TIP]
> **Security Property - Effect on historical exposure**
>
> Because delivered ciphertext is removed from the server, a later compromise of the database exposes far less history than the volume of traffic might suggest, and even that ciphertext is protected by the forward secrecy of the Double Ratchet (Chapter 12).

---

[Previous: 8. Service Architecture](08-service-architecture.md) | [Contents](../README.md) | [Next: 10. Cryptographic Architecture](10-cryptographic-architecture.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 6. Security Architecture Principles

Twenty-four principles govern SANKET's design. Each is stated with the specific mechanism through which the current release applies it, so that the principle can be tested rather than taken on trust.

*Table 9: Security architecture principles and how SANKET applies them*

| # | Principle | How SANKET applies it |
| --- | --- | --- |
| 1 | **Zero trust** | No request is trusted because of where it comes from. Every API call carries a device-bound token and a device client certificate verified at the edge; the device's trust state is re-checked on every request, so revocation, lost mode or suspension takes effect almost immediately (Chapter 16). |
| 2 | **Server-blind content, not "zero knowledge"** | The server cannot decrypt message bodies, reactions, encrypted broadcasts, classification labels, file contents or call media because it never holds the keys. That is a precise and verifiable property. SANKET does not describe itself as a zero-knowledge system in the cryptographic sense, because an accountable organisational platform must know accounts, devices, membership and delivery metadata (Chapter 20). |
| 3 | **End-to-end encryption** | Content is encrypted on the sending device and decrypted only on recipient devices, using libsignal for messages, reactions and encrypted broadcasts and per-file AES-256-GCM keys for attachments. The server relays ciphertext. |
| 4 | **Least privilege** | Administrative capabilities are divided into permission categories with separate actions. Built-in roles grant the minimum for a job, and custom roles cannot exceed the permissions of their creator. Service credentials are scoped to the operations they need. |
| 5 | **Defence in depth** | Content is protected by libsignal inside TLS; local data by SQLCipher inside the operating system's storage encryption; calls by frame encryption inside SRTP. Access is checked at the edge, in middleware and in each route. |
| 6 | **Minimise plaintext exposure** | Plaintext exists only on endpoints. Notification payloads carry no content by default, and broadcast lock-screen text is generic by priority. The desktop holds keys in the main process, away from the renderer. |
| 7 | **Minimise metadata** | Push payloads carry only routing identifiers; Android wake-ups are sealed, padded to a fixed size and mixed with decoy traffic; edge logs mask identifiers in request paths; delivered ciphertext is tombstoned after a short retention window. Metadata that remains is stated in Chapter 20. |
| 8 | **Explicit device trust** | Devices are first-class security principals with states (pending, trusted, lost, revoked), per-platform caps, optional administrator approval, a recorded hardware protection level and an inventory that users and administrators can act on. Each account's device list is signed by its own devices. |
| 9 | **Strong identity before access** | Argon2id-verified password plus a second factor before any token is issued: TOTP (SHA-256) for users; a FIDO2 security key for administrators. |
| 10 | **Cryptographic authentication** | Device-link approvals are signed with the phone's libsignal identity key and verified by the server with libsignal. Device lists are signed with a per-account libsignal key and verified by every sender before encrypting. Prekeys are signed and verified. Administrator security keys sign a fresh WebAuthn challenge. Licences, release manifests and tenant status reports are Ed25519-signed. |
| 11 | **Compartmentalisation** | One installation per customer, with no shared database or tenant identifier columns. Within an installation, conversations are separate cryptographic sessions per device pair. |
| 12 | **Failure containment** | Cryptographic failures fail closed: a client refuses to send without libsignal encryption, a call ends if its media cannot be secured, a strict helper refuses any AES key that is not 256 bits. |
| 13 | **Tamper evidence** | The audit log is a SHA-256 hash chain whose integrity can be verified on demand and whose links are carried into SIEM exports. |
| 14 | **Secure by default** | Defaults include TOTP required, invitation-only registration, member approval for groups, security keys for administrators, notification previews off, file download to device off, link previews off, message forwarding off and group calls off. |
| 15 | **Customer-controlled deployment** | The customer installs, configures, upgrades and administers every component. |
| 16 | **Offline survivability** | Licence checks are local; Android wake-ups can use the self-hosted push relay; clients queue outbound messages in an encrypted outbox when offline. |
| 17 | **No hidden runtime dependency** | Product applications contain no third-party analytics or crash-reporting SDK. Desktop builds are scanned for foreign hosts at build time and restricted to the tenant's hosts at run time. Exceptions, such as Apple push for iOS, are documented (Chapter 43). |
| 18 | **Segmentation** | Only the HTTPS edge and media ports are exposed; the secrets service is on an internal-only network. Finer zoning is a deployment recommendation (Chapter 25). |
| 19 | **Revocation** | Device revocation, lost mode, account suspension, refresh-token revocation and device certificate revocation are enforced by the server on the next request, and remote wipe is executed only after a confirmed revocation. |
| 20 | **Auditability** | Security-relevant actions by users, administrators, integrations and the system are recorded by Sanket ID with outcome and severity. |
| 21 | **Crypto-agility** | Call crypto is negotiated by profile and enforced by the server; prekey formats and the content envelope are versioned; the edge offers hybrid post-quantum key exchange first and classical groups after it; the release and licence trust bundles support key rotation and revocation (Chapter 39). |
| 22 | **Administrative separation** | Tosh Defence's licensing and release console is a separate system with no path into customer data. Customer administrators are separated by role. |
| 23 | **Secure software supply chain** | Frozen dependency lockfile, pinned upstream sources, pre-commit cryptographic policy checks and signed release manifests (Chapter 30). |
| 24 | **Controlled update architecture** | Releases are pinned versions installed by the customer, verified before load (signature, sequence and expiry), preceded by an encrypted restore point and rolled back automatically if the post-upgrade health and smoke checks fail. |

## 6.1 Using Precise Terminology

Security documents often blur distinct properties under broad labels. SANKET uses the following distinctions throughout this whitepaper.

*Table 10: Distinguishing confidentiality properties*

| Term | Meaning in this document | Applies to SANKET? |
| --- | --- | --- |
| **Server inability to decrypt (E2EE)** | The server never holds keys that decrypt content | Yes, for message bodies, reactions, encrypted broadcasts, classification labels, file contents and call frames |
| **Encryption at rest** | Stored data is encrypted on disk, with keys held separately | Endpoints: yes (SQLCipher, AES-256-GCM cache). Server: content is E2EE ciphertext; disk encryption is a deployment control |
| **Administrative metadata** | Accounts, devices, roles, policy, audit records | Visible to authorised administrators by design |
| **Routing metadata** | Sender, recipient device, conversation, time, size | Visible to the server; needed to deliver messages |
| **Server-readable content by design** | Content the server can read because the customer chose a channel that is not end-to-end encrypted | Notice broadcasts only, labelled as not end-to-end encrypted. With the large-group reaction option on, the server also learns who reacted to which message, never the emoji (Chapter 20) |
| **Zero-knowledge proof / system** | A protocol in which a party proves a statement without revealing anything else, or a service that learns nothing about its users | Not claimed. SANKET's server learns metadata |

> [!NOTE]
> **Note - Why SANKET avoids the phrase "zero knowledge"**
>
> The phrase is often used loosely to mean that the server holds only ciphertext of content. SANKET uses the more precise term server-blind content, because a messaging server necessarily learns who is communicating, and lists that metadata explicitly.

---

[Previous: 5. Threat Model](05-threat-model.md) | [Contents](../README.md) | [Next: 7. System Architecture](07-system-architecture.md)

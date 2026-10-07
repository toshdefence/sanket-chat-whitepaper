<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 44. Privacy Architecture

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

SANKET is designed to collect what it needs to deliver communications securely, to keep it inside the customer's boundary, and to delete it on a defined schedule. Customers remain the controllers of personal data in their installations; this chapter describes the technical measures, not legal compliance.

## 44.1 Principles Applied

- **Data minimisation:** content is end-to-end encrypted, including reactions, encrypted broadcasts and classification labels; phone and email are optional; notifications carry no content by default; the user directory is not enumerable.
- **Purpose limitation:** metadata is used for delivery, security and accountability; usage analytics are first-party, content-free counters computed within the installation, which the organisation can switch off and an installation can lock off, after which nothing is collected.
- **Administrator visibility** is limited by role and never extends to content.
- **Deletion:** delivered ciphertext is tombstoned; disappearing messages expire; retention windows prune metadata; account self-deletion (optional) revokes devices and removes the account from use.
- **Access records:** administrator actions and security events are audited.

## 44.2 Retention Schedule

*Table 70: Default retention of server-held data*

| Data | Default retention | Mechanism |
| --- | --- | --- |
| **Message ciphertext and its metadata (reactions included)** | 72 hours after delivery to all copies | Scheduled tombstoning job |
| **Encrypted broadcast ciphertext** | Until each device confirms it has stored its copy, otherwise until the broadcast expires | Deleted per device on confirmation; expiry |
| **Message stubs** | 90 days | Hard deletion |
| **Disappearing messages** | At expiry | Scheduled purge job |
| **Message partitions** | 90-day hot window (configurable) | Partition drop |
| **Broadcast records (notice content; encrypted broadcast metadata), recipients and notifications** | 90 days (configurable) | Partition drop |
| **Call logs** | 365 days (configurable), rolled up into daily statistics | Batched deletion |
| **Security alerts** | 365 days (configurable) | Partition drop |
| **Notification delivery metrics** | 90 days | Partition drop |
| **Attachment objects** | 90 days | Object-storage lifecycle |
| **Vault backup versions** | Current plus non-current versions for 30 days | Object-storage versioning |
| **Analytics rollups** | 400 days (configurable, minimum 30 days); none collected while analytics is off | Purge job; "Delete all collected analytics" on demand |
| **Webhook delivery records** | 30 days | Worker deletion |
| **Audit log** | Indefinite unless pruning is explicitly enabled (default setting 365 days) | Chapter 21 |
| **Backups** | 14 days, minimum seven archives | Backup script |

*Shrinking a retention window is itself an audited administrative action.*

## 44.3 Personal Data Inventory

Personal data held by the server comprises the Sanket ID, display name, optional email and phone, profile fields, device information (platform, model, app version, hardware protection level and the fingerprint of the device's client certificate, which itself carries an opaque random name), each account's signed device list, IP addresses and user agents in audit records, group memberships and names, and communication metadata (Chapter 20). Where federated sign-in is configured, it also holds the mapping from the customer's identity provider or directory to the administrator account or Sanket ID; accounts are never created from the provider. Email and phone are stored in clear.

The server does not hold the emoji of any reaction (where the large-group count option is on, it learns only who reacted to which message), the title, body or attachments of an encrypted broadcast (only its audience, priority, deadline, expiry and receipts; notice broadcasts remain readable), or the classification label of any message or file. Usage analytics are pseudonymous counts, collected only while the organisation's switch is on and never on an installation locked against them; the organisation can delete everything collected at any time. Audit records and the app version each device reports for update enforcement are not analytics and are kept regardless. End users can review their own security activity. Export of user data by users is off by default.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 43. External Dependency Analysis](43-external-dependency-analysis.md) | [Contents](../README.md) | [Next: 45. Security and Usability Trade-offs](45-security-and-usability-trade-offs.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 1. Executive Summary

<p align="center"><a href="https://www.sanket.chat"><img src="../images/sanket-logo.png" alt="SANKET by Tosh Defence - sovereign secure communications platform" width="320"></a></p>

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

SANKET is a sovereign secure information exchange platform. It gives an organisation end-to-end encrypted messaging, file exchange, voice and video, and priority broadcast with acknowledgement, on infrastructure that the organisation owns, operates and can disconnect from the public internet.

## 1.1 The Sovereign Communications Problem

High-trust organisations increasingly depend on instant communications for operational decisions. Most of that traffic runs on services that the organisation neither owns nor controls. Encryption is now common in those services, but encryption on its own answers only one question: whether a third party on the network can read a message. It does not answer the questions that matter most to a defence ministry, an intelligence service or a critical infrastructure operator:

- **Jurisdiction.** Which government can compel the service provider to hand over data, change the software or stop the service?
- **Operational dependency.** Does the service keep working if the provider is unreachable, sanctioned, compromised or simply decides to change its terms?
- **Telemetry and analytics.** What does the software report back to its vendor, and to which third-party analytics, crash-reporting or advertising services?
- **Metadata.** Even when content is encrypted, who can see who talked to whom, when, from where and how often?
- **Cloud dependency.** Are content, keys, logs or backups held in infrastructure the organisation cannot inspect?
- **Disconnected operation.** Can the system run on a closed network, in an enclave or at a site without internet access?
- **Administrator compromise.** If an administrator account, or the vendor's own staff, is compromised, what can the attacker read or change?
- **Endpoint compromise.** What protection remains when a phone is lost, seized or infected?
- **Software supply chain.** How does the organisation know that an update is genuine, and who decides when it is installed?
- **Ownership.** Who owns the infrastructure, the keys, the identities and the audit trail?

A platform that encrypts content but leaves these questions to a foreign provider leaves the most valuable information about an organisation, its structure, its tempo and its relationships, outside its control.

## 1.2 The SANKET Proposition

SANKET is designed so that every one of those questions has an answer that sits inside the customer's own security boundary. Its security rests on six reinforcing properties:

*Table 1: The six properties of the SANKET security proposition*

| Property | What it means in SANKET |
| --- | --- |
| **Sovereignty** | One dedicated installation per customer, run with Docker Compose on customer-chosen infrastructure. No vendor-operated service in the message path. No third-party analytics or crash-reporting SDK in any product application. |
| **Cryptography** | Messages, reactions, encrypted broadcasts and classification labels are encrypted with Signal's official, audited libsignal library (the current release on iOS, Android, desktop and server), using PQXDH key agreement with a mandatory post-quantum Kyber prekey and the Double Ratchet with its post-quantum companion ratchet. Files are encrypted on the device with AES-256-GCM. Transport is TLS with AES-256-GCM cipher suites only, and the edge offers hybrid post-quantum key exchange to clients that support it. |
| **Identity** | Customer-controlled identities identified by Sanket ID. Argon2id password hashing, TOTP with SHA-256 as the users' second factor, FIDO2 security keys for administrators, device-bound session tokens with single-use refresh rotation, and device lists signed by each account's own devices so that the server cannot silently add a device. Sign-in can be federated to the customer's own identity provider, never to a public one. |
| **Isolation** | A deployment needs no cloud service. It can run on a closed or air-gapped network with an internal certificate authority, self-hosted push for Android, offline licence validation and offline release bundles. |
| **Auditability** | A SHA-256 hash-chained audit log of security and administrative events, recorded by Sanket ID, verifiable on demand and exportable to the customer's SIEM over TLS-protected syslog. Audit records never contain message content. |
| **Operational control** | The customer decides when to install a release, what features are enabled, how long data is retained, which devices are trusted, and when a device is revoked, placed in lost mode or remotely wiped. |

> *Sovereignty + Cryptography + Identity + Isolation + Auditability + Operational Control*

No single property is sufficient. Strong cryptography on a vendor-operated service still leaves jurisdiction and metadata exposure. A self-hosted service with weak identity controls still lets an attacker sign in. SANKET's position is that the combination is what produces a defensible security posture, and the rest of this document examines each part on its merits.

## 1.3 What This Document Establishes

- A threat model covering twenty-three threat actors, from passive network observers to a malicious system administrator and a compromised update package (Chapter 5).
- An end-to-end account of how messages, files and calls are encrypted, which keys exist, where each is generated and held, and what each protects (Chapters 10 to 12 and 19).
- A precise inventory of the metadata the server necessarily handles, and why (Chapter 20).
- An analysis of the effect of a server or endpoint compromise, and why compromise of the server yields no message content (Chapters 28 and 29).
- Reference architectures for single-site, segmented, air-gapped and resilient deployments, with the parts that are implemented separated from the parts that are recommended or planned (Chapters 23 to 26 and 32).
- An explicit statement of what SANKET does not claim (Chapter 46).

> [!IMPORTANT]
> **Key Point - How claims are made in this document**
>
> Every technical statement in this whitepaper was checked against the SANKET platform source code at the release described in Chapter 2. Where a capability is planned, optional or dependent on deployment choices, the text says so, and every design boundary is stated with its rationale. Cryptographic statements were checked against the primary specifications cited in the footnotes.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Contents](../README.md) | [Next: 2. Release Baseline and Capability Status](02-release-baseline-and-capability-status.md)

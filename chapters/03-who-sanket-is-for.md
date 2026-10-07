<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 3. Who SANKET Is For

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

SANKET is built for organisations whose communications would cause serious harm if they were read, altered, withheld or mapped by an outside party, and that cannot accept an outside party holding the infrastructure, the keys or the logs.

The common factor is not a sector. It is a security requirement: confidentiality and integrity must hold even if the communications provider, the hosting provider or the network is not trusted. That rules out designs in which a third party operates the service, holds the content or decides when it changes. The environments below share that requirement.

*Table 5: Representative high-trust deployment environments*

| Environment | Typical driver | Most relevant SANKET properties |
| --- | --- | --- |
| **Defence and armed forces organisations** | Operational security, command communications, dispersed units, contested networks | Self-hosted deployment, end-to-end encryption, end-to-end encrypted priority broadcast with acknowledgement, classification labels, device revocation and remote wipe |
| **Intelligence and national security organisations** | Compartmentalised communications, insider risk, counter-intelligence | Customer-held infrastructure and keys, audit metadata without content, duress and lost-device controls, usage analytics that can be locked off for the whole installation |
| **Ministries and government departments** | Policy deliberation, inter-departmental coordination, jurisdictional control | Sovereign deployment, administrator-provisioned identities, sign-in through the department's own identity provider, retention policy |
| **Law-enforcement agencies** | Case confidentiality, field coordination, integrity of instructions | End-to-end encryption, device trust, audit trail of administrative actions |
| **Critical infrastructure operators (energy, water, transport)** | Incident coordination during outages, segmented operational networks | Operation on closed networks, offline delivery queues, self-hosted push options |
| **Telecom operators** | Network operations and security operations coordination | Self-hosted deployment, integration API, syslog export to internal SIEM |
| **Financial institutions and regulators** | Market-sensitive information, regulatory confidentiality | Customer-controlled logs and retention, role-based administration |
| **Research, aerospace and strategic manufacturing** | Intellectual property, export-controlled programmes | Encrypted file exchange, classification labels that block forwarding, export and download, controlled user population |
| **Executive leadership and board communications** | Targeted surveillance of senior staff | End-to-end encrypted voice, video and messaging; identity-key change warnings |
| **Incident-response and security operations teams** | Out-of-band communications when primary systems may be compromised | Independent infrastructure, separate identity domain, offline-capable deployment |
| **Allied and friendly governments** | Need for a platform they can host and inspect themselves | No mandatory runtime dependency on the vendor; customer-operated update and licensing |

> [!IMPORTANT]
> **Key Point - Not a consumer product**
>
> SANKET has no public service that anyone can join, no global directory and no vendor-operated server that end users connect to. Users exist only inside an installation the customer controls, and the customer decides how they are admitted: by administrator provisioning, by directory provisioning, or by single-use invitation (the default). That removes several attack paths that consumer services have by design, including account creation by an arbitrary adversary, contact discovery across organisations, and vendor-side access to the user graph.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 2. Release Baseline and Capability Status](02-release-baseline-and-capability-status.md) | [Contents](../README.md) | [Next: 4. Sovereignty as a Security Control](04-sovereignty-as-a-security-control.md)

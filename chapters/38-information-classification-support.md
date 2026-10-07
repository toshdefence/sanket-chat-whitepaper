<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 38. Information Classification Support

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Many customers handle information under a classification scheme, whether national markings or customer-defined labels. This chapter states plainly what SANKET does and does not do in that respect.

> [!NOTE]
> **Note - Approach in the current platform**
>
> SANKET supports classification at two levels. Within an installation, each message and each attachment can carry a label from a customer-defined ranked list of up to eight, carried inside the end-to-end encrypted message so that the server can neither read nor alter it, and the label drives what recipients can do with the content. Between formally accredited levels, the recommended separation remains one installation per security domain: each installation is a self-contained security domain, which is the strongest separation available and does not rely on software labels to keep levels apart.

## 38.1 Classification Labels

*Table 61: Classification labels within an installation*

| Element | Behaviour | Status |
| --- | --- | --- |
| **Label list** | Defined by the customer in the administrator console: up to eight labels, each with an identifier, a name and a rank from 0 to 7, and a default label preselected when composing. Every change is audited. Callers that have not signed in never receive the label names, since they describe the deployment | **CONFIGURABLE** |
| **Where a label travels** | Inside the end-to-end encrypted content envelope (Chapter 17), for the message and for each attachment. The server never sees a label, and clients no longer send a free-text classification field with file uploads | **IMPLEMENTED** |
| **Handling** | Content whose effective label ranks above the lowest configured rank cannot be forwarded, exported or downloaded (saved to the device or shared out of the app) on mobile or desktop, the desktop secure viewer included; the desktop main process enforces the download restriction as well as its interface. Content at the lowest rank, and unlabelled content, follows the installation's normal policy. Copying stays governed by the clipboard policy | **IMPLEMENTED** |
| **Effective label** | The stricter of the message label and the file label; for each, the stricter of the rank carried in the message and the rank currently configured for that label, so lowering a rank in the console never unlocks content that was sent at a higher one. A label the device does not know is treated as the highest rank (fail closed) | **IMPLEMENTED** |
| **Forwarding** | Where forwarding is allowed, a forward never carries a lower label than its source | **IMPLEMENTED** |
| **Audit** | Devices report coarse events (label applied, action blocked by a label) to the audit log under the user's Sanket ID and device, with no message identifier, conversation identifier or content (Chapter 21) | **IMPLEMENTED** |
| **Parity** | Mobile and desktop apply the same handling rule from the same shared policy code | **IMPLEMENTED** |
| **Conversation-level labels and a per-label handling matrix** | Labels attached to a whole conversation, and handling rules configured separately for each label (for example retention or audience by label), are not provided | **ROADMAP** |

Labels are an aid to handling discipline among authorised users of one installation. They rely on the recipient's app honouring them; a recipient who can read content can still photograph the screen or retype it, which is why screenshot blocking, visible watermarks (Chapter 18) and managed devices complement them, and why labels do not replace separation between accredited levels.

## 38.2 Separation Between Security Domains

- **Separate installations per security domain.** Because each installation is fully independent, customers can run one installation per classification level or compartment, on networks accredited for that level. This is the strongest separation available and is how high-trust customers should segregate formally accredited levels.
- **Policy per installation.** Each installation's policy (download, export, forwarding, retention, screenshot blocking, disappearing messages) can be set to match the handling rules for the level it carries.
- **Membership-based access.** Within an installation, access to content follows conversation membership, with member approval and administrator-controlled group creation.

## 38.3 Policy Support Is Not Accreditation

Classification-aware handling does not by itself authorise a system to process government classified information. Formal accreditation of a system for a given classification level is a decision of the competent national authority, based on assessment of the whole system: platform, infrastructure, endpoints, personnel and procedures. SANKET is not accredited for any national classification level at the date of this document.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 37. Feature Catalogue](37-feature-catalogue.md) | [Contents](../README.md) | [Next: 39. Crypto-Agility and Post-Quantum Readiness](39-crypto-agility-and-post-quantum-readiness.md)

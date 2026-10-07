<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 47. Why Air-Gapped SANKET Is Structurally Different

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Most security improvements reduce the probability of an attack succeeding. An isolated deployment does something different: it removes whole categories of attack path, because the parties and systems on those paths are no longer involved.

## 47.1 Trust Removed

*Table 72: Categories of external trust removed by an air-gapped deployment*

| Category removed | Attack paths that disappear |
| --- | --- |
| **Foreign cloud provider** | Provider insiders, provider compromise, cross-tenant attacks, provider-side key access |
| **External legal jurisdiction** | Compelled disclosure or modification by a foreign authority |
| **External analytics and telemetry** | Leakage of usage patterns and device data to third parties |
| **Internet identity systems** | Account takeover through a third-party identity provider |
| **Public runtime APIs** | Service disruption or data exfiltration through API dependencies |
| **Public storage and CDNs** | Content or code substitution by a third-party host |
| **External key management** | Key access by a cloud KMS operator |
| **External key directories and attestation services** | Device or key substitution by a third-party key transparency service; dependence on a platform vendor's online attestation service (signed device lists are kept by the installation, and Android attestation is verified offline) |
| **External logging** | Disclosure of security events to a log provider |
| **External licence validation** | Remote disablement through a licensing service |
| **Internet adversaries** | Remote exploitation, scanning, volumetric denial of service from the internet |

## 47.2 Control Retained

What remains is inside the customer's control: servers, keys, identities, network, storage, audit records, updates, certificates, backups, administrators, policies and monitoring. Every remaining risk is a risk the customer can see, govern and test.

> [!TIP]
> **Security Property - The structural argument**
>
> In a conventional service, the customer's security depends on the security of every external party in the delivery chain, most of which the customer cannot inspect. In an air-gapped SANKET installation, combined with end-to-end encryption that keeps content from the customer's own servers, security depends on the customer's own people, endpoints and infrastructure, and on the integrity of the software it chose to install, which it can verify. That is a smaller, inspectable trust base.

The remaining risks are real: insiders, endpoint compromise, the integrity of the release chain, and the operational discipline of the customer's own teams. Chapters 27 to 30 address them.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 46. What SANKET Does Not Claim](46-what-sanket-does-not-claim.md) | [Contents](../README.md) | [Next: 48. Comparative Architecture](48-comparative-architecture.md)

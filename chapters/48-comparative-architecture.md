<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 48. Comparative Architecture

The comparison below is between architectural categories, not named products. Individual products within a category vary; the columns describe typical properties of the category. For the SANKET column, the properties are those of the described release. Protocol properties such as forward secrecy and post-compromise security differ materially between products in every category and should be checked against published analyses rather than assumed.

*Table 73: Architectural categories compared*

| Property | A. Consumer cloud messenger | B. Enterprise cloud collaboration platform | C. Self-hosted encrypted platform (typical) | D. Sovereign air-gapped SANKET deployment |
| --- | --- | --- | --- | --- |
| **Deployment ownership** | Provider | Provider | Customer | Customer |
| **Internet requirement** | Required | Required | Usually optional | None |
| **External telemetry** | Common | Common | Varies | None |
| **Third-party analytics** | Common | Varies | Varies | None |
| **Content encryption** | Often end-to-end | Often provider-readable | Varies | End-to-end (libsignal, including reactions, encrypted broadcasts and classification labels; per-file AES-256-GCM) |
| **Administrator authentication** | Provider-managed | Single sign-on, often with FIDO2 | Varies | FIDO2 security keys verified by the installation; federation only to the customer's identity provider |
| **Device-list integrity** | Varies; some publish key transparency | Provider directory | Usually server-asserted | Device lists signed by each account's devices, verified by senders against the installation's own log |
| **Private PKI** | No | Limited | Usually | Yes |
| **Air-gap support** | No | No | Varies | Yes |
| **Customer key control** | No | Sometimes (customer-managed keys) | Usually | Content keys on endpoints; server keys in customer OpenBao |
| **Customer-controlled logs** | No | Partial | Yes | Yes, hash-chained, SIEM export |
| **Offline updates** | No | No | Varies | Yes, signed offline packages |
| **Jurisdiction exposure** | Provider's jurisdiction | Provider's jurisdiction | Customer's | Customer's |
| **Organisational policy controls** | Minimal | Extensive | Varies | Device governance, retention, feature and access policy |
| **Crypto-agility** | Provider decides | Provider decides | Varies | Profile-negotiated calls; versioned protocol and content envelope; hybrid post-quantum TLS key exchange offered first; single crypto helpers |
| **High availability** | Provider-managed | Provider-managed | Varies | Multi-host profile with an asynchronous second site, or single host |
| **Certification flexibility** | Provider decides | Provider certifications | Varies | Customer-specific evaluation possible; no certification yet |

---

[Previous: 47. Why Air-Gapped SANKET Is Structurally Different](47-why-air-gapped-sanket-is-structurally-different.md) | [Contents](../README.md) | [Next: 49. SANKET within the Tosh Defence Ecosystem](49-sanket-within-the-tosh-defence-ecosystem.md)

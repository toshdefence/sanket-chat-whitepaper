# SANKET Technical Security & Architecture Whitepaper

**SANKET by Tosh Defence** (संकेत): Sovereign Secure Information Exchange Platform

> *The signal only you can read.*

Public edition, version 2.0, 7 October 2026. Document ID TD-SNK-WP-PUB-001. Licensed under Creative Commons Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0).

SANKET is a sovereign, self-hosted secure communications platform: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, on infrastructure the organisation owns and can run on a closed or air-gapped network. This whitepaper sets out its threat model, cryptography, key management, identity and device controls, metadata handling, deployment and assurance, including what it does not claim.

Website: www.sanket.chat. Security reports: security@toshdefence.com (see [SECURITY.md](SECURITY.md)).

## Document Control

| Field | Value |
| --- | --- |
| **Document title** | SANKET by Tosh Defence - Technical Security & Architecture Whitepaper |
| **Edition** | Public edition |
| **Document ID** | TD-SNK-WP-PUB-001 |
| **Version** | 2.0 |
| **Publication date** | 7 October 2026 |
| **Prepared by** | Tosh Defence Private Limited, India |
| **Product** | SANKET - Sovereign Authenticated Network Key-based Encrypted Transmission |
| **Review status** | Released for public distribution. Technical claims reviewed against the platform source code and engineering records of the release described in Chapter 2. |
| **Intended audience** | CISOs, CIOs, security architects, network security teams, technical evaluation committees, security assurance, risk and compliance teams, and procurement teams of organisations evaluating sovereign secure communications |
| **Classification** | PUBLIC |
| **Business contact** | tosh@toshdefence.com |

**PUBLIC EDITION**

This is the public edition of the SANKET Technical Security & Architecture Whitepaper. It describes the security architecture, the cryptography, the threat model and the limits of the platform in full at the level of design. It deliberately omits component versions, detection and abuse thresholds, internal interface and configuration names, and the operating detail of protective features such as duress and remote wipe, because that detail helps an attacker more than an evaluator. A controlled edition containing it is provided to evaluating organisations under a non-disclosure agreement.

**LICENCE**

Copyright 2026 Tosh Defence Private Limited. This edition may be copied and redistributed in any medium or format, unmodified and with attribution to Tosh Defence Private Limited, under the Creative Commons Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0) licence. Product and company names mentioned may be trademarks of their respective owners.

**REPORTING A SECURITY ISSUE**

Researchers who believe they have found a vulnerability in SANKET are asked to report it privately to security@toshdefence.com and to allow time for a fix to reach customer installations before any public disclosure. Reports are acknowledged and handled in confidence.

**REVISION HISTORY**

| Version | Date | Author | Description |
| --- | --- | --- | --- |
| **2.0** | 7 October 2026 | Tosh Defence Private Limited | First public edition. Describes the release with end-to-end encrypted reactions, broadcasts and classification labels, administrator security keys, federated sign-in, signed device lists, device mutual TLS, hardware-backed key storage, calls over TCP 443, hybrid post-quantum transport, release freeze and rollback protection, release safety and the high-availability profile. |

## Legal and Security Disclaimer

This whitepaper describes the security architecture of SANKET as engineered by Tosh Defence Private Limited. It is provided for information and technical evaluation. It is not a warranty, a contractual specification or a certification of fitness for any particular purpose. Contractual commitments are made only in a signed agreement.

- **Architecture varies by deployment.** SANKET is self-hosted. The exact topology, network zones, identity integration, push-notification path and hardening baseline are chosen per deployment. Diagrams in this document are reference architectures, not a description of any particular installation.
- **Cryptographic configuration depends on the deployment baseline and release.** Algorithms, key sizes and protocol versions are stated for the release described in Chapter 2. Customers should confirm the configuration of the release they actually deploy.
- **Certification status must be verified against the current release.** At the date of this document SANKET holds no third-party security certification. Certification targets are identified as targets, not achievements (Chapter 40).
- **Security depends on correct configuration and operational discipline.** In a self-hosted deployment the customer operates the servers, networks, identity processes and endpoints. Weak operating practice can undermine any cryptographic design.
- **Capability status is labelled.** Every capability in this document is described as implemented, configurable, by design, deployment-dependent, planned, roadmap, recommended hardening or certification target. Planned and roadmap capabilities are not operational until delivered in a release.
- **Endpoints remain inside the security boundary.** End-to-end encryption protects data in transit and on the server. It does not protect plaintext on an authorised endpoint that an attacker fully controls (Chapter 29).
- **No absolute claims.** No responsible vendor can guarantee immunity from compromise. This document sets out controls, assumptions and residual risks so that the reader can form an independent judgement.

Product and company names mentioned in this document may be trademarks of their respective owners. Third-party specifications are cited for technical reference only. Their citation does not imply endorsement of SANKET by their authors.

## How to Read This Document

The document is written for technical evaluators. Chapters 1 to 7 set out the problem, the threat model and the design principles. Chapters 8 to 20 describe the architecture, cryptography, identity and lifecycle of information in depth. Chapters 21 to 40 deal with deployment, operations, assurance and limits. The appendices collect reference tables, checklists, a glossary and the standards cited.

Capability status is shown with the following labels wherever precision matters:

| Label | Meaning |
| --- | --- |
| **IMPLEMENTED** | Present in the platform release described in this document and covered by automated tests. |
| **CONFIGURABLE** | Implemented, and turned on, off or tuned by the customer administrator through tenant policy. |
| **DEPLOYMENT-DEPENDENT** | The property depends on how the customer builds and operates the deployment (network, hardware, PKI, processes). |
| **BY DESIGN** | A deliberate architectural choice. The reason is always stated alongside it. |
| **PLANNED** | Engineering work defined and scheduled, but not in the current release. |
| **ROADMAP** | Direction of travel without a committed release. |
| **RECOMMENDED** | Hardening guidance for the customer. Not a product feature. |
| **CERTIFICATION TARGET** | An assurance outcome Tosh Defence intends to pursue. Not achieved. |

Footnotes cite primary sources: IETF RFCs, NIST publications, W3C and FIDO Alliance specifications, Signal's published protocol specifications and peer-reviewed research. The full list is in Appendix J.

## Contents

- [1. Executive Summary](chapters/01-executive-summary.md)
- [2. Release Baseline and Capability Status](chapters/02-release-baseline-and-capability-status.md)
- [3. Who SANKET Is For](chapters/03-who-sanket-is-for.md)
- [4. Sovereignty as a Security Control](chapters/04-sovereignty-as-a-security-control.md)
- [5. Threat Model](chapters/05-threat-model.md)
- [6. Security Architecture Principles](chapters/06-security-architecture-principles.md)
- [7. System Architecture](chapters/07-system-architecture.md)
- [8. Service Architecture](chapters/08-service-architecture.md)
- [9. Data Architecture](chapters/09-data-architecture.md)
- [10. Cryptographic Architecture](chapters/10-cryptographic-architecture.md)
- [11. Key Management Architecture](chapters/11-key-management-architecture.md)
- [12. Forward Secrecy and Post-Compromise Security](chapters/12-forward-secrecy-and-post-compromise-security.md)
- [13. Identity and Authentication](chapters/13-identity-and-authentication.md)
- [14. Device Trust Model](chapters/14-device-trust-model.md)
- [15. Role and Policy Architecture](chapters/15-role-and-policy-architecture.md)
- [16. Zero Trust Architecture](chapters/16-zero-trust-architecture.md)
- [17. Message Security Lifecycle](chapters/17-message-security-lifecycle.md)
- [18. File Security Lifecycle](chapters/18-file-security-lifecycle.md)
- [19. Secure Voice and Video](chapters/19-secure-voice-and-video.md)
- [20. Metadata Security](chapters/20-metadata-security.md)
- [21. Audit Architecture](chapters/21-audit-architecture.md)
- [22. Monitoring, Threat Detection and Observability](chapters/22-monitoring-threat-detection-and-observability.md)
- [23. Air-Gapped Deployment](chapters/23-air-gapped-deployment.md)
- [24. Offline-First Operation](chapters/24-offline-first-operation.md)
- [25. Network Architecture and Segmentation](chapters/25-network-architecture-and-segmentation.md)
- [26. PKI Architecture](chapters/26-pki-architecture.md)
- [27. Administrative Security](chapters/27-administrative-security.md)
- [28. Security Impact of Server Compromise](chapters/28-security-impact-of-server-compromise.md)
- [29. Endpoint Compromise Analysis](chapters/29-endpoint-compromise-analysis.md)
- [30. Software Supply Chain and Secure Updates](chapters/30-software-supply-chain-and-secure-updates.md)
- [31. Offline Licensing](chapters/31-offline-licensing.md)
- [32. Availability, High Availability and Disaster Recovery](chapters/32-availability-high-availability-and-disaster-recovery.md)
- [33. Hardware Security](chapters/33-hardware-security.md)
- [34. Security Control Matrix](chapters/34-security-control-matrix.md)
- [35. Defensive Attack Scenarios](chapters/35-defensive-attack-scenarios.md)
- [36. Deployment Mode Comparison](chapters/36-deployment-mode-comparison.md)
- [37. Feature Catalogue](chapters/37-feature-catalogue.md)
- [38. Information Classification Support](chapters/38-information-classification-support.md)
- [39. Crypto-Agility and Post-Quantum Readiness](chapters/39-crypto-agility-and-post-quantum-readiness.md)
- [40. Cryptographic Certification Roadmap](chapters/40-cryptographic-certification-roadmap.md)
- [41. Compliance, Assurance and Security Testing](chapters/41-compliance-assurance-and-security-testing.md)
- [42. Secure Deployment Baseline](chapters/42-secure-deployment-baseline.md)
- [43. External Dependency Analysis](chapters/43-external-dependency-analysis.md)
- [44. Privacy Architecture](chapters/44-privacy-architecture.md)
- [45. Security and Usability Trade-offs](chapters/45-security-and-usability-trade-offs.md)
- [46. What SANKET Does Not Claim](chapters/46-what-sanket-does-not-claim.md)
- [47. Why Air-Gapped SANKET Is Structurally Different](chapters/47-why-air-gapped-sanket-is-structurally-different.md)
- [48. Comparative Architecture](chapters/48-comparative-architecture.md)
- [49. SANKET within the Tosh Defence Ecosystem](chapters/49-sanket-within-the-tosh-defence-ecosystem.md)
- [50. Technical FAQ](chapters/50-technical-faq.md)
- [Appendix A - Cryptographic Algorithm Reference](chapters/appendix-a-cryptographic-algorithm-reference.md)
- [Appendix B - Key Hierarchy](chapters/appendix-b-key-hierarchy.md)
- [Appendix C - Security Control Matrix](chapters/appendix-c-security-control-matrix.md)
- [Appendix D - Threat Model Summary](chapters/appendix-d-threat-model-summary.md)
- [Appendix E - Deployment Reference Architectures](chapters/appendix-e-deployment-reference-architectures.md)
- [Appendix F - Air-Gap Deployment Checklist](chapters/appendix-f-air-gap-deployment-checklist.md)
- [Appendix G - Security Hardening Checklist](chapters/appendix-g-security-hardening-checklist.md)
- [Appendix H - Network and Protocol Categories](chapters/appendix-h-network-and-protocol-categories.md)
- [Appendix I - Glossary](chapters/appendix-i-glossary.md)
- [Appendix J - Standards and References](chapters/appendix-j-standards-and-references.md)

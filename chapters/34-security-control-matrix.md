<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 34. Security Control Matrix

The matrix maps each security objective to the threats it addresses, the controls that implement it, where those controls operate, the relevant standards, and the residual risk.[^67]

*Table 58: Security control matrix*

| Security objective | Threat | Control | Security layer | Relevant standard | Residual risk |
| --- | --- | --- | --- | --- | --- |
| **Confidentiality (content)** | Server, network or storage compromise | libsignal E2EE; per-file AES-256-GCM; call frame encryption | Endpoint | Signal PQXDH, Double Ratchet; NIST SP 800-38D | Endpoint compromise |
| **Confidentiality (transport)** | Network eavesdropping | TLS 1.3 / 1.2 AES-256-GCM only; hybrid post-quantum key exchange offered first; HSTS; certificate pinning on desktop and mobile; device mutual TLS | Network | RFC 8446, RFC 9325 | Device trust store integrity |
| **Integrity** | Tampering with messages or files | libsignal MACs; GCM tags; TLS | Endpoint, network | RFC 2104, SP 800-38D | Server-controlled ordering and dropping |
| **Authenticity** | Impersonation of users or devices | Identity keys pinned on first use; signed prekeys; signed device links; device-bound tokens; device client certificates | Endpoint, application | XEdDSA; RFC 7519 | Social engineering of device approvers |
| **Device-list integrity** | A compromised or hostile server adding a device to an account to receive copies | Device lists signed with each account's list key on its own devices; append-only log refusing forks; senders verify the signed list before encrypting on every fan-out path; identity-change notices replayed to offline devices | Endpoint | Key transparency (self-hosted) | Trust on first use for a new identity, verified by safety-number comparison |
| **Availability** | Denial of service | Rate limits, adaptive limits, caps, block manager, health checks | Edge, application | Customer DDoS controls | Volumetric attacks beyond the customer's DDoS controls |
| **Accountability** | Undetected misuse | Hash-chained audit, signed with a non-exportable key; SIEM export with the anchors | Application | NIST SP 800-92 | Mitigated by independent SIEM copy |
| **Non-repudiation** | Denial of administrative actions | Audit records bound to authenticated actors | Application | - | Not cryptographic non-repudiation; messages are deniable by protocol design |
| **Forward secrecy** | Later key compromise | Double Ratchet; ECDHE TLS; call key ratchet | Endpoint, network | Signal specifications | Stored local plaintext |
| **Post-compromise recovery** | Ongoing use of stolen state | DH ratchet; session reset; call re-keying | Endpoint | Signal specifications | Persistent endpoint compromise |
| **Access control** | Unauthorised access | RBAC; group roles; membership checks; policy; licence gates | Application | NIST SP 800-53 AC family | Misconfiguration by administrators |
| **Sovereignty** | Foreign jurisdiction and dependency | Self-hosting; no third-party SDKs; offline licence and updates | Deployment | - | Platform push for iOS in connected mode |
| **Device trust** | Rogue or lost devices | Device states, caps, approval, lost mode, wipe; hardware-backed keys with a recorded protection level; offline Android Key Attestation | Application, endpoint | OWASP MASVS | Posture on unmanaged devices |
| **Auditability** | Tampering with records | Hash chain with verifier; periodic Ed25519 anchors from the key service; exported hash links and anchors | Application | NIST SP 800-92 | Collusion across separated operator duties; mitigated by independent SIEM copy |
| **Resilience** | Host failure, data loss | Encrypted backups; restore tooling; health checks; multi-host high-availability profile with an asynchronous second site and drill tooling | Operations | NIST SP 800-34 | Writes since the last replicated point on a site loss (design target RPO 5 minutes); recovery time of a single-host profile |
| **Supply-chain integrity** | Malicious or tampered release | Signed manifest with sequence number and expiry; signed timestamp on connected upgrades; hashes; pinned sources; offline verification | Build, deployment | NIST SP 800-218; SLSA (intent) | Compromise upstream of the build |
| **Administrative control** | Abuse of administrator power | FIDO2 security keys for administrators; federation only to the customer's identity provider; least privilege; audit; no content keys | Application | NIST SP 800-53 AC-5, AC-6 | Host-level administrators (separation of duties) |

---

[^67]: NIST, "Security and Privacy Controls for Information Systems and Organizations", NIST SP 800-53 Rev. 5, September 2020.

---

[Previous: 33. Hardware Security](33-hardware-security.md) | [Contents](../README.md) | [Next: 35. Defensive Attack Scenarios](35-defensive-attack-scenarios.md)

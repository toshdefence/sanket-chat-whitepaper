<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 41. Compliance, Assurance and Security Testing

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Assurance comes from evidence that others can check. This chapter sets out the current status of formal assurance, the testing SANKET performs, and the independent assessment Tosh Defence recommends customers commission.

## 41.1 Assurance Status

*Table 64: Assurance and certification status*

| Control / certification | Current status | Relevance | Target |
| --- | --- | --- | --- |
| **ISO/IEC 27001 information security management** | Not certified[^76] | Organisational assurance for the vendor | **CERTIFICATION TARGET** |
| **Secure software development practice** | Internal practices (Chapter 30); not externally assessed | Supply-chain assurance | Alignment with NIST SSDF |
| **CERT-In empanelled security audit** | Planned | Required by many Indian government buyers | **CERTIFICATION TARGET** |
| **STQC / national product evaluation** | Planned | Indian government product assurance | **CERTIFICATION TARGET** |
| **Independent vulnerability assessment and penetration test** | Planned | Baseline assurance for any deployment | Recommended before production |
| **Independent cryptographic review** | Planned for SANKET's integration; libsignal and the Signal Protocol have published analyses | Assurance of correct use of primitives | **CERTIFICATION TARGET** |
| **Source code review** | Internal reviews performed; available to customers under agreement | Independent verification | Customer or third-party review |
| **SBOM** | Package-level CycloneDX SBOM per release image, signed with the release manifest and shipped in the offline package | Vulnerability management | **IMPLEMENTED** |
| **Configuration hardening** | Baseline in Chapter 42; installation gates | Deployment assurance | Customer acceptance |
| **Customer accreditation** | Customer-specific | Authority to operate | Per customer |

## 41.2 Internal Security Reviews

SANKET has undergone several rounds of structured internal security review of the backend, mobile, desktop and administration code, with findings triaged by severity and remediated. Internal review complements, and does not replace, the independent assessment described below.

## 41.3 Testing Performed

*Table 65: Security-relevant testing in the engineering process*

| Activity | Current practice |
| --- | --- |
| **Unit and route tests** | Automated tests for authentication, device lifecycle, key directory, messaging, files, calls, integrations and policy enforcement |
| **Local end-to-end suite** | Checks against a running installation covering integrations, audit export and device-loss controls |
| **Release freeze and rollback tests** | Freeze and rollback attempts refused across the offline, connected and pre-flight upgrade paths |
| **Upgrade compatibility** | A contract test replays the previous release's client calls against the new server; automatic rollback is tested against upgrade failures |
| **Media-server proof tests** | Real DTLS-SRTP handshakes in both roles proving that only AEAD_AES_256_GCM is accepted; built into the image build |
| **TLS configuration probes** | Handshake probes confirming that only AES-256-GCM suites are accepted at the edge, on TURN over TLS and on outbound connections, and that the hybrid post-quantum group is offered first |
| **Call-key interoperability** | Equality of the client HKDF ratchet with the media frame-cryptor ratchet over many steps |
| **Cryptographic policy checks** | Pre-commit and lint checks for AES-256-only construction and the pinned patched WebRTC artefact |
| **Container scanning** | Image vulnerability scanning and container benchmark workflows |
| **Device test matrices** | Defined test matrices for calls (including calls over TCP 443 only on iOS, Android and desktop), network conditions, MDM and device-loss controls |

## 41.4 Recommended Testing Programme

- **Threat modelling** of the customer's specific deployment, building on Chapter 5.
- **Code review** and **SAST** of the release, and **software composition analysis** of its dependencies.
- **DAST and API testing** of the edge and APIs, including authorisation tests across roles and memberships.
- **Mobile application security testing** against OWASP MASVS on both platforms.[^77]
- **Container and infrastructure assessment** against a container benchmark and NIST SP 800-190.[^78][^79]
- **Penetration and red-team testing**, including insider and lost-device scenarios.
- **Configuration review** against the hardening baseline.
- **Restore testing and disaster-recovery exercises**, including a second-site failover drill that records the measured recovery point and recovery time.

## 41.5 Cryptographic Testing

*Table 66: Cryptographic test areas*

| Area | What to test |
| --- | --- |
| **Test vectors and known-answer tests** | AES-256-GCM, HKDF, HMAC and Ed25519 helpers against published vectors; libsignal's own test suite |
| **Interoperability** | Sessions between iOS, Android and desktop in all combinations; call key ratchet equality |
| **Nonce uniqueness** | Fresh random IV per encryption; one key per file |
| **Randomness** | Platform CSPRNG installed before any cryptographic library loads on mobile |
| **Key lifecycle** | Prekey rotation, replenishment, consumption; identity rotation; wipe and sign-out key deletion |
| **Corrupted ciphertext** | Modified messages, files and frames rejected |
| **Replay** | Duplicate messages, reused refresh tokens, reused TOTP challenge tokens, replayed call keys |
| **Revoked keys and devices** | Revoked devices excluded from fan-out and refused by the server; revoked device certificates refused at the edge |
| **Device-list integrity** | A device absent from the signed device list receives no copy on any fan-out path; a list that does not extend the previous one is refused |
| **Security keys** | Wrong origin, missing user verification, synced passkeys and sign-count regression refused |
| **Expired certificates** | Clients refuse expired or mismatched edge certificates; certificate pins enforced |
| **Group membership changes** | Removed members receive no new copies; call re-keying on join and leave |
| **Downgrade** | Server refuses lower call profiles; edge refuses weaker TLS suites; older or expired release manifests refused |

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[^76]: ISO/IEC 27001:2022, "Information security, cybersecurity and privacy protection - Information security management systems - Requirements".
[^77]: OWASP Foundation, "Mobile Application Security Verification Standard (MASVS)" v2, mas.owasp.org.
[^78]: M. Souppaya, J. Morello, K. Scarfone, "Application Container Security Guide", NIST SP 800-190, September 2017.
[^79]: Center for Internet Security, "CIS Docker Benchmark", cisecurity.org.

---

[Previous: 40. Cryptographic Certification Roadmap](40-cryptographic-certification-roadmap.md) | [Contents](../README.md) | [Next: 42. Secure Deployment Baseline](42-secure-deployment-baseline.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 43. External Dependency Analysis

The table lists every category of external service a communications platform commonly depends on, and how SANKET handles it in connected and air-gapped deployments.

*Table 68: External dependency analysis*

| Dependency | Connected deployment | Air-gapped deployment | Customer-controlled alternative |
| --- | --- | --- | --- |
| **Content delivery network** | None. Applications and assets are served by the installation or bundled | None | - |
| **Analytics** | None third-party. First-party, content-free, pseudonymous rollups computed inside the installation, on by default | Same | Switch analytics off in the console, or set the installation-level lock at provisioning to force it off |
| **Crash reporting** | None | None | - |
| **Fonts** | Bundled with the applications | Same | - |
| **Push notifications (Android)** | Self-hosted ntfy in the installation; the release app carries no Firebase messaging library | Same | - |
| **Push notifications (iOS)** | Apple push service (generic payloads), if enabled | Not available; no background wake-up | None available from Apple; foreground delivery |
| **Email** | In-installation relay; direct delivery or customer relay; off by default | Internal mail relay or disabled | Customer mail system |
| **SMS** | Off by default; customer-selected provider (Indian or international) for verification only | Disabled | Customer SMS gateway through a supported provider, or none |
| **Certificate authority** | ACME DNS-01 or customer CA | Customer internal CA | Customer PKI |
| **DNS** | Public or private DNS for installation host names | Internal DNS | Customer DNS |
| **NTP** | Customer time source | Internal time source | Customer NTP |
| **STUN (media server)** | None: the node address is set explicitly at installation and STUN discovery is off | Not used | Installer and security gate refuse a missing node address or STUN discovery |
| **Object storage** | MinIO in the installation | Same | - |
| **Package repositories** | Offline package or optional connected download from Tosh Defence | Offline package only | Customer staging repository |
| **Third-party base images** | Pulled by the operator from the vendor registry or public registries | Pre-staged by the customer | Customer internal registry |
| **Monitoring** | Built-in monitors; optional customer metrics and tracing collectors | Same | Customer monitoring |
| **Logging** | Installation logs; syslog to customer SIEM | Internal SIEM | Customer SIEM |
| **Licensing** | Local verification; optional signed status report to Tosh Defence | Local verification; offline renewal; no status report | - |
| **Identity provider** | Optional: the customer's own OIDC, SAML or LDAP directory; never a foreign provider (public identity providers are refused). Optional SCIM from the customer directory | Same, inside the enclave | Customer directory |
| **Security-key verification (WebAuthn)** | Verified inside the installation on platform cryptography, with no external attestation or metadata service | Same | - |
| **Device attestation (Android)** | Verified offline against vendor roots and a revocation snapshot shipped in each release; no runtime call to Google | Same | - |
| **Key management** | OpenBao in the installation | Same | Customer HSM where policy requires (evaluation) |
| **Backup destinations** | Operator-chosen: local or network-attached storage, S3-compatible object storage or SFTP, all customer-operated; there is no foreign cloud adapter | Offline media | Customer storage |

## 43.1 Security-Relevant Libraries and Runtimes

SANKET's rule is that every cryptographic library has at least one published independent security audit. Any exception is reviewed, approved and recorded, with how its exposure is limited, and is detailed for evaluators in the controlled edition.

*Table 69: Security-relevant libraries and runtimes*

| Component | Role | Audit position and limits |
| --- | --- | --- |
| **libsignal (official)** | Signal Protocol for messages, call keys, broadcasts and device-list signatures, through the official Swift, Java and TypeScript bindings (current audited release) | Signal's official implementation; the only end-to-end protocol code in SANKET (Chapter 10) |
| **OpenSSL in the edge** | TLS termination in the edge, offering hybrid X25519MLKEM768 key exchange | A widely deployed TLS library; the hybrid group combines ML-KEM with X25519, so a weakness in either alone does not break the key exchange[^80] |
| **Server runtime** | Node.js on a supported LTS line for the API, workers and the vendor super-administration API, digest-pinned; outbound TLS offers the hybrid group too | Platform cryptography used through the runtime's bundled OpenSSL |
| **Desktop runtime** | Electron on a supported release line | Kept on a line that receives security updates; all cryptography runs in the main process |
| **Android release build** | Push through the self-hosted relay | Carries no Firebase messaging library |

> [!IMPORTANT]
> **Key Point - Architectural statement**
>
> SANKET is designed with no mandatory foreign runtime dependency for sovereign deployment. An air-gapped installation operates with no external service at all. A connected installation can be configured with none, at the cost of iOS background wake-ups. Build-time tooling used by Tosh Defence (source hosting, public package and image registries, mobile build services) is a supply-chain consideration addressed by the signed release process in Chapter 30, not a runtime dependency of the customer's installation.

---

[^80]: NIST, "Module-Lattice-Based Key-Encapsulation Mechanism Standard" (ML-KEM), FIPS 203, August 2024.

---

[Previous: 42. Secure Deployment Baseline](42-secure-deployment-baseline.md) | [Contents](../README.md) | [Next: 44. Privacy Architecture](44-privacy-architecture.md)

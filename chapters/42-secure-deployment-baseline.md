<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 42. Secure Deployment Baseline

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

A self-hosted platform is only as strong as the host it runs on. The baseline below separates what the SANKET installation provides from what the customer should apply.

## 42.1 Provided by the Platform

- One public TCP port (443, carrying HTTPS and TURN over TLS) plus the media UDP port; ICE over TCP optional; UDP TURN closed by default; API bound to loopback; data services without host ports.
- Edge on a digest-pinned nginx image whose OpenSSL supports ML-KEM, offering hybrid X25519MLKEM768 key exchange first, then X25519 and secp384r1, with AES-256-GCM suites only. The edge logs only the negotiated key-exchange group per connection, with no client address, URI or user, so the share of post-quantum connections can be measured.
- TURN terminated by the edge with TLS 1.3 and an AES-256-GCM suite only; the media server holds no certificate key, and a patch restricts its TURN relay to the node's own media address, so it cannot be used as an open relay.
- Device mutual TLS enforced per tenant setting; the installation gate refuses an edge configured for it without the customer CA and the device-certificate revocation list in place.
- No container-runtime socket in the API in the standard installation.
- Secrets service on an internal-only network with a least-privilege runtime token.
- Hardening overlay for the Edge profile: no-new-privileges on every service, per-service CPU and memory limits, log rotation and removal of the API host port.
- Every base and third-party image pinned by version and content digest, in every container build and every deployed service default, so a re-pushed or altered upstream tag cannot change what runs; a release check refuses any reference pinned by tag alone, and the installation check warns about an image override that is not pinned.
- Every SANKET-built container runs as a fixed non-root user; any component that must start with higher privilege drops it at start-up and is documented.
- In the Edge hardening overlay SANKET-built containers drop all Linux capabilities and run on a read-only root filesystem, with small in-memory scratch areas only for the paths the service writes; any documented exception keeps only the minimum the component needs.
- Generated secrets at installation; rejection of weak placeholder secrets in production.
- Installation gates that check, among other things, that every SANKET container runs non-root with no capabilities and a read-only root filesystem, that no placeholder secrets remain, that image references match the manifest and that the release verification key is present.
- Upgrade execution from the console off by default.

## 42.2 Operator Hardening Points

The single-host profile favours compatibility with the widest range of customer environments. Further hardening, such as hardened variants of third-party infrastructure images, seccomp or AppArmor profiles and the customer's own container benchmark, is applied by the operator to its own standard; inter-service traffic stays on isolated container networks inside the host, and an air-gapped or mirrored deployment stages the pinned third-party images in its internal registry.

## 42.3 Customer Hardening Baseline

*Table 67: Recommended host and deployment hardening*

| Area | Recommendation |
| --- | --- |
| **Operating system** | Minimal, supported server OS; automatic security updates through an internal mirror |
| **Boot integrity** | UEFI Secure Boot; TPM 2.0 |
| **Disk encryption** | Full-disk encryption of all installation volumes, keyed to TPM with a recovery key under dual custody |
| **Mandatory access control** | SELinux or AppArmor in enforcing mode |
| **Host firewall** | Allow only TCP 443 and the media UDP port inbound (and the ICE-over-TCP port if enabled); deny all outbound except explicitly approved destinations (SIEM, webhooks, the customer's identity provider, optional Apple push) |
| **Administrator sign-in** | Keep the security-key requirement for every administrator; register more than one key per administrator; review administrator sign-in audit events |
| **Device mutual TLS** | Require device mutual TLS for every device |
| **Usage analytics** | For high-sensitivity customers, set the installation-level analytics lock at provisioning: analytics is then forced off, clients collect nothing, ingestion is refused and any queued events are cleared. Other organisations can switch analytics off in the console (a step-up confirmation and a high-severity audit event) and delete collected analytics |
| **Inter-host links (high availability)** | Carry replication between hosts and sites on a dedicated network segment |
| **Administration** | Key-based SSH from a bastion only; MFA on the bastion; no password authentication; separate administrator identities |
| **Management interface** | Isolated management network; administrator console reachable only from it |
| **Containers** | Mirror the pinned image digests to the internal registry and keep any image override pinned by digest; prefer non-root variants of third-party images |
| **Secrets** | Restrict the environment file to the service account; keep OpenBao unseal shares and backup passphrase with separate custodians |
| **Egress** | Set the media server node address or an internal STUN server; leave Apple push disabled where iOS wake-ups are not required |
| **Host auditing** | auditd or equivalent; forward host and container logs to the internal SIEM |
| **Time** | Internal authoritative time source |
| **Backups** | Scheduled encrypted backups to separate media; periodic restore tests |
| **Immutable infrastructure** | Rebuild hosts from known-good images rather than patching in place where practical |
| **Monitoring** | Certificate expiry, disk, memory and service health alerts |

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 41. Compliance, Assurance and Security Testing](41-compliance-assurance-and-security-testing.md) | [Contents](../README.md) | [Next: 43. External Dependency Analysis](43-external-dependency-analysis.md)

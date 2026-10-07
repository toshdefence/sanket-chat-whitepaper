<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 31. Offline Licensing

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

A sovereign platform must not stop working because a vendor server is unreachable. SANKET licences are signed files verified entirely inside the installation.

## 31.1 Licence Format

A licence is a canonical, sorted-key JSON document and an Ed25519 signature, each base64url-encoded.[^65] It is signed by Tosh Defence with a non-exportable key and verified by the installation against a trust bundle that lists active, previous and revoked licence keys and is itself signed by a root key. Licence and trust-bundle signatures are Ed25519; hybrid Ed25519 and ML-DSA signatures are on the roadmap, held until an ML-DSA implementation has a published independent audit or an issued CMVP certificate (Chapter 39).

*Table 53: What a licence binds*

| Field group | Contents |
| --- | --- |
| **Identity** | Licence identifier, signing key identifier, environment, tenant identifier, licensee organisation and country |
| **Term** | Plan, issue and expiry dates, update-subscription end, grace period, offline renewal counter |
| **Limits** | Users, administrators, storage, concurrent calls, group-call size, groups, audit retention, rate limits |
| **Features** | Entitlement flags such as voice, video, group calls, file sharing, encrypted vault, broadcast, integration API, audit export, remote wipe and administrator security keys |
| **Clients** | An optional minimum app version, applied as a floor on every platform |
| **Security** | Permitted deployment type (trial, standard, air-gapped) and an optional list of permitted image digests |
| **Communication** | Connected or offline update mode and the status-report destination |

## 31.2 Validation

- Signature verified against the trust bundle; a bundle cannot supply its own trust anchor.
- Environment must match: a production installation rejects non-production licences.
- Tenant identifier must match the installation.
- If a permitted-image list is present, the running image digest must be on it.
- A licence may state a minimum app version. It is a floor on every platform (iOS, Android and desktop): the server raises the minimum it sends each app to at least that version, and no tenant setting can lower it.
- A production installation will not start without a valid licence.
- Renewals for disconnected sites are delivered as signed offline bundles, bound to the tenant, with a counter that must increase.
- Clock rollback is detected and enforced against.

## 31.3 Expiry Behaviour

*Table 54: Licence expiry stages*

| Stage | Behaviour |
| --- | --- |
| **Valid** | Full operation |
| **Grace period** | Operation continues; new registrations blocked |
| **Read-only (30 days after grace)** | Existing data accessible; no new messages or calls |
| **Hard lock** | Service refuses use until renewed |

## 31.4 Design Notes

- Licences bind to the tenant identity and, optionally, to permitted image digests rather than to hardware, so that installations can be migrated, restored or rebuilt on new hardware without a licence reissue.
- Deployment attestation evidence (Secure Boot state and measured-boot values) is reported for operational visibility.
- Independent review of the licensing and release chain is a certification target (Chapter 40).

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[^65]: S. Josefsson, I. Liusvaara, "Edwards-Curve Digital Signature Algorithm (EdDSA)", IETF RFC 8032, January 2017.

---

[Previous: 30. Software Supply Chain and Secure Updates](30-software-supply-chain-and-secure-updates.md) | [Contents](../README.md) | [Next: 32. Availability, High Availability and Disaster Recovery](32-availability-high-availability-and-disaster-recovery.md)

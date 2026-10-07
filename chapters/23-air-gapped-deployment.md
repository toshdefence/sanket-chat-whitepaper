<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 23. Air-Gapped Deployment

An air-gapped SANKET installation runs on a network with no path to the public internet. Everything it needs at run time is inside the enclave, and everything it needs from outside arrives on controlled media.

## 23.1 Definition

For the purposes of this document, a true air-gapped deployment is one in which the installation, its clients and its supporting services operate with no runtime access to any of: public DNS, cloud APIs, SaaS identity providers, public push services, external certificate authorities, content delivery networks, external analytics or telemetry, internet time servers, public package repositories, cloud key management, public object storage or external licence validation. SANKET supports this mode; the remaining dependencies are a function of the customer's client devices, as set out below.

![Air-gapped deployment architecture](images/airgap.png)

*Figure 13: Air-gapped deployment architecture*

## 23.2 Replacing External Dependencies

*Table 43: Conventional external dependencies and their air-gapped alternatives*

| Conventional external dependency | SANKET air-gapped alternative | Status |
| --- | --- | --- |
| **Public certificate authority** | Certificate issued by the customer's internal CA, installed in import mode; internal CA trusted by clients | **IMPLEMENTED** |
| **Cloud object storage** | MinIO in the installation | **IMPLEMENTED** |
| **Cloud database and cache** | PostgreSQL and Redis in the installation | **IMPLEMENTED** |
| **Cloud key management** | OpenBao in the installation (non-exportable keys) | **IMPLEMENTED** |
| **Public push services (Android)** | Self-hosted ntfy (UnifiedPush) with sealed payloads | **IMPLEMENTED** |
| **Public push services (iOS)** | No equivalent: iOS background wake-up requires Apple's service. Messages are delivered when the app is in the foreground or reconnects | **DEPLOYMENT-DEPENDENT** |
| **Hosted media service and STUN** | Self-hosted SFU with an explicit node address; public STUN is never used; TURN is off, because every client is inside the enclave | **IMPLEMENTED** |
| **Device attestation service (Android)** | Android Key Attestation verified offline by the installation against vendor root certificates and a revocation snapshot shipped in each release; no runtime call to Google | **IMPLEMENTED** |
| **Hosted identity provider** | None required. Optional OIDC or SAML for administrators and LDAP for end-user enrolment only against the customer's own identity provider inside the enclave; public identity providers are refused | **OPTIONAL** |
| **Online licence validation** | Ed25519-signed licence verified locally; offline renewal bundles | **IMPLEMENTED** |
| **Online package registry and updates** | Offline release package with signed manifest, image archives and SBOMs, verified before load; the signed manifest's sequence number and expiry protect against rollback and stale releases without any online check | **IMPLEMENTED** |
| **Third-party base images** | Pre-staged by the customer from a verified source into a local registry or archive | **CUSTOMER RESPONSIBILITY** |
| **Cloud logging and SIEM** | Syslog over TLS to the customer's internal SIEM; local monitors | **IMPLEMENTED** |
| **Internet NTP** | Customer's internal authoritative time source; SANKET detects clock rollback for licensing | **RECOMMENDED** |
| **Public DNS** | Internal DNS for the installation's host names | **DEPLOYMENT-DEPENDENT** |
| **SMS and email providers** | SMS disabled; email via the in-installation relay to internal mail, or disabled | **CONFIGURABLE** |
| **External analytics / crash reporting** | Not used by product applications | **IMPLEMENTED** |
| **Vendor status reporting and connected upgrade** | Offline-mode licence: no status report; upgrades by offline package only | **CONFIGURABLE** |

## 23.3 Client Devices in an Enclave

- **Android** devices connect to the installation over the enclave network and receive wake-ups through the self-hosted push relay. Applications are distributed through the customer's managed channel.
- **Desktop** clients maintain a persistent connection while running and are restricted at run time to the installation's own hosts.
- **iOS** devices can run SANKET in an enclave, but without Apple's push service they are not woken in the background; incoming messages and calls are received when the app is open. Distribution and device enrolment of iOS devices also depend on Apple's infrastructure at provisioning time.

## 23.4 Operational Considerations

- **Time.** Token validity, TOTP and licence checks depend on accurate clocks. Provide an internal authoritative time source to servers and devices.
- **Certificates.** Plan the internal CA hierarchy and certificate renewal before installation (Chapter 26).
- **Releases.** Establish a controlled media transfer procedure with malware scanning and custody records (Chapter 30). A release manifest expires, and installation refuses an expired one. Bring current releases into the enclave before they expire; an installation that is already running never stops because its manifest expires.
- **Device attestation data.** Attestation roots and revocation data arrive with each release; keep releases current.
- **Licences.** Renewals are delivered as signed offline bundles; the platform rejects bundles for another tenant and enforces a monotonic renewal counter.
- **Backups.** Hold encrypted backups on separate media inside the enclave and test restoration (Chapter 32).

> [!TIP]
> **Security Property - Result**
>
> In an air-gapped installation, no SANKET component makes a connection outside the enclave (device attestation, identity providers and release checks all work offline), and no vendor or third party can observe, alter or interrupt the service. The residual dependencies are the customer's own infrastructure and, for iOS devices, the limits of the operating system.

---

[Previous: 22. Monitoring, Threat Detection and Observability](22-monitoring-threat-detection-and-observability.md) | [Contents](../README.md) | [Next: 24. Offline-First Operation](24-offline-first-operation.md)

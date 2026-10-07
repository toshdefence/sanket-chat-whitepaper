<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 4. Sovereignty as a Security Control

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Sovereignty is often treated as a hosting statement: the servers sit in a particular country. In security terms that is the least important part. What matters is who can read, change, switch off or observe the system, and under whose legal authority they can be compelled to do so.

A communications platform has many points of control besides the servers: the people who administer it, the keys that protect it, the identity system that admits users, the logs that record its use, the software updates that change its behaviour, and the third-party services it calls while running. An organisation is sovereign over its communications only when each of these points of control sits inside its own security boundary, or can be moved there.

SANKET treats each of these points of control as a separate design requirement. The sections below define nine dimensions of sovereignty, explain the threat each one addresses, and state how SANKET meets it.

## 4.1 Jurisdictional Sovereignty

**Threat addressed:** lawful or extra-legal compulsion of a foreign service provider to disclose data, insert access, or suspend service. Cloud and SaaS providers are subject to the laws of the countries in which they are incorporated and operate, which can reach data held anywhere in the world.

**How SANKET addresses it:** SANKET is installed and operated by the customer on infrastructure the customer chooses. Tosh Defence does not operate the customer's communications service, does not hold its data, and has no standing access to it. There is therefore no provider in another jurisdiction that can be served with a demand for the customer's content, metadata or logs.

## 4.2 Data Sovereignty

**Threat addressed:** data residing on, or transiting, infrastructure the customer does not control.

**How SANKET addresses it:** the relational database, cache, object storage, secrets store, media server and push relay all run inside the customer's installation. Message content and file content are end-to-end encrypted before they leave the sending device, so even the customer's own servers hold ciphertext. Metadata that the server must process (for example routing information and audit records) stays on the customer's servers. Chapter 20 lists exactly what that metadata is.

## 4.3 Cryptographic Sovereignty

**Threat addressed:** a third party holding, generating or escrowing keys; dependence on a provider's key management service; inability to inspect or replace the cryptography.

**How SANKET addresses it:** end-user identity and session keys are generated on the user's device and never leave it in usable form. Server-side signing and wrapping keys are generated inside the customer's own OpenBao secrets service and are marked non-exportable. TLS certificates can be issued by the customer's own certificate authority. No key is held by Tosh Defence. The message protocol is the published, open-source Signal Protocol implementation (libsignal), which the customer or its evaluators can inspect. The signed device-list log that lets senders check every account's devices is kept by the installation itself, never by an external key transparency service, and Android hardware attestation is verified offline against vendor roots shipped in the release, with no runtime call to the platform vendor.

## 4.4 Infrastructure Sovereignty

**Threat addressed:** reliance on infrastructure that can be withdrawn, throttled, inspected or modified by an outside party.

**How SANKET addresses it:** a SANKET installation is a set of containers that run on the customer's servers with Docker Compose. It needs no cloud provider, managed database, managed queue, content delivery network or hosted media service. The video and voice media server is a self-hosted build of the open-source LiveKit server, built and pinned by Tosh Defence and run by the customer.

## 4.5 Identity Sovereignty

**Threat addressed:** an external identity provider deciding who may sign in, or being compromised so that an attacker can mint identities.

**How SANKET addresses it:** the customer decides how identities come into existence. Administrators can create accounts directly, the customer's own provisioning system can create them through the SCIM 2.0 interface, or users can register with a single-use invitation issued by the organisation. Registration mode is a tenant policy and the shipped default is invitation-only. Administrator sign-in can be federated to the customer's own OIDC or SAML 2.0 identity provider, and enrolment can be checked against its own LDAP directory; public identity providers are refused and the local second factor is still required. Administrator security keys are verified by the installation itself with no external service. There is no dependency on a public identity provider, and identities exist only inside the customer's installation. Each user is identified by a Sanket ID.

## 4.6 Operational Sovereignty

**Threat addressed:** the vendor or a provider being able to stop, degrade or reconfigure the service; dependence on the vendor's availability.

**How SANKET addresses it:** the customer operates every component. Licence validation is performed locally against a signed licence file (Chapter 31), so the service does not stop because a vendor licensing server is unreachable. Configuration, retention, security policy and feature enablement are set by the customer's administrators.

## 4.7 Update Sovereignty

**Threat addressed:** a vendor pushing a change into production without the customer's approval, whether through error, coercion or compromise of the vendor.

**How SANKET addresses it:** server releases are pinned container image versions plus a configuration bundle that the customer installs. A release changes the installation only when the customer's operator runs the upgrade. Each release manifest is signed and carries a sequence number and an expiry, so an old or replayed release is refused, and connected installations also check a signed statement of the newest release, so they cannot be held on an old one without noticing. Air-gapped installations receive releases as offline bundles that the customer can inspect, scan and stage before deployment (Chapter 30).

## 4.8 Audit Sovereignty

**Threat addressed:** audit records held by a third party, or invisible to the customer; inability to prove what administrators did.

**How SANKET addresses it:** the audit log is stored in the customer's database, hash-chained so that alteration or deletion can be detected, and exported only to destinations the customer configures, such as its own SIEM over syslog with TLS. No audit or telemetry data is sent to Tosh Defence.

## 4.9 Administrative Sovereignty

**Threat addressed:** vendor staff with privileged access to customer systems; administrators with more power than their role needs.

**How SANKET addresses it:** the tenant administration console is part of the customer's installation and is used by the customer's staff. Tosh Defence's own operator console manages licences and releases. It is a separate system with no access path to the customer's message store. Within the customer's installation, administrative power is divided into permission-scoped roles (Chapter 15) and every administrative action is audited.

## 4.10 How the Security Posture Changes

The effect of these properties is easiest to see by comparing the questions an evaluator must ask about a conventional cloud-hosted communications service with the questions that remain for a sovereign SANKET deployment.

*Table 6: Sovereignty dimensions compared*

| Sovereignty dimension | Conventional cloud architecture | SANKET sovereign deployment |
| --- | --- | --- |
| **Jurisdiction** | Provider is subject to the laws of its home and operating countries; data can be compelled from it | No provider holds the customer's data; compulsion would have to be served on the customer itself |
| **Data location** | Provider-selected regions; replicas and backups may sit elsewhere | Customer-selected servers; backups are made by the customer's operator |
| **Keys** | Often held in a provider key management service | End-user keys on devices; server keys in the customer's OpenBao, non-exportable |
| **Infrastructure** | Shared multi-tenant provider infrastructure | Dedicated installation on customer hardware or a customer-chosen sovereign data centre |
| **Identity** | Provider or federated public identity service | Customer-controlled identities: administrator-created, invitation-based or SCIM-provisioned from the customer's own directory; optional federation only to the customer's own identity provider |
| **Operations** | Provider controls availability and configuration | Customer operates every component; licence checked locally |
| **Updates** | Provider deploys changes continuously | Customer decides when a pinned release is installed |
| **Audit and logs** | Provider-held logs, partial customer visibility | Hash-chained audit log in the customer's database; export only to customer destinations |
| **Administration** | Provider staff hold privileged access | No vendor access; customer administrators in permission-scoped roles |
| **External analytics and telemetry** | Commonly present | None sent outside the installation; first-party usage counts stay on the customer's servers and can be switched off, or locked off for the whole installation |
| **Foreign CDN and hosted fonts** | Commonly present | Not used by product applications; desktop builds are checked for foreign hosts and blocked from them at run time |
| **Internet connectivity** | Required | Not required for the server installation; an air-gapped mode is supported (Chapter 23) |

> [!NOTE]
> **Note - Scope of the sovereignty claims**
>
> These properties describe the SANKET server installation and the SANKET client applications. Two practical boundaries apply.
>
> - Mobile operating systems: Apple iOS devices receive wake-up notifications through Apple's push service when the customer chooses to use it. SANKET sends only generic, content-free payloads on that path, and air-gapped deployments can operate without it (Chapter 23).
>
> - Endpoint and app distribution: smartphones and their operating systems are made by third parties. Customers with the highest assurance requirements should use managed devices and enterprise distribution channels (Chapter 14).

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 3. Who SANKET Is For](03-who-sanket-is-for.md) | [Contents](../README.md) | [Next: 5. Threat Model](05-threat-model.md)

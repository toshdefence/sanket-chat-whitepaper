<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 27. Administrative Security

Administrators of a SANKET installation have significant power over identities, devices and policy, but not over content. This chapter describes how that power is constrained and how customers should divide it.

## 27.1 Central Management Console

Every installation includes one administrator console from which the organisation manages its users, devices, policy and licence. Administration is delegated through roles (Chapter 15): each administrator sees and changes only what their role permits.

*Table 48: Central console functions*

| Area | Functions |
| --- | --- |
| **Users** | Create, edit, suspend, reactivate and delete users; invitations and activation codes; roles; per-user device lists |
| **Devices** | Approve, revoke, mark lost or found, locate, remote wipe per device or per user; managed-device (MDM) status; hardware protection level and client certificate state per device; outdated devices by platform and app version |
| **Licence** | Plan, validity and days remaining, licensed features, user and storage limits; export of an offline renewal request and import of a signed renewal bundle for disconnected sites |
| **Policy** | Security, messaging, files, calls, groups, retention, notifications and integrations settings; classification labels; usage analytics; device client certificates and required protection level |
| **Administration** | Built-in and custom administrator roles under a permission ceiling; administrator security keys; the customer's own identity provider |
| **Broadcasts** | Broadcast-sender accounts (create, assign to named administrators, suspend); encrypted broadcasts shown as metadata and receipts only (audience, priority, deadline, expiry, acknowledgement progress); notice broadcasts composed, targeted, scheduled and sent from the console, labelled "Notice (not end-to-end encrypted)" |
| **Monitoring and audit** | Operational monitors, alerts, block manager, audit log with chain verification and export |
| **Integrations** | API clients, SCIM, syslog, webhooks, MDM templates, OpenAPI documentation |

## 27.2 Platform Controls

*Table 49: Administrative controls in the platform*

| Control | Implementation | Status |
| --- | --- | --- |
| **Dedicated administrator accounts** | Administrator accounts are separate from end-user accounts; the database forbids giving an end user the administrator role | **IMPLEMENTED** |
| **Mandatory MFA** | A second factor is always required for administrators, regardless of tenant policy: a FIDO2 security key, or a TOTP code where security keys are not required | **IMPLEMENTED** |
| **Administrator security keys** | FIDO2 security keys (WebAuthn) verified by the installation itself, with no external service, and **required on new installations for every administrator**. ES256 and EdDSA keys only, user verification required, and synced passkeys refused by default because they copy the key to a vendor cloud; a suspected cloned key is refused. The relying party is the customer's own console address. Recovery from a lost key is a narrow path recorded as a critical audit event | **IMPLEMENTED** |
| **Identity provider** | Optional sign-in through the customer's own identity provider: OpenID Connect or SAML 2.0 for the console, as a first factor only (the local second factor is still required), and an LDAP directory check for end users at enrolment, device restore and new-device sign-in only. The provider must be the customer's own host (public identity providers are refused); accounts are never created from the provider, an unmapped subject is refused and a group mapping can never grant the Owner role. At least one local administrator is always kept, local sign-in is never disabled, and provider secrets are held in the key service (Chapter 13) | **OPTIONAL** |
| **Broadcast-sender accounts** | Dedicated accounts, each with a Sanket ID, that alone can send encrypted broadcasts (Chapter 17): created, assigned to named administrators and suspended in the console, every step audited; excluded from contact search, from all-user audiences and from ordinary chats. Each has a mobile trust root held by the unit's signal office and exactly one sending desktop, approved both by that mobile and in the console. The console never sees an encrypted broadcast's text; it sends only notice broadcasts, labelled as not end-to-end encrypted | **IMPLEMENTED** |
| **Classification labels** | A customer-defined ranked list of up to eight labels and a default label (Chapter 38); every change audited; label names are never sent to unauthenticated callers | **CONFIGURABLE** |
| **Usage analytics settings** | Switch (fresh TOTP step-up, high-severity audit and alert), batching interval, rollup retention and "Delete all collected analytics"; disabled and shown as turned off when the installation lock is set (Chapter 22) | **CONFIGURABLE** |
| **Device client certificates** | Device mutual TLS at the edge, enforced under tenant policy (Chapter 26) | **CONFIGURABLE** |
| **Device protection level** | The hardware protection of each device's keys shown per device, with Android key attestation verified offline; policy can require a minimum level (Chapter 14) | **IMPLEMENTED** |
| **Least privilege** | Nine permission categories by four actions; five immutable built-in roles; custom roles capped at the creator's own permissions | **IMPLEMENTED** |
| **Session protection** | Access token in memory; refresh cookie HttpOnly, Secure, SameSite=Strict, path-scoped; device revocation applies to administrator devices; sessions end after the configured idle period (default 60 minutes) and absolute duration (default 24 hours), enforced by the server | **IMPLEMENTED** |
| **Administrative audit** | Every configuration, user, device, integration and broadcast change is recorded in the hash-chained audit log | **IMPLEMENTED** |
| **Sensitive-action gating** | Wipe and locate each require a specific permission plus policy and licence gates; syslog target changes are critical audit events | **IMPLEMENTED** |
| **Outdated devices** | Per platform, how many active devices run each app version and how many are below the minimum, with a list by Sanket ID and a daily adoption chart kept as counts only; server-side refusal of outdated apps is configurable | **IMPLEMENTED** |
| **Dual control / four-eyes approval** | Achieved through role separation and audit review; in-product approval workflows on the roadmap | **ROADMAP** |
| **Network restriction for administrators** | The API accepts administrator sign-in, every request made with an administrator session and every administrator session refresh only from configured IPv4 and IPv6 address ranges, judged by the address the edge proxy saw; end users are not affected. A save that would exclude the saving administrator is refused. A firewall or reverse proxy in front of the console remains recommended | IMPLEMENTED (address ranges) |
| **No vendor access** | Tosh Defence has no account on, and no remote access path into, the customer's installation | **IMPLEMENTED** |

## 27.3 Lost Security Keys

Recovery from lost security keys follows narrow paths that allow nothing but enrolling a new key, and every use is recorded as a critical audit event. An owner can revoke another administrator's keys, never their own, only by confirming with a fresh assertion from the owner's own security key.

## 27.4 Separation of Duties

A self-hosted installation involves several kinds of privilege, only some of which are inside the SANKET application. Customers should assign them to different people.

*Table 50: Recommended separation-of-duties matrix*

| Duty | Organisation administrator | Security administrator | Auditor | Infrastructure administrator | Database administrator | PKI administrator |
| --- | --- | --- | --- | --- | --- | --- |
| **Create and suspend users** | Yes | - | - | - | - | - |
| **Approve, revoke, wipe devices** | Yes | Yes | - | - | - | - |
| **Change security policy** | Yes | Propose | - | - | - | - |
| **Review alerts and audit** | Read | Yes | Yes | - | - | - |
| **Configure integrations and SIEM** | Yes | Review | Review | - | - | - |
| **Host and container administration** | - | - | - | Yes | - | - |
| **Database access** | - | - | - | - | Break-glass only | - |
| **OpenBao unseal share** | - | One share | One share | One share | - | One share |
| **Certificate issuance** | - | - | - | - | - | Yes |
| **Backup passphrase custody** | - | - | Witness | Yes | - | - |

## 27.5 Recommended Practices

- **Privileged access workstations** for administrator console and host access, on a management network.
- **Break-glass accounts** for the console and the host, with credentials sealed under dual custody and every use reviewed.
- **Recovery credentials** (OpenBao unseal shares, backup passphrase, root CA) held by different custodians.
- **Database separation:** routine operation never requires direct database access; any direct access should be logged and reviewed, because database operators can read server-visible metadata.
- **Host separation:** the host is the root of trust for the installation. Host administrators should not also be SANKET organisation administrators.
- **Review cadence:** verify the audit chain and review administrator activity regularly; forward the audit stream to a SIEM administered by a different team.

> [!TIP]
> **Recommended Practice - Host privilege**
>
> Anyone with root on the installation host can read every server-side secret and all server-visible metadata, and can modify the running software. End-to-end encryption still protects past message content from such a person, but they could alter the server to attack future sessions (Chapter 28). Host access is therefore the most sensitive privilege in a self-hosted deployment.

---

[Previous: 26. PKI Architecture](26-pki-architecture.md) | [Contents](../README.md) | [Next: 28. Security Impact of Server Compromise](28-security-impact-of-server-compromise.md)

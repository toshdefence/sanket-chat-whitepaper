<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 15. Role and Policy Architecture

SANKET separates five things that are often merged: who a person is, what they are allowed to do, which content they can cryptographically read, which devices are trusted, and what administrative power they hold.

*Table 28: Five distinct control concepts*

| Concept | Question it answers | Where enforced in SANKET |
| --- | --- | --- |
| **Authentication** | Is this really the account holder? | Password (Argon2id) and TOTP at sign-in for end users, with an optional directory check at enrolment and restore; password (or the customer identity provider as first factor) and a FIDO2 security key for administrators; device-bound tokens |
| **Authorisation** | May this account perform this action? | API route checks: group roles, administrator permissions, tenant policy, licence features |
| **Cryptographic access** | Can this device decrypt this content? | Only devices included in a libsignal fan-out, and present on the recipient account's signed device list, receive a decryptable copy |
| **Device trust** | Is this device allowed to act for the account? | Device state checked on every request and refresh; device client certificate at the edge; key protection level and attestation where policy requires them |
| **Administrative privilege** | May this administrator change identities, devices or policy? | Permission-scoped administrator roles, a FIDO2 security key and audit |

The separation matters because authorisation and cryptographic access can diverge in useful ways. Removing a member from a group is an authorisation change that immediately removes their devices from future fan-outs; no administrator can, by changing a role, give a device the ability to decrypt messages that were never encrypted to it.

## 15.1 Administrator Roles (RBAC)

Administrator permissions are organised into categories (users, broadcast, vault, audit log, settings, alerts, licence, integrations and location), each with read, create, edit and delete actions. Five built-in roles are provided and cannot be altered; customers can define additional roles, but an administrator cannot create or assign a role with more permission than they hold. Every administrator, whatever the role, is subject to the same strong-authentication policy (Chapter 13). When administrators sign in through the customer's identity provider, roles still come from the console unless the owner enables a group-to-role map, and that map can never grant the Owner role.

*Table 29: Built-in administrator roles*

| Permission category | Owner | User Manager | Config Manager | Threat Monitor | Auditor |
| --- | --- | --- | --- | --- | --- |
| **Users** | All | All | - | - | Read |
| **Broadcast** | All | - | All | - | Read |
| **Vault** | All | - | Read | - | Read |
| **Audit log** | All | Read | - | Read | Read |
| **Settings** | All | - | All | - | Read |
| **Alerts** | All | - | - | Read, edit | Read |
| **Licence** | All | - | - | - | Read |
| **Integrations** | All | - | - | - | - |
| **Location** | All | - | - | - | - |

*Customers map these to their own structure, for example Organisation Administrator (Owner), Security Administrator (Threat Monitor plus a custom role with user-edit permission for lost-device handling), and Auditor.*

## 15.2 Group Roles

*Table 30: Group roles and permissions*

| Action | Owner | Group administrator | Member |
| --- | --- | --- | --- |
| **Send and read messages** | Yes | Yes | Yes |
| **Add or remove members** | Yes | Yes | - |
| **Remove a group administrator** | Yes | - | - |
| **Change roles, transfer ownership, delete group** | Yes | - | - |
| **Rename group** | Yes | Yes | If policy allows |
| **Approve pending members (when member approval is on)** | Yes | Yes | - |
| **Leave group** | If policy allows | If policy allows | If policy allows |

Member approval is on by default: an added member is pending until they accept, so nobody is placed into a conversation without consent. Group creation is itself a policy; a deployment chooses the model that suits its structure. Under the administrators-only policy, the default, administrators with the user-creation permission create groups from the console, naming an active end user as the owner and the initial members by Sanket ID; member approval applies to them as to any group, and each creation is audited. Notice broadcasts, the announcement type that is labelled as not end-to-end encrypted, can be sent only by administrators with the broadcast-creation permission, and only the administrator who created a notice can edit or send it.

## 15.3 Broadcast-Sender Accounts

Administrators hold no libsignal identity, so they cannot send end-to-end encrypted content. Encrypted broadcasts are therefore sent by a dedicated **broadcast-sender account**: a user account with its own Sanket ID, created, suspended and assigned to named administrators in the console, with every such action audited. It is excluded from contact search, from "all users" audiences and from ordinary chats. Its root of trust is a mobile device in the organisation's custody, and its sending desktop must be approved both by that mobile and in the administrator console. The desktop app encrypts each broadcast for every recipient device through its own libsignal Double Ratchet session, with no sender keys; recipients pin the sending device and identity on the first broadcast and are warned if a later broadcast comes from another device or key. Sending is a desktop function by design; receiving is identical on mobile and desktop. Chapter 17 describes the broadcast lifecycle.

## 15.4 End-User Policy Controls

Policies apply to the whole installation and are set by administrators with the settings permission. Clients receive a reduced copy of the policy from which sensitive values, such as lockout thresholds and rate limits, are removed. The principal enforced controls are:

*Table 31: Enforced policy controls (selection)*

| Area | Controls | Enforced at |
| --- | --- | --- |
| **Access** | Registration mode; TOTP requirement; administrator security keys; identity providers; lockout thresholds; device approval mode; trusted-device and per-platform caps; idle-device revocation | Server |
| **Local security** | App passcode requirement; biometric unlock; lock timeout; wipe after failed unlocks; duress PIN | Client (mobile); MDM can tighten |
| **Messaging** | Message editing, delete-for-everyone window, disappearing messages, read receipts, typing indicators | Server |
| **Messaging** | Forwarding, reactions (end-to-end encrypted; an optional emoji-free server count for very large groups), quoting, link previews, starring | Client |
| **Content handling** | Clipboard copy of message text; forwarding of files and of vault files into chats, and sharing attachments to other apps | Client (mobile and desktop) |
| **Classification** | Customer-defined ranked list of labels and a default label; content labelled above the lowest rank has forwarding, export and download turned off (Chapter 38) | Client (mobile and desktop); label inside the end-to-end encryption |
| **Files** | File sharing on or off per source (camera, gallery, documents, location, contacts, voice); maximum size; allowed and blocked types; download to device; data export | Server and client |
| **Calls** | Voice, video and group calls; maximum duration; participants; per-user call rates | Server |
| **Groups** | Creation permission; member approval; maximum members; renaming; leaving | Server |
| **Device governance** | Remote wipe per device and per user; lost mode and automatic wipe; locate; device mutual TLS; minimum key protection level; attestation enforcement | Server |
| **Privacy** | Notification sender names and previews (both off by default); last-seen visibility; profile photo and display-name policy | Server and client |
| **Retention** | Hot message window, broadcast, call log and alert retention (Chapter 44) | Server |
| **Integrations** | Integration API enablement; audit export | Server and licence |

> [!NOTE]
> **Note - Client-side controls**
>
> Client-enforced controls such as forwarding restrictions govern the behaviour of the SANKET applications. As with any messaging platform, they cannot prevent an authorised reader from copying content by other means, which is why SANKET pairs them with audit, download and export policy, and managed devices. Administrator sign-in and sessions can be limited to configured IPv4 and IPv6 address ranges, enforced by the API (Chapter 27); a firewall or reverse proxy in front of the console remains recommended.

## 15.5 Attribute-Based and Hierarchical Policy

SANKET's current model is role-based for administrators and membership-based for content, with installation-wide policy. It does not implement attribute-based access control, per-department policy or an organisational hierarchy with inherited permissions. Large organisations typically deploy separate installations for separate security domains, which gives strong compartmentalisation at the cost of cross-domain communication, and installation-level separation remains the recommended separation between formally accredited levels. Per-message and per-file classification labels are implemented (Chapter 38); hierarchical units, attribute-based policy, conversation-level labels and a per-label handling matrix are roadmap directions.

---

[Previous: 14. Device Trust Model](14-device-trust-model.md) | [Contents](../README.md) | [Next: 16. Zero Trust Architecture](16-zero-trust-architecture.md)

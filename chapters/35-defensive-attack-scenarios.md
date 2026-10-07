<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 35. Defensive Attack Scenarios

The following scenarios walk through realistic attacks from the defender's perspective: how each is prevented, detected, contained and recovered from, and what risk remains. They describe defensive behaviour only.

## 35.1 Scenario 1: Untrusted public network interception

| Stage | Description |
| --- | --- |
| **Attack** | An adversary controls the Wi-Fi or carrier network and records or manipulates traffic (ATT&CK: Adversary-in-the-Middle). |
| **Prevention** | TLS with AES-256-GCM and hybrid post-quantum key exchange where the client supports it; certificate validation against the customer CA; certificate pinning on desktop and mobile; device client certificates; E2EE inside TLS. |
| **Detection** | Certificate errors on clients; pin mismatch refusals. |
| **Containment** | Move users to trusted networks or VPN; revoke sessions if tokens may have been captured. |
| **Recovery** | Rotate token secrets if interception of tokens is suspected. |
| **Residual risk** | Metadata visible to the network; content remains end-to-end encrypted, with post-quantum protection from PQXDH and SPQR. |

## 35.2 Scenario 2: Compromised SANKET application server

| Stage | Description |
| --- | --- |
| **Attack** | An attacker gains code execution in the API container. |
| **Prevention** | Content keys absent from the server; signed device lists, so the server cannot add a device that receives copies; non-root containers; least-privilege access to secrets. |
| **Detection** | Unexpected processes or traffic (customer host monitoring); audit anomalies; clients reporting decryption failures or unexpected devices. |
| **Containment** | Isolate the host; stop the installation if necessary. |
| **Recovery** | Rebuild from a verified release; rotate all server secrets; force re-authentication; review device inventories. |
| **Residual risk** | Metadata and notice broadcasts observed until the server is isolated; content stays end-to-end encrypted. |

## 35.3 Scenario 3: Stolen employee device

| Stage | Description |
| --- | --- |
| **Attack** | A phone is stolen while locked. |
| **Prevention** | OS encryption; app passcode or biometric; SQLCipher; keys wrapped by a hardware key (Secure Enclave, StrongBox or TEE); wipe after failed unlocks. |
| **Detection** | User reports loss. |
| **Containment** | Mark lost: the device is refused by the server and receives no new messages. |
| **Recovery** | Remote wipe after confirmation; issue a replacement device; rotate identity if appropriate. |
| **Residual risk** | Forensic attacks on the OS if the device was unlocked or vulnerable. |

## 35.4 Scenario 4: Malicious system administrator

| Stage | Description |
| --- | --- |
| **Attack** | An administrator tries to read communications or cover activity. |
| **Prevention** | No content keys on server; RBAC; administrator security keys; signed device lists; audit chain; separation of duties. |
| **Detection** | Audit review; SIEM copy; chain verification; identity changes shown to every peer. |
| **Containment** | Revoke the administrator; preserve evidence. |
| **Recovery** | Review and reverse changes; review device approvals made by that administrator. |
| **Residual risk** | Access to metadata within the administrator's role. |

## 35.5 Scenario 5: Replay of captured encrypted traffic

| Stage | Description |
| --- | --- |
| **Attack** | An attacker resubmits captured API requests or messages. |
| **Prevention** | TLS; libsignal duplicate detection; single-use tokens; idempotency keys; webhook timestamps. |
| **Detection** | Duplicate-message errors; refresh-token reuse events. |
| **Containment** | Device revoked automatically on refresh reuse. |
| **Recovery** | User re-authenticates. |
| **Residual risk** | Minimal. |

## 35.6 Scenario 6: Compromised user password

| Stage | Description |
| --- | --- |
| **Attack** | An attacker obtains a user's password. |
| **Prevention** | TOTP required; lockout; rate limits; device caps and approval. |
| **Detection** | Failed TOTP events; new-device events; lockouts. |
| **Containment** | Reset password; suspend account if needed. |
| **Recovery** | Re-enrol TOTP if the second factor may be exposed. |
| **Residual risk** | Social engineering of the user; administrators use phishing-resistant security keys. |

## 35.7 Scenario 7: Unauthorised device access attempt

| Stage | Description |
| --- | --- |
| **Attack** | An attacker tries to add a device to an account. |
| **Prevention** | Device caps without eviction; administrator approval mode; trusted-phone linking with TOTP and signature; a device absent from the account's signed device list receives no messages. |
| **Detection** | Pending-device notifications; device-approved events. |
| **Containment** | Reject or revoke the device. |
| **Recovery** | Review account security; rotate credentials. |
| **Residual risk** | Social engineering of the user or an approver. |

## 35.8 Scenario 8: Database theft

| Stage | Description |
| --- | --- |
| **Attack** | A database copy is exfiltrated. |
| **Prevention** | E2EE content; Argon2id; encrypted secret fields; tombstoning. |
| **Detection** | Data-loss detection (customer). |
| **Containment** | Rotate field and TOTP keys if keys may also be exposed. |
| **Recovery** | Assess metadata disclosure; consider password resets. |
| **Residual risk** | Account and communication metadata in the copy. |

## 35.9 Scenario 9: Backup media theft

| Stage | Description |
| --- | --- |
| **Attack** | Backup media is lost or stolen. |
| **Prevention** | Archive encryption with a passphrase-derived key; E2EE content. |
| **Detection** | Custody records. |
| **Containment** | Treat as potential exposure of installation secrets. |
| **Recovery** | Rotate secrets and unseal material if the passphrase may be weak or exposed. |
| **Residual risk** | Offline guessing of a weak passphrase. |

## 35.10 Scenario 10: Supply-chain attack on an update package

| Stage | Description |
| --- | --- |
| **Attack** | A tampered package is introduced in transit. |
| **Prevention** | Signed manifest with sequence number and expiry; per-archive SHA-256; offline verification; signed timestamp on connected upgrades; customer staging. |
| **Detection** | Hash or signature mismatch; expired, replayed or lower-sequence manifest refused. |
| **Containment** | Reject the package. |
| **Recovery** | Obtain a fresh copy through an independent channel. |
| **Residual risk** | Compromise upstream of signing. |

## 35.11 Scenario 11: Lost device with cached messages

| Stage | Description |
| --- | --- |
| **Attack** | A device holding history is lost and may be unlocked later. |
| **Prevention** | Local encryption; lock; disappearing messages where used. |
| **Detection** | Loss report. |
| **Containment** | Lost mode; remote wipe. |
| **Recovery** | Replacement device; history on lost device not recoverable by the organisation. |
| **Residual risk** | Content already viewed on an unlocked device. |

## 35.12 Scenario 12: User removed from a confidential group

| Stage | Description |
| --- | --- |
| **Attack** | A member must lose access immediately. |
| **Prevention** | Removal takes effect in the send transaction; future fan-outs exclude their devices; downloads require active membership. |
| **Detection** | Membership-change records. |
| **Containment** | Removal; device revocation if the person is leaving. |
| **Recovery** | Consider rotating any shared secrets discussed. |
| **Residual risk** | The removed user keeps whatever they already read or saved. |

---

[Previous: 34. Security Control Matrix](34-security-control-matrix.md) | [Contents](../README.md) | [Next: 36. Deployment Mode Comparison](36-deployment-mode-comparison.md)

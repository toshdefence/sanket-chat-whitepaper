<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 50. Technical FAQ

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

#### Can Tosh Defence read customer messages?

No. Tosh Defence does not operate customer installations and has no access path into them. Message content is end-to-end encrypted with keys that exist only on users' devices.

#### Can customer administrators read communication content?

No. Administrators govern identities, devices and policy; the server holds no content keys. Administrators with database access can read server-visible metadata as described in Chapter 20, and notice broadcasts, which are labelled as not end-to-end encrypted.

#### What happens if a SANKET server is compromised?

The attacker gains metadata and the ability to disrupt service. Signed device lists prevent it from adding a device to an account to receive copies, and safety-number comparison verifies identities. Past message and file content remains confidential (Chapter 28).

#### What happens if an endpoint is stolen?

If locked, the data is protected by operating-system encryption, the app lock and encrypted local storage. The device can be placed in lost mode, revoked and remotely wiped. If the attacker obtains an unlocked device, they can see what the user could see until the device is locked or revoked.

#### Does SANKET require public internet?

No. It runs on private or air-gapped networks.

#### Can SANKET operate completely air-gapped?

Yes, with internal CA, DNS and time, self-hosted push for Android and offline releases and licences. iOS devices work without background wake-ups in that mode.

#### Does SANKET require foreign cloud services?

No. The media server never uses public STUN; its address is set at installation, and the TURN relay runs on the installation. Android Key Attestation is verified offline against vendor roots shipped in each release. The remaining optional connected-mode paths (Apple push for iOS and the optional status report) can each be disabled.

#### Where are keys stored?

End-user keys on the device, protected by the platform key store (on iOS under a key derived from a Secure Enclave key, on Android under a StrongBox or TEE key); server keys inside the installation; only public licence and release keys come from Tosh Defence. See Chapter 11.

#### Does SANKET support hardware-backed keys?

Yes. On iOS the protocol store, device secrets, the device certificate key and the account list key are encrypted with AES-256-GCM under a key derived from a Secure Enclave key; on Android they are protected by a StrongBox key where the phone has one and the TEE otherwise. libsignal identity keys stay in libsignal format, wrapped by the hardware key, because the Secure Enclave cannot hold Curve25519 keys. Android Key Attestation is verified offline; the protection level is recorded per device and policy can require one. Administrators sign in with FIDO2 security keys.

#### Can SANKET operate with private PKI?

Yes. The edge certificate and every device's client certificate can be issued under the customer's CA, and the SIEM connection pins the customer's CA.

#### Can SANKET integrate with LDAP or Active Directory?

Yes. User lifecycle is driven from the directory through SCIM 2.0 provisioning. An installation can also require the user's directory password, over LDAPS or StartTLS, when a device is enrolled or restored or a new device signs in; this is an extra check beside TOTP and device approval, never a replacement. Everyday sign-in stays local, so field devices work without the directory.

#### Does SANKET support single sign-on?

For the administration console, yes: through the customer's own OpenID Connect or SAML 2.0 identity provider. The provider is a first factor only, the local security key is still required, public identity providers are refused and no account is created on first sign-in. End users sign in locally.

#### Can retention be configured?

Yes, within limits: message hot window, broadcasts, call logs, alerts, disappearing messages and more (Chapter 44).

#### Can files be revoked?

Removing a member or revoking a device prevents future access. Files already obtained by an authorised recipient cannot be recalled by any platform.

#### How are backups protected?

Each component is encrypted with its own AES-256-GCM key. The recovery artefact's key is wrapped under a passphrase-derived key (scrypt) and every other key by the installation's OpenBao; a manifest signed with a non-exportable Ed25519 key is verified, with every artefact, before a restore changes anything. Scheduled database backups are signed and verified the same way, and content inside is already end-to-end encrypted. Hold the passphrase and the OpenBao unseal material separately from the backup media (Chapter 32).

#### How are air-gapped updates performed?

With a signed offline package, verified with the release public key, staged and installed by the customer with automatic rollback (Chapter 30).

#### What appears in audit logs?

Security and administrative events with actor, action, target, device, address, result and time. Never content, ciphertext or keys.

#### Is SANKET independently certified?

Not at the date of this document. Certification targets are described in Chapters 40 and 41.

#### Is SANKET post-quantum secure?

SANKET messaging uses hybrid post-quantum cryptography for both session establishment (PQXDH with Kyber-1024) and the ongoing ratchet (SPQR). The edge offers the hybrid X25519MLKEM768 TLS key exchange first, and it is negotiated wherever the client platform's TLS stack supports it. Symmetric encryption is 256-bit throughout. Signatures remain classical (Chapter 39).

#### What is protected if the server is compromised?

Message bodies, reactions, encrypted broadcasts, classification labels, file contents, call media and private keys.

#### Are reactions end-to-end encrypted?

Yes. A reaction is a small libsignal message; the server stores only opaque per-device copies and each device computes the counts. For groups of 500 or more active members a tenant option, off by default, lets the server keep a count per message: it then learns who reacted to which message, never the emoji.

#### Are broadcasts end-to-end encrypted?

Encrypted broadcasts are. They are sent from a dedicated broadcast-sender account's approved desktop, which encrypts a copy for every recipient device with libsignal; priority, deadline and expiry are inside the encryption too, so the server cannot downgrade or replay them. Notice broadcasts, for non-sensitive scheduled announcements, are readable by the server and labelled "Notice (not end-to-end encrypted)".

#### Does SANKET support classification labels?

Yes. The customer defines up to eight ranked labels. A label travels inside the end-to-end encrypted message, so the server can neither read nor change it, and content above the lowest rank cannot be forwarded, exported or downloaded. Separation between formally accredited levels remains by installation (Chapter 38).

#### What is exposed if the endpoint is compromised?

Everything that endpoint can decrypt and display while compromised.

#### How does device revocation work?

The server marks the device revoked; its next request or refresh fails; it is excluded from future fan-outs; its client certificate is revoked; a remote wipe can also be requested.

#### What happens when a user leaves the organisation?

Suspend or delete the account (SCIM delete suspends), which revokes access; revoke or wipe the user's devices; remove them from groups. Their Sanket ID is not reused.

#### Can SANKET support multiple sites?

Yes. The high-availability profile runs on several hosts with Docker Compose and replicates the databases, object storage and secrets service asynchronously to a second site, with drill tooling to fence, promote, switch and fail back. The reference topology is designed for a recovery point of 5 minutes and a recovery time of 1 hour; each deployment's disaster-recovery drill records its measured figures. Separate installations and a standby restored from backups remain available (Chapter 32).

#### Is SANKET highly available?

With the multi-host profile, yes: two edge hosts, more than one API, WebSocket and media node, replicated PostgreSQL with fenced failover, Redis with Sentinel, distributed MinIO and a three-node OpenBao cluster. The single-host profile remains available for small and disconnected sites. Kubernetes is on the roadmap.

#### Can SANKET support multiple departments or business units?

Within an installation, through groups and administrator roles; for strong separation, through separate installations. Hierarchical units are a roadmap item.

#### Can SANKET integrate with existing internal systems?

Yes: Integration API with scoped credentials, SCIM 2.0, syslog to SIEM and signed webhooks, documented in OpenAPI.

#### What metadata does SANKET retain?

See the inventory in Chapter 20 and the retention schedule in Chapter 44.

#### Can all telemetry remain local?

Yes. Product applications contain no third-party analytics or crash reporting; first-party usage counts stay in the installation, and the organisation can switch them off or the installation can be locked with them off; the status report to Tosh Defence is not sent under an offline licence.

#### How does licensing work in a disconnected deployment?

The licence is a signed file verified locally. Renewals are signed offline bundles bound to the tenant with an increasing counter.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 49. SANKET within the Tosh Defence Ecosystem](49-sanket-within-the-tosh-defence-ecosystem.md) | [Contents](../README.md) | [Next: Appendix A - Cryptographic Algorithm Reference](appendix-a-cryptographic-algorithm-reference.md)

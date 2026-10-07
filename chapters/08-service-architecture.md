<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 8. Service Architecture

SANKET separates functions into containers with distinct privileges, so that the failure or compromise of one function has a bounded effect. This chapter describes each service's responsibility, the controls around it and the effect of its failure.

## 8.1 Why Separation Matters

- **Privilege separation.** The secrets service is reachable only from the services that need it, on an internal-only network; the edge never talks to it.
- **Blast radius.** Each service holds only the credentials it needs, so a defect in one service does not expose credentials held by another.
- **Independent scaling.** The media server, notifier and API have different resource profiles and can be sized independently. The Edge profile sets per-service CPU and memory limits.
- **Independent upgrade.** Each service is a separately pinned image in the signed release manifest.

The application tier is intentionally consolidated: authentication, messaging relay, groups, files, broadcast and administration share one API process. This keeps the number of moving parts, credentials and network paths small for customers who operate the platform themselves, at the cost of coarser internal isolation than a fully decomposed microservice design. Logical separation inside the API is enforced through route-level permission checks and distinct database access paths.

*Table 13: Service responsibilities, controls and failure impact*

| Service | Responsibility | Security controls | Failure impact |
| --- | --- | --- | --- |
| **Edge (nginx)** | Server-name routing of TCP 443 to HTTPS or TURN without decryption; TLS termination for HTTPS and TURN; device client-certificate verification; rate limiting; headers | AES-256-GCM-only suites, TURN hop TLS 1.3 only; hybrid X25519MLKEM768 key exchange offered first; session tickets off; HSTS; strict CSP for API responses; client address preserved and forged forwarding headers from outside refused; customer CA and revocation list for device certificates; identifier masking in logs; connection and request limits | Service unavailable; no content exposure |
| **Authentication (API)** | Sign-in, TOTP, security keys, federated sign-in, tokens, sessions, lockout | Argon2id; WebAuthn with ES256 and EdDSA only, user verification required, synced passkeys refused by default and sign-count regression refused; OIDC with PKCE and verified ID tokens, SAML with a pinned signing certificate and replay cache, always beside the local second factor; HS256-only verification of SANKET tokens; separate access and refresh secrets; single-use refresh with reuse detection; device certificate fingerprint bound to the device record; rate limits | Users cannot sign in; existing sessions continue until expiry |
| **Messaging relay (API + WebSocket)** | Accept per-device ciphertext, store, deliver, receipts | Active-membership checks; fan-out caps; idempotency keys; per-user send rate; tombstoning | Delivery paused; clients queue in encrypted outbox |
| **Key directory and device lists (API)** | Publish and serve prekey bundles; keep the append-only log of signed device lists | libsignal signature verification of signed and Kyber prekeys; trusted-device check on upload; consumption of one-time prekeys; a device list that does not extend the previous one is refused, and clients check the log themselves; rate limits | New sessions and new devices cannot be added; existing sessions continue |
| **Files (API + MinIO)** | Upload and download encrypted objects | Licence and tenant gates; size and type limits; active membership on download; lifecycle expiry | Attachments unavailable |
| **Broadcast (API)** | Encrypted broadcasts from a broadcast-sender account; clear notice broadcasts from the console; acknowledgement, deadline and escalation | Broadcast permission; named administrators for each broadcast-sender account; one approved sending desktop per account; recipient cap; generic push text by priority; audit | Broadcasts delayed |
| **Calls signalling (API + Redis)** | Room tokens, ringing, key-message relay | Membership checks; server-signed device identity in room token; AES-256 profile enforcement; one call per device | Calls cannot start |
| **Media server (LiveKit)** | Forward media between participants; TURN relay behind the edge | SRTP AES-256-GCM only (patched build with proof tests); TURN relay limited to the node's own media address (patched); no TLS certificate key; frame E2EE; token-gated rooms | Calls drop; messaging unaffected |
| **Notifier + ntfy** | Wake devices | Sealed, padded payloads; constant priority; decoys; no content | Wake-ups delayed; WebSocket delivery continues |
| **Administration (API + console)** | Users, devices, policy, monitors, audit | Mandatory administrator second factor; RBAC; role ceiling; audit of every change | Administration unavailable; users unaffected |
| **Integration API, SCIM, syslog, webhooks** | Machine interfaces for customer systems | Scoped expiring credentials stored as SHA-256; network allowlist; per-client limits; HMAC-signed webhooks; syslog over TLS with pinned CA | Integrations pause; core service unaffected |
| **Scheduler** | Retention, purge, hygiene, status | Least-privilege OpenBao token; internal network | Retention delayed; storage grows |
| **OpenBao** | Non-exportable signing and wrapping keys; device-certificate PKI | Internal-only network; Shamir unseal ceremony in production; least-privilege runtime token; device certificates carry an opaque random name and a short lifetime; three-node raft with TLS between nodes in the high-availability profile | Signing, backup wrapping and device-certificate issue unavailable |
| **PostgreSQL / Redis** | Persistent state; cache and live state | No host ports; authentication on Redis; Redis split into an evictable cache and a state instance that never evicts; streaming replication and Sentinel in the high-availability profile | Service unavailable |
| **Analytics processor** | Aggregate first-party usage events | Inside the installation, no third party; health monitored; can be switched off by the organisation or locked off at provisioning | Analytics stop; chat and calls unaffected |
| **Threat processor (optional)** | Rule-based detection on event stream | Separate database; configurable | Alerts from this source stop; built-in protections continue |

---

[Previous: 7. System Architecture](07-system-architecture.md) | [Contents](../README.md) | [Next: 9. Data Architecture](09-data-architecture.md)

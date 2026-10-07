<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 21. Audit Architecture

SANKET makes the use and administration of the system accountable without making communications readable. Audit records describe security and administrative events; they never contain message content, ciphertext, keys or file contents.

*Table 39: Separation of content and audit metadata*

| CONTENT (never audited) | SECURITY AND ADMINISTRATIVE METADATA (audited) |
| --- | --- |
| Message bodies, attachments, call media, keys, ciphertext | Who did what, to which object, from which device and address, when, with what result and severity |

## 21.1 Events Recorded

Every audited action is mapped to a category, severity and result. Events cover registration and sign-in, administrator authentication, threat detections, data access (never content), calls, the device lifecycle, classification labels, administration and integrations.

Each record carries an event identifier, the actor and actor type (user, administrator, system or integration), action, resource, target, IP address, user agent, endpoint, device, category, severity, result and timestamp. End users are identified by Sanket ID in exports and in the console; internal identifiers are not exposed in the exported stream. Integration calls record the route pattern rather than the full URL, so that a credential mistakenly placed in a query string is never written to the log. End users can review their own recent security activity.

## 21.2 Tamper Evidence

The audit log is a hash chain. Each record stores a sequence number, the previous record's hash and its own hash, computed with SHA-256 over the previous hash, the sequence number and the record's fields. Writes join the chain one at a time under a database lock. A verifier, available to holders of the audit-read permission and run by the licence-and-audit monitor, walks the chain and reports sequence gaps, broken links, recomputation mismatches and deleted rows. Exported events carry their hash links so that a SIEM can check continuity independently.[^57]

The chain head is signed at regular intervals with an Ed25519 key held non-exportable in the tenant's key service, and the newest signed anchor is also recorded outside the database. The verifier checks every anchor's signature, that the anchors form an unbroken sequence and that the chain passes through each anchored hash, so a database superuser who rewrites history and recomputes every later hash to hide it is still detected, as is a forged, altered or deleted anchor. The pull API and every syslog message carry the latest anchor, so a receiver that pins the public key can check it independently.

> [!TIP]
> **Recommended Practice - An independent copy**
>
> The hash chain makes alteration, insertion or deletion of records detectable, and the signed anchors make a rewrite detectable even by a database superuser. For the strongest assurance, customers forward the audit stream to an external SIEM or write-once archive administered by a separate team: that independent copy, with the anchors it receives, holds the chain outside the installation and satisfies WORM retention requirements.

## 21.3 Access, Retention and Export

- **Access:** reading the audit log requires the audit-read permission. The Owner, User Manager, Threat Monitor and Auditor roles include it; the Config Manager role does not.
- **Retention:** audit records are retained indefinitely by default to preserve chain continuity; a retention period can be configured (default setting 365 days) and pruning enabled explicitly by the operator.
- **CSV export** from the administrator console.
- **Syslog:** RFC 5424 messages framed per RFC 5425 over TLS (AES-256-GCM suites), with the customer's CA pinned rather than the system trust store, server name checked, at-least-once delivery in chain order, and a persisted cursor.[^58][^59]
- **Pull API:** paginated audit events by Sanket ID with hash links, through a scoped integration credential.
- **Webhooks:** device and administrator security events, signed with HMAC-SHA256 over a timestamp and body.

## 21.4 Administrators Cannot Audit Plaintext

Because audit is metadata-only and the server holds no content keys, there is no audit view, export or administrative action that reveals message plaintext. Organisations that need content supervision must implement it at the endpoint under their own policy; SANKET does not provide a server-side mechanism for it.

---

[^57]: K. Kent, M. Souppaya, "Guide to Computer Security Log Management", NIST SP 800-92, September 2006.
[^58]: R. Gerhards, "The Syslog Protocol", IETF RFC 5424, March 2009.
[^59]: F. Miao, Y. Ma, J. Salowey, "Transport Layer Security (TLS) Transport Mapping for Syslog", IETF RFC 5425, March 2009.

---

[Previous: 20. Metadata Security](20-metadata-security.md) | [Contents](../README.md) | [Next: 22. Monitoring, Threat Detection and Observability](22-monitoring-threat-detection-and-observability.md)

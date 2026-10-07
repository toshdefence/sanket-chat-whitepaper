<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix H - Network and Protocol Categories

| Category | Protocol | Direction | Notes |
| --- | --- | --- | --- |
| **Client application traffic** | HTTPS / WSS (TLS 1.3 or 1.2) | Inbound to edge | All API, WebSocket, console and signalling traffic |
| **Real-time media** | DTLS-SRTP over UDP; TCP fallback | Inbound to media server | AES-256-GCM SRTP |
| **Media relay** | TURN over UDP, or TURN over TLS on TCP 443 (TLS 1.3 AES-256-GCM only), routed by server name | Inbound | On by default for internet-facing installations, off for air-gapped ones |
| **Android wake-ups** | HTTPS to self-hosted push relay | Device to edge | Sealed payloads |
| **iOS wake-ups (optional)** | HTTPS / HTTP/2 to Apple push | Outbound | Connected deployments only |
| **Security events** | Syslog over TLS | Outbound to SIEM | RFC 5424 / 5425 |
| **Integration events** | HTTPS webhooks | Outbound | HMAC-SHA256 signed |
| **Provisioning** | HTTPS (SCIM 2.0, Integration API) | Inbound | Scoped credentials |
| **Directory and identity provider (optional)** | LDAPS or StartTLS; HTTPS (OIDC, SAML 2.0) | Outbound to the customer's directory and identity provider | Customer-hosted only; public providers refused |
| **Second-site replication (high-availability profile)** | Database streaming replication and WAL archive; object-storage site replication; encrypted secrets snapshots | Site to site | Asynchronous; customer-operated link |
| **Administration** | SSH via bastion | Management network | Customer-operated |
| **Backups** | Customer-chosen transport | Outbound to backup zone | Encrypted archives |

---

[Previous: Appendix G - Security Hardening Checklist](appendix-g-security-hardening-checklist.md) | [Contents](../README.md) | [Next: Appendix I - Glossary](appendix-i-glossary.md)

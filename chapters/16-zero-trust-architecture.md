<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 16. Zero Trust Architecture

Zero trust, as defined by NIST, means that no implicit trust is granted to assets or accounts based on their network location or ownership, and that authentication and authorisation are discrete functions performed before each session to a resource.[^50]

In practice SANKET refuses to treat any of the following as sufficient grounds for access:

- the request comes from an internal network or a trusted VLAN;
- the user authenticated earlier;
- the device was authorised last month;
- the caller is an administrator who owns the infrastructure;
- the connection arrived through the edge proxy.

![Zero trust policy evaluation](images/zerotrust.png)

*Figure 8: Zero trust policy evaluation*

## 16.1 Evaluation Inputs

*Table 32: Evidence evaluated per request*

| Input | Evidence | Freshness |
| --- | --- | --- |
| **Identity** | Valid, unexpired HS256 token whose subject account is active | Short-lived access token |
| **Device** | Device in the trusted state, not lost or revoked | Re-checked every request |
| **Device certificate** | Client certificate verified by the edge against the device CA and its revocation list; the verified fingerprint must match the device record that the token names | Every connection on the device routes |
| **Key protection** | Reported hardware protection level of the device, attested on Android | Policy can require a minimum level |
| **Session** | Refresh token not previously used; generation counter current | Every refresh |
| **Role and permission** | Administrator category and action; group role | Every request |
| **Resource relationship** | Active membership of the conversation for messages, files and calls | Every request; locked inside send transactions |
| **Policy and licence** | Feature enabled by tenant policy and licence; limits | Every request |
| **Abuse state** | Rate limits, blocks, lockout | Every request |
| **Step-up** | TOTP re-authentication for sensitive operations such as device linking and identity rotation; a fresh security-key assertion for administrator key management and for an owner revoking another administrator's keys | Per operation |

## 16.2 Cryptography as the Last Control

Zero trust is usually discussed as an access-decision architecture. SANKET adds a cryptographic layer underneath it: even when every access check passes, including for an administrator or a compromised server process, what the server can hand over is ciphertext. The access decision governs metadata and service; the cryptography governs content.

The same principle extends to the list of devices a sender encrypts for. The server supplies that list, but the sender does not take it on trust: every account's device list is signed by its own account list key, and the sending device verifies the signed list before it encrypts, on mobile and desktop alike. A device that the server adds without a valid signed entry receives nothing (Chapter 13).

## 16.3 Device Mutual TLS

A device-bound token proves that a session was issued to a device; a client certificate proves, on every connection, that the connection comes from that device's key. Every approved device holds a short-lived certificate whose ECDSA P-256 key never leaves it (Chapter 10). The edge verifies the certificate and the revocation list and passes the verified fingerprint to the API, which binds it to the device record: a token presented with another device's certificate is refused, and where certificates are required a stolen access token is useless on a machine that does not hold the device key. Enforcement is configurable per tenant.

## 16.4 Integration with the Customer's Zero Trust Architecture

- Device posture (patch level, root or jailbreak state) is supplied by the customer's MDM, and SANKET also checks the phone for root and jailbreak indicators itself, reporting them and, by policy, blocking use.
- Network context (IP reputation, country) is used for rate limiting and blocking; administrator access can be limited to configured address ranges, enforced by the API, with the customer's edge as a further layer.
- Internal service traffic stays on isolated container networks inside the host in the single-host profile. In the multi-host high-availability profile, database replication and traffic between OpenBao nodes run over TLS with AES-256-GCM only, and the device CA and revocation list are loaded on every edge host (Chapter 32).

---

[^50]: S. Rose, O. Borchert, S. Mitchell, S. Connelly, "Zero Trust Architecture", NIST SP 800-207, August 2020.

---

[Previous: 15. Role and Policy Architecture](15-role-and-policy-architecture.md) | [Contents](../README.md) | [Next: 17. Message Security Lifecycle](17-message-security-lifecycle.md)

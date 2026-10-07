<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 46. What SANKET Does Not Claim

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

Precise claims are more useful than broad ones. The statements below define the edges of SANKET's security properties.

1. End-to-end encryption cannot protect plaintext on a fully compromised authorised endpoint.
2. Air-gapping does not eliminate insider risk; it removes external paths, not trusted people.
3. Screenshot restrictions cannot prevent an external camera.
4. Revocation cannot erase plaintext that was already legitimately obtained or exported.
5. Encryption does not replace endpoint security, device management or user training.
6. The server is not blind to metadata: it knows who communicates with whom and when, including the sender of every message, and it can read notice broadcasts, which are labelled as not end-to-end encrypted (Chapter 20). Sealed sender is not implemented.
7. SANKET is not "zero knowledge" in the cryptographic sense.
8. Post-quantum protection covers messaging (PQXDH and SPQR) and, wherever the client platform's TLS stack supports it, hybrid key exchange at the edge. Other transport paths and all signatures (identity keys, licences, release manifests, device certificates) remain classical and follow the roadmap in Chapter 39.
9. SANKET holds no independent security certification at the date of this document. Certification requires independent assessment.
10. Software classification labels do not confer accreditation to process government classified information. Their handling rules are enforced by SANKET's own clients; separation between accredited levels is by installation.
11. Signed device lists stop the server adding a device to an account, but the first identity key seen for a new contact is trusted on first use; safety-number comparison is how a substituted identity is detected.
12. Infrastructure security, host hardening, physical security and key custody remain the customer's responsibility in a self-hosted deployment.
13. High availability across sites is asynchronous: writes since the last replicated point can be lost when a site fails. Recovery figures are design targets that each deployment's drill measures, not guarantees.
14. Key protection on each device is bounded by the key store its platform provides.
15. A file already delivered cannot be revoked per share, and roadmap capabilities such as push-to-talk, forensic watermarking and conversation-level classification labels are not claimed.
16. No responsible platform can guarantee absolute immunity from compromise.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 45. Security and Usability Trade-offs](45-security-and-usability-trade-offs.md) | [Contents](../README.md) | [Next: 47. Why Air-Gapped SANKET Is Structurally Different](47-why-air-gapped-sanket-is-structurally-different.md)

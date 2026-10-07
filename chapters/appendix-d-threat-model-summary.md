<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix D - Threat Model Summary

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

*Table 75: Threat actors by STRIDE category*

| STRIDE category | Principal actors | Principal controls |
| --- | --- | --- |
| **Spoofing** | Credential thief, rogue device, man-in-the-middle, compromised server | TOTP; administrator security keys; device states and caps; signed device links and device lists; device client certificates; identity pinning; certificate validation |
| **Tampering** | Network adversary, compromised server, supply-chain attacker | libsignal MACs; GCM tags; TLS; signed releases with sequence and expiry; labels and broadcast priority inside the encryption |
| **Repudiation** | Malicious insider, privileged administrator | Hash-chained audit; SIEM export |
| **Information disclosure** | Eavesdropper, database or storage thief, metadata analyst, endpoint malware | E2EE; local encryption; retention; self-hosting |
| **Denial of service** | DoS attacker, compromised network device | Rate limits; caps; block manager; customer DDoS protection |
| **Elevation of privilege** | External attacker, lateral mover, malicious update | RBAC; least-privilege tokens; network isolation; signed updates |

The full matrix of twenty-three actors is in Chapter 5.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: Appendix C - Security Control Matrix](appendix-c-security-control-matrix.md) | [Contents](../README.md) | [Next: Appendix E - Deployment Reference Architectures](appendix-e-deployment-reference-architectures.md)

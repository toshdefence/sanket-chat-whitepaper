<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix J - Standards and References

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

1. E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.3", IETF RFC 8446, August 2018.
2. T. Dierks, E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", IETF RFC 5246, August 2008.
3. Y. Sheffer, P. Saint-Andre, T. Fossati, "Recommendations for Secure Use of TLS and DTLS", IETF RFC 9325 (BCP 195), November 2022.
4. E. Rescorla, "TLS Elliptic Curve Cipher Suites with SHA-256/384 and AES Galois Counter Mode (GCM)", IETF RFC 5289, August 2008.
5. A. Langley, M. Hamburg, S. Turner, "Elliptic Curves for Security" (X25519, X448), IETF RFC 7748, January 2016.
6. S. Josefsson, I. Liusvaara, "Edwards-Curve Digital Signature Algorithm (EdDSA)", IETF RFC 8032, January 2017.
7. H. Krawczyk, P. Eronen, "HMAC-based Extract-and-Expand Key Derivation Function (HKDF)", IETF RFC 5869, May 2010.
8. H. Krawczyk, M. Bellare, R. Canetti, "HMAC: Keyed-Hashing for Message Authentication", IETF RFC 2104, February 1997.
9. NIST, "Advanced Encryption Standard (AES)", FIPS PUB 197 (updated 2023).
10. M. Dworkin, "Recommendation for Block Cipher Modes of Operation: Galois/Counter Mode (GCM) and GMAC", NIST SP 800-38D, November 2007.
11. E. Barker, "Recommendation for Key Management: Part 1 - General", NIST SP 800-57 Part 1 Rev. 5, May 2020.
12. E. Barker, J. Kelsey, "Recommendation for Random Number Generation Using Deterministic Random Bit Generators", NIST SP 800-90A Rev. 1, June 2015.
13. E. Barker, A. Roginsky, "Transitioning the Use of Cryptographic Algorithms and Key Lengths", NIST SP 800-131A Rev. 2, March 2019.
14. S. Rose, O. Borchert, S. Mitchell, S. Connelly, "Zero Trust Architecture", NIST SP 800-207, August 2020.
15. P. Grassi et al., "Digital Identity Guidelines: Authentication and Lifecycle Management", NIST SP 800-63B.
16. K. Kent, M. Souppaya, "Guide to Computer Security Log Management", NIST SP 800-92, September 2006.
17. M. Souppaya, K. Scarfone, D. Dodson, "Secure Software Development Framework (SSDF) Version 1.1", NIST SP 800-218, February 2022.
18. M. Souppaya, J. Morello, K. Scarfone, "Application Container Security Guide", NIST SP 800-190, September 2017.
19. M. Swanson et al., "Contingency Planning Guide for Federal Information Systems", NIST SP 800-34 Rev. 1, May 2010.
20. M. Marlinspike, T. Perrin, "The X3DH Key Agreement Protocol", Signal specification, Revision 1, November 2016. signal.org/docs/specifications/x3dh/
21. E. Kret, R. Schmidt, "The PQXDH Key Agreement Protocol", Signal specification, Revision 3, May 2023 (updated January 2024). signal.org/docs/specifications/pqxdh/
22. T. Perrin, M. Marlinspike, "The Double Ratchet Algorithm", Signal specification. signal.org/docs/specifications/doubleratchet/
23. T. Perrin, "The XEdDSA and VXEdDSA Signature Schemes", Signal specification, Revision 1, October 2016. signal.org/docs/specifications/xeddsa/
24. M. Marlinspike, T. Perrin, "The Sesame Algorithm: Session Management for Asynchronous Message Encryption", Signal specification, April 2017. signal.org/docs/specifications/sesame/
25. Signal, "Signal Protocol and Post-Quantum Ratchets" (Sparse Post-Quantum Ratchet, SPQR), signal.org/blog/spqr/, October 2025.
26. K. Cohn-Gordon, C. Cremers, B. Dowling, L. Garratt, D. Stebila, "A Formal Security Analysis of the Signal Messaging Protocol", IEEE EuroS&P 2017; extended version in Journal of Cryptology 33, 2020.
27. K. Bhargavan, C. Jacomme, F. Kiefer, R. Schmidt, "Formal verification of the PQXDH Post-Quantum key agreement protocol for end-to-end secure messaging", USENIX Security 2024.
28. Signal Messenger LLC, "libsignal" (Rust implementation with Java, Swift and TypeScript bindings), AGPL-3.0, github.com/signalapp/libsignal.
29. Signal Messenger LLC, libsignal source, group cipher (Sender Key) implementation, github.com/signalapp/libsignal (rust/protocol/src/group_cipher.rs).
30. D. M'Raihi et al., "TOTP: Time-Based One-Time Password Algorithm", IETF RFC 6238, May 2011.
31. M. Jones, J. Bradley, N. Sakimura, "JSON Web Token (JWT)", IETF RFC 7519, May 2015.
32. Y. Sheffer, D. Hardt, M. Jones, "JSON Web Token Best Current Practices", IETF RFC 8725, February 2020.
33. T. Lodderstedt et al., "Best Current Practice for OAuth 2.0 Security" (refresh token rotation), IETF RFC 9700, January 2025.
34. A. Biryukov, D. Dinu, D. Khovratovich, S. Josefsson, "Argon2 Memory-Hard Function for Password Hashing and Proof-of-Work Applications", IETF RFC 9106, September 2021.
35. OWASP Foundation, "Password Storage Cheat Sheet", cheatsheetseries.owasp.org.
36. OWASP Foundation, "Mobile Application Security Verification Standard (MASVS)" v2, mas.owasp.org.
37. OWASP Foundation, "Pinning Cheat Sheet", cheatsheetseries.owasp.org.
38. W3C, "Web Authentication: An API for accessing Public Key Credentials, Level 2", W3C Recommendation, April 2021.
39. FIDO Alliance, "Client to Authenticator Protocol (CTAP) 2.1", fidoalliance.org.
40. NIST, "Module-Lattice-Based Key-Encapsulation Mechanism Standard" (ML-KEM), FIPS 203, August 2024.
41. NIST, "Module-Lattice-Based Digital Signature Standard" (ML-DSA), FIPS 204, August 2024.
42. NIST, "Stateless Hash-Based Digital Signature Standard" (SLH-DSA), FIPS 205, August 2024.
43. NIST, "Transition to Post-Quantum Cryptography Standards", NIST IR 8547 (Initial Public Draft), November 2024.
44. D. Cooper et al., "Internet X.509 Public Key Infrastructure Certificate and CRL Profile", IETF RFC 5280, May 2008.
45. S. Santesson et al., "X.509 Internet PKI Online Certificate Status Protocol - OCSP", IETF RFC 6960, June 2013.
46. J. Hodges, C. Jackson, A. Barth, "HTTP Strict Transport Security (HSTS)", IETF RFC 6797, November 2012.
47. R. Gerhards, "The Syslog Protocol", IETF RFC 5424, March 2009.
48. F. Miao, Y. Ma, J. Salowey, "Transport Layer Security (TLS) Transport Mapping for Syslog", IETF RFC 5425, March 2009.
49. P. Hunt et al., "System for Cross-domain Identity Management: Core Schema", IETF RFC 7643, September 2015.
50. P. Hunt et al., "System for Cross-domain Identity Management: Protocol", IETF RFC 7644, September 2015.
51. M. Baugher et al., "The Secure Real-time Transport Protocol (SRTP)", IETF RFC 3711, March 2004.
52. D. McGrew, E. Rescorla, "DTLS Extension to Establish Keys for SRTP", IETF RFC 5764, May 2010.
53. D. McGrew, K. Igoe, "AES-GCM Authenticated Encryption in SRTP", IETF RFC 7714, December 2015.
54. E. Omara et al., "Secure Frame (SFrame): Lightweight Authenticated Encryption for Real-Time Media", IETF RFC 9605, August 2024.
55. The MITRE Corporation, "MITRE ATT&CK Enterprise and Mobile Matrices", attack.mitre.org.
56. A. Shostack, "Threat Modeling: Designing for Security", Wiley, 2014 (STRIDE methodology).
57. ISO/IEC 27001:2022, "Information security, cybersecurity and privacy protection - Information security management systems - Requirements".
58. OpenSSF, "Supply-chain Levels for Software Artifacts (SLSA)" v1.0, slsa.dev.
59. OWASP Foundation, "CycloneDX Bill of Materials Standard" (ECMA-424); and The Linux Foundation, "SPDX Specification" (ISO/IEC 5962:2021).
60. Zetetic LLC, "SQLCipher Design" (AES-256 page encryption with HMAC page authentication), zetetic.net/sqlcipher/design/.
61. Apple Inc., "Apple Platform Security Guide" (Secure Enclave, Keychain data protection), support.apple.com/guide/security.
62. Android Open Source Project, "Android Keystore system" and "Hardware-backed Keystore", developer.android.com and source.android.com.
63. Electron project, "safeStorage" API documentation, electronjs.org/docs/latest/api/safe-storage.
64. LiveKit project, "LiveKit open source WebRTC SFU" and "End-to-end encryption" documentation, github.com/livekit/livekit and docs.livekit.io.
65. Signal, "Technology preview: Sealed sender for Signal", signal.org/blog/sealed-sender/, October 2018.
66. R. Barnes, K. Bhargavan, B. Lipp, C. Wood, "Hybrid Public Key Encryption", IETF RFC 9180, February 2022.
67. NIST, "Security and Privacy Controls for Information Systems and Organizations", NIST SP 800-53 Rev. 5, September 2020.
68. Center for Internet Security, "CIS Docker Benchmark", cisecurity.org.
69. OpenBao project (Linux Foundation), "OpenBao: secrets management, encryption as a service and privileged access management", openbao.org.
70. MinIO, Inc., "Server-Side Encryption" documentation, min.io/docs.
71. ntfy project, "ntfy - push notifications made easy" (self-hostable pub-sub notification server), docs.ntfy.sh.
72. T. Reddy, A. Johnston, P. Matthews, J. Rosenberg, "Traversal Using Relays around NAT (TURN): Relay Extensions to Session Traversal Utilities for NAT (STUN)", IETF RFC 8656, February 2020.
73. W. Tarreau, "The PROXY protocol, Versions 1 & 2", HAProxy Technologies specification.
74. K. Kwiatkowski, P. Kampanakis, B. Westerbaan, D. Stebila, "Post-quantum hybrid ECDHE-MLKEM Key Agreement for TLSv1.3", IETF Internet-Draft draft-ietf-tls-ecdhe-mlkem.
75. N. Sakimura, J. Bradley, M. Jones, B. de Medeiros, C. Mortimore, "OpenID Connect Core 1.0", OpenID Foundation.
76. N. Sakimura, J. Bradley, N. Agarwal, "Proof Key for Code Exchange by OAuth Public Clients", IETF RFC 7636, September 2015.
77. OASIS, "Assertions and Protocols for the OASIS Security Assertion Markup Language (SAML) V2.0", OASIS Standard, March 2005.
78. Android Open Source Project, "Verifying hardware-backed key pairs with Key Attestation", Android developer documentation.
79. J. Samuel, N. Mathewson, J. Cappos, R. Dingledine, "Survivable Key Compromise in Software Update Systems", ACM CCS 2010; and "The Update Framework Specification".

Product documentation cited for SANKET components refers to the upstream open-source projects named; their inclusion does not imply endorsement by those projects.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: Appendix I - Glossary](appendix-i-glossary.md) | [Contents](../README.md)

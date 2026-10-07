<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix B - Key Hierarchy

The complete key inventory, with origin, storage, purpose, rotation and exposure consequence for each key, is in Chapter 11 (Key Management Architecture) and its figure. In summary:

| Tier | Keys | Custodian |
| --- | --- | --- |
| **Endpoint content tier** | libsignal identity, prekeys and sessions; per-file keys; call sender keys; local cache DEK and envelope key; account list key; device certificate key; hardware wrapping key (Secure Enclave, StrongBox or TEE) | User device |
| **Administrator tier** | FIDO2 security-key private keys (only public keys registered on the installation) | Administrator |
| **Installation tier** | Edge TLS key; device certificate issuing key (OpenBao PKI); token secrets; field and TOTP keys; OpenBao Transit keys; push payload keys; identity-provider secrets; backup passphrase | Customer operator |
| **Customer PKI tier** | Root and issuing CA keys | Customer PKI administrator |
| **Vendor tier** | Licence, release and release-timestamp signing keys (public halves only in the installation) | Tosh Defence |

---

[Previous: Appendix A - Cryptographic Algorithm Reference](appendix-a-cryptographic-algorithm-reference.md) | [Contents](../README.md) | [Next: Appendix C - Security Control Matrix](appendix-c-security-control-matrix.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 33. Hardware Security

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

SANKET does not require special hardware, but it benefits from, and is designed to be deployed with, hardware security features on servers and endpoints.

*Table 57: Hardware security options*

| Feature | Use with SANKET | Status |
| --- | --- | --- |
| **Mobile secure storage (Keychain, Keystore)** | Protects protocol state, device keys and refresh tokens on phones, this device only | **IMPLEMENTED** |
| **Secure Enclave (iOS)** | A non-exportable P-256 key in the Secure Enclave; a 256-bit AES-GCM key derived from it (ECDH and HKDF-SHA256) wraps the libsignal store, device secrets, the device certificate key and the account list key. Usable after first unlock, so background delivery and calls work while locked | **IMPLEMENTED** |
| **StrongBox, with TEE fallback (Android)** | A StrongBox key where the phone has a StrongBox secure element; a TEE-backed Android Keystore key otherwise; wraps the same items with AES-256-GCM | **IMPLEMENTED** |
| **Protection level per device** | Each phone reports Secure Enclave, StrongBox, TEE or software (simulators and emulators); stored per device, shown in the console; policy can require a minimum level | **IMPLEMENTED** |
| **Desktop OS keychain** | Protects desktop protocol state and secrets | **IMPLEMENTED** |
| **TPM 2.0 on servers** | Full-disk encryption key protection; measured boot | **RECOMMENDED** |
| **Secure Boot on servers** | Boot-chain integrity of the host | **RECOMMENDED** |
| **Encrypted server storage** | Full-disk encryption (for example LUKS with TPM) for all installation volumes | **RECOMMENDED** |
| **HSM** | Root CA keys; evaluation of OpenBao auto-unseal against customer key infrastructure | **RECOMMENDED** |
| **FIDO2 security keys** | Phishing-resistant administrator sign-in (WebAuthn, ES256 or EdDSA, user verification required), verified by the installation; required on new installations for every administrator | **IMPLEMENTED** |
| **Redundant infrastructure** | Redundant power, storage and network for the host | **RECOMMENDED** |
| **Android Key Attestation** | Proof that a phone's keys are held in StrongBox or the TEE, verified offline against vendor roots and a revocation snapshot shipped with each release | **IMPLEMENTED** |

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 32. Availability, High Availability and Disaster Recovery](32-availability-high-availability-and-disaster-recovery.md) | [Contents](../README.md) | [Next: 34. Security Control Matrix](34-security-control-matrix.md)

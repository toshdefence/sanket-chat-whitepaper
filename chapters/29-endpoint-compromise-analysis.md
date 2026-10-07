<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 29. Endpoint Compromise Analysis

End-to-end encryption does not protect plaintext on a fully compromised authorised endpoint. The endpoint is where messages are decrypted, displayed and typed; an attacker who controls it sees what the user sees.

*Table 51: Endpoint threats and SANKET mitigations*

| Threat | What the attacker can do | SANKET mitigation |
| --- | --- | --- |
| **Malware with full device control** | Read the screen, keystrokes and decrypted memory | Managed devices; minimal local plaintext; remote wipe after detection |
| **Screen capture** | Record displayed content | Screenshot and screen-recording blocking on mobile and content protection on desktop; in-chat screenshot alerts |
| **Clipboard compromise** | Read copied text | User awareness; managed-device clipboard policies |
| **Memory scraping** | Extract keys or plaintext from process memory | Keys stored under a hardware-backed wrapping key on phones; desktop keys confined to the main process; cache key zeroed on sign-out |
| **Unlocked device in another's hands** | Use the app as the user | App passcode or biometric lock; background lock timeout; duress PIN |
| **Rooted or jailbroken device** | Bypass OS protections | MDM compliance blocks non-compliant devices; SANKET detects root and jailbreak indicators, alerts administrators and can refuse use; Android Key Attestation verified offline; remote wipe and revocation |
| **Copied app storage** | Copy the app's files and keychain items to another device and open them there | Protocol store and device secrets encrypted under a non-exportable Secure Enclave, StrongBox or TEE key; items copied to another device do not decrypt |
| **Physical access to a locked device** | Forensic extraction | OS storage encryption; keys bound to this device and to first unlock, wrapped by secure hardware on phones; SQLCipher; wipe after failed unlocks; lost mode; remote wipe |
| **Malicious authorised user** | Copy, photograph or forward content they can read | Download, export and forwarding restrictions, turned off automatically for content labelled above the lowest classification rank; visible watermark (Sanket ID or name) in the desktop and mobile file viewers (configurable); audit of file events |

## 29.1 Mitigations in Combination

- **Managed devices** with MDM-enforced patching, posture checks and SANKET managed configuration (Chapter 14).
- **Secure local storage:** SQLCipher database and AES-256-GCM file cache; on phones the protocol store and device secrets encrypted under a hardware-backed key (Secure Enclave, StrongBox or TEE), with the protection level reported per device and a minimum level enforceable by policy.
- **Local lock:** passcode, biometric unlock, timeout, wipe after failures.
- **Screenshot controls:** screenshot and screen-recording blocking for high-trust deployments.
- **Revocation and wipe:** lost mode, device revocation and server-confirmed remote wipe.
- **Policy:** restrict download to device, export and forwarding; keep notification previews off.
- **Limited local data:** disappearing messages where the use case allows.

## 29.2 Desktop and Mobile

The desktop app runs the same protocol, the same message, call and broadcast semantics and the same certificate lifecycle as the mobile apps; differences between platforms lie in key storage, not in behaviour. Each device's key protection level is reported to administrators, policy can require a minimum level, and desktop use can be limited to managed machines where the endpoint threat model requires it.

> [!NOTE]
> **Note - Residual endpoint risk**
>
> A device that is compromised before it is revoked exposes everything it could decrypt during the compromise. Revocation and wipe protect against continued exposure and against an attacker who obtains the device later; they do not undo exposure that has already happened. Plaintext that a user has legitimately exported, photographed or forwarded is outside SANKET's control.

---

[Previous: 28. Security Impact of Server Compromise](28-security-impact-of-server-compromise.md) | [Contents](../README.md) | [Next: 30. Software Supply Chain and Secure Updates](30-software-supply-chain-and-secure-updates.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix G - Security Hardening Checklist

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

☐  Hardened, minimal host OS with Secure Boot and TPM; full-disk encryption on all volumes.

☐  SELinux or AppArmor enforcing; host firewall allowing only HTTPS and media ports inbound.

☐  Default-deny egress with explicit exceptions.

☐  Bastion-only, key-based SSH with MFA; administrator console restricted to the management network.

☐  All images pinned by digest; Edge hardening overlay applied; installation gates pass.

☐  Release public key configured; upgrades verified before load.

☐  Tenant policy: TOTP required; invitation-only or administrator-only registration; administrator device approval; member approval for groups.

☐  Security keys required for every administrator; synced passkeys refused.

☐  Device mutual TLS required; required hardware protection level and device attestation enforced on managed fleets.

☐  Minimum app versions set and enforced per platform.

☐  Classification labels defined; encrypted broadcasts used for sensitive announcements and notices only for non-sensitive content; large-group reaction counts left off.

☐  Usage analytics switched off, or locked off at provisioning, where policy requires.

☐  Tenant policy: notification previews and sender names off; download to device off; forwarding off; link previews off.

☐  App passcode required and wipe-after-failures set; screenshot blocking enabled.

☐  Vault disabled if identity keys must never leave devices.

☐  Audit export to SIEM enabled; audit chain verified on a schedule.

☐  Retention windows set to the minimum consistent with policy.

☐  Managed devices enrolled in MDM with SANKET managed configuration.

☐  Certificate pinning configured on clients.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: Appendix F - Air-Gap Deployment Checklist](appendix-f-air-gap-deployment-checklist.md) | [Contents](../README.md) | [Next: Appendix H - Network and Protocol Categories](appendix-h-network-and-protocol-categories.md)

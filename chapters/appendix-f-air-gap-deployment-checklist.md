<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# Appendix F - Air-Gap Deployment Checklist

☐  Enclave network has no route to the internet, verified by testing egress from the installation host.

☐  Internal CA issues the edge certificate; clients trust the internal CA.

☐  Internal DNS resolves all installation host names.

☐  Internal authoritative time source configured on servers and managed devices.

☐  Licence issued for air-gapped deployment and offline operation; trust bundle installed.

☐  Release public key obtained out of band and configured; verification succeeds.

☐  Third-party base images pre-staged from a verified source.

☐  Media server node address set explicitly or internal STUN configured; TURN relay enabled only if needed inside the enclave.

☐  Android attestation verified against the roots and revocation snapshot shipped in the release; no online attestation check.

☐  Identity provider and LDAP directory, if used, hosted inside the enclave.

☐  Device client-certificate revocation list published to the edge.

☐  Apple push disabled; iOS behaviour without background wake-up accepted by users.

☐  SMS disabled; email relay internal or disabled.

☐  Android devices registered with the self-hosted push relay.

☐  Desktop clients built with the installation's host allowlist.

☐  Syslog configured to the internal SIEM with the internal CA pinned.

☐  Controlled media transfer procedure documented, with scanning and custody records.

☐  Encrypted backups written to separate media inside the enclave; restore tested.

☐  OpenBao unseal shares and backup passphrase held by separate custodians.

---

[Previous: Appendix E - Deployment Reference Architectures](appendix-e-deployment-reference-architectures.md) | [Contents](../README.md) | [Next: Appendix G - Security Hardening Checklist](appendix-g-security-hardening-checklist.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 28. Security Impact of Server Compromise

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

A server compromise is serious in any system. The purpose of end-to-end encryption is to change what it means: an attacker who controls SANKET's servers should gain metadata and the ability to disrupt service, but not message or file content.

Tosh Defence analyses the compromise of every server-side component, from the messaging service and its data stores through the edge, certificate and release infrastructure to the application host and the infrastructure beneath it. Across all of them, three statements hold:

1. **Past content stays confidential.** Message bodies, file contents and call media were encrypted with keys the server never held.
2. **Metadata is exposed.** The server necessarily knows who communicates with whom and when (Chapter 20).
3. **Future sessions are the attack surface, within limits.** A party controlling the server can withhold or delay messages. It cannot silently add a device to an account, because senders encrypt only for devices on the account's signed, append-only device list, and it cannot downgrade or replay an encrypted broadcast, because priority, deadline and expiry travel inside the encrypted envelope. Safety numbers, device inventories and audit make any attempt at key substitution visible, which is why those checks form part of high-trust operating procedure.

Recovery from any server compromise follows one pattern: rebuild from a verified, signed release on trusted media, rotate server-side keys and secrets, force re-authentication, and review the audit record against the independent copy held outside the installation. Content confidentiality never depends on a server component remaining uncompromised; what a compromise can expose is the metadata the server already handles (Chapter 20) and the ability to disrupt service while the attacker remains in control.

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 27. Administrative Security](27-administrative-security.md) | [Contents](../README.md) | [Next: 29. Endpoint Compromise Analysis](29-endpoint-compromise-analysis.md)

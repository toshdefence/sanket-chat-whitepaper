<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 24. Offline-First Operation

SANKET is designed for intermittent connectivity. Clients keep working with local data while disconnected, and the server holds encrypted messages until devices return.

> [!IMPORTANT]
> **Key Point - Offline-capable is not peer-to-peer**
>
> Offline-capable means that a device can compose and read while disconnected and that messages are stored and forwarded when connectivity returns. It does not mean that two devices can communicate with each other without a SANKET installation between them. SANKET has no peer-to-peer or mesh mode in this release.

## 24.1 Store and Forward

*Table 44: Behaviour under intermittent connectivity*

| Aspect | Behaviour |
| --- | --- |
| **Outbound while offline** | Mobile and desktop hold composed text messages, attachments and voice notes in an encrypted local outbox (mobile: inside the SQLCipher database; desktop: in its encrypted secure storage) for up to seven days and send them on reconnection. An attachment or voice note is kept only as a copy encrypted with the local cache key (desktop: AES-256-GCM in the main process file cache, never handed to the renderer), never in clear, and is uploaded again if its upload had not finished; one the server refuses stays in the conversation as failed, with Retry and Discard, also after the app restarts. Mobile and desktop decide which failures to retry by the same rule (Chapter 17) |
| **Inbound while offline** | Per-device ciphertext waits on the server until the device fetches it |
| **Identity changes while offline** | Notices that a contact's identity key changed are kept by the server and replayed to each device that was offline when the key changed, so no device misses the warning |
| **Encrypted broadcasts** | A recipient device that is offline receives its encrypted copy when it reconnects, until the broadcast expires. A device provisioned while a broadcast is still live is queued, and the sender desktop encrypts the broadcast for it |
| **Reconnection** | Mobile WebSocket reconnects with jittered back-off; sync cursors fetch only what is new |
| **Ordering** | Messages carry server timestamps; the client orders by them |
| **Deduplication** | Idempotency keys make retries safe; libsignal rejects duplicate ciphertext |
| **Stale sessions** | Repeated decryption failures on recent messages trigger a controlled session reset and a fresh handshake |
| **Expired credentials** | Access tokens refresh on reconnection, tolerating lost responses; revoked or lost devices are refused |
| **Clock drift** | TOTP tolerates small clock drift; licence time is protected by rollback detection; servers should use an internal time source |
| **Message expiry** | Disappearing messages are deleted on the server at expiry and on devices when they next run the retention check |
| **Queue protection** | Server queues hold only ciphertext; local outboxes are inside the encrypted database, and a queued file only as an encrypted copy that is deleted once it is sent, dropped or discarded |
| **Queue size** | Per-user send rates and fan-out caps bound queue growth; delivered rows are tombstoned |
| **Low bandwidth** | A narrowband call profile reduces audio and video bit rates for constrained links |

---

[Previous: 23. Air-Gapped Deployment](23-air-gapped-deployment.md) | [Contents](../README.md) | [Next: 25. Network Architecture and Segmentation](25-network-architecture-and-segmentation.md)

<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 45. Security and Usability Trade-offs

Every strong security property costs something. Stating the costs openly allows customers to choose policy deliberately.

*Table 71: Principal trade-offs and how customers choose*

| Trade-off | What is gained | What is lost | Customer choice |
| --- | --- | --- | --- |
| **End-to-end encryption vs server-side search** | Server cannot read content | No server-side full-text search or content supervision | Fixed: search is on-device only |
| **Metadata minimisation vs auditability** | Less data to leak | Less forensic detail | Retention windows; audit export |
| **Message expiry vs record retention** | Less exposure on lost devices | Records disappear | Allow, force or disable disappearing messages |
| **Air gap vs mobile push** | No external path | No background wake-up on iOS | Deployment mode |
| **Strong device trust vs friction** | Rogue devices blocked | Approval delays; caps | Approval mode; device caps |
| **Confidentiality vs data-loss prevention** | Content opaque to servers | No server-side DLP inspection | Endpoint DLP on managed devices; download and export policy |
| **Offline access vs immediate revocation** | Work continues offline | A device offline at revocation keeps its local data until it reconnects or is wiped | Lost mode, wipe, disappearing messages |
| **Screenshot blocking vs usability** | Deters capture | Users cannot capture legitimately | Configurable; detection alerts |
| **Identity vault vs keys-never-leave-device** | Recovery after loss | Encrypted identity key on server | Enable or disable vault |
| **TOTP SHA-256 vs authenticator choice** | Stronger algorithm | Some popular apps incompatible | Fixed: SHA-256 only |
| **Pairwise group fan-out vs efficiency** | Per-device forward secrecy; simple removal | Bandwidth grows with group size | Group size limit |
| **Encrypted broadcasts vs scheduling and convenience** | The server cannot read, downgrade, move or replay a broadcast | No scheduling; sending only from an approved desktop; fan-out time grows with recipient devices | Encrypted broadcasts for sensitive content; notice broadcasts for scheduled, non-sensitive announcements |
| **Encrypted reactions vs server-side counts** | The server never learns who reacted with which emoji | Counts are computed on each device | For groups of 500 or more active members, an option (off by default) lets the server count reactions without the emoji |
| **Security keys vs administrator convenience** | Phishing-resistant administrator sign-in | Keys to issue, carry and replace; synced passkeys refused by default | Configurable per installation; required on new installations |
| **Classification handling vs workflow** | Higher-ranked content cannot be forwarded, exported or downloaded | Users cannot move that content out of the app, even legitimately | Label names and ranks; default label |
| **Device mutual TLS vs operational simplicity** | Only enrolled devices complete a TLS connection to the API | A certificate lifecycle and revocation list to operate | Configurable per tenant |
| **Asynchronous second site vs zero data loss** | Writes are not tied to the inter-site link | Writes since the last replicated point can be lost when a site fails | Single-host or high-availability profile; drills record the measured figures |
| **Usage analytics vs data minimisation** | First-party operational insight | Pseudonymous usage counts held on the installation | Switch off; installation lock at provisioning; delete collected analytics |

An external camera can always photograph a screen. No screenshot control changes that, and SANKET does not suggest otherwise.

---

[Previous: 44. Privacy Architecture](44-privacy-architecture.md) | [Contents](../README.md) | [Next: 46. What SANKET Does Not Claim](46-what-sanket-does-not-claim.md)

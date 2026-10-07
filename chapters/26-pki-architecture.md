<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 26. PKI Architecture

SANKET uses three distinct trust systems, and it is important not to confuse them: the customer's X.509 PKI for transport, libsignal identity keys for messaging, and Tosh Defence's signing keys for licences and releases.

![PKI hierarchy](images/pki.png)

*Figure 15: PKI hierarchy*

## 26.1 Customer Transport PKI

*Table 47: Transport PKI elements*

| Element | Role | Status |
| --- | --- | --- |
| **Offline root CA** | Anchors the customer's trust; kept offline, ideally in an HSM, used only to sign intermediates | **RECOMMENDED** |
| **Issuing intermediate CA** | Issues server and service certificates | **RECOMMENDED** |
| **Edge server certificate** | Authenticates the installation to clients; installed in import mode | **IMPLEMENTED** |
| **SIEM / syslog server certificate** | Authenticates the customer SIEM to SANKET; SANKET pins the configured CA | **IMPLEMENTED** |
| **Device certificate CA** | An OpenBao PKI mount in the installation, chained to the customer CA; its signing key stays in OpenBao; a role fixes the key type, the names allowed (no client-chosen names) and the lifetime | **IMPLEMENTED** |
| **Device client certificates** | One per approved mobile and desktop device: ECDSA P-256 key generated on the device, CSR built on the device, opaque random name with no user or device identifier, short lifetime, re-minted before expiry; verified by the edge under tenant policy | **IMPLEMENTED** |
| **Device certificate revocation** | OpenBao certificate revocation list loaded by the edge; certificates deleted on the device on revoke, lost mode and wipe | **IMPLEMENTED** |
| **Administrator certificates** | Client certificates for administrator access, applied at the customer's reverse proxy (administrators authenticate with FIDO2 security keys in the installation itself) | **DEPLOYMENT-DEPENDENT** |
| **Revocation (CRL / OCSP)** | Revocation status of server certificates | **DEPLOYMENT-DEPENDENT** |

### 26.1.1 Lifecycle

- **Issuance:** certificates are issued by the customer's CA process; the private key is generated on the edge host or in the CA workflow and installed with restrictive permissions.
- **Rotation:** replace the certificate before expiry; the edge reloads without changing client trust as long as the chain is unchanged.
- **Expiry monitoring:** track certificate expiry in the customer's monitoring.
- **Revocation in disconnected networks:** clients in an enclave cannot reach public revocation services. Use short certificate lifetimes, an internal CRL distribution point or OCSP responder, and certificate pinning on desktop and mobile so that a substituted certificate is rejected.[^60][^61]
- **CA backup:** hold the root CA key and its backups in separate secure locations under dual control.

## 26.2 Client Trust in the Edge

Clients trust the edge through the platform trust store (with the customer CA installed on managed devices) or a CA bundled in a customer-specific build, and pin the edge certificate on mobile and desktop wherever the deployment configures pins. Mobile pins are enforced natively, as a current and a backup pin compiled into the customer build and never accepted from the server. Rotating the edge certificate therefore means issuing the new certificate on the backup key, or shipping a build with the new pins before the change. The edge certificate must also name the TURN host when the media relay runs on TCP 443, because the edge terminates that TLS hop too.

Trust also runs the other way. Under tenant policy, each device presents its own client certificate to the edge on the REST API, the chat WebSocket and the media signalling connection, on iOS, Android and desktop; a device presents it only to the tenant's own hosts. The certificate name is an opaque random handle that never identifies the user; the mapping from handle to device exists only in the installation's database. The edge passes the verified fingerprint to the API, which binds it to the device record (Chapter 16).

## 26.3 Messaging Identity Is Not X.509

libsignal identity keys are not certificates and are not issued by any CA. Their authenticity rests on trust on first use, signatures over prekeys, server verification of those signatures, and out-of-band comparison of safety numbers. Which devices belong to an account is established by signed device lists: each account's list is signed with its own libsignal account list key, kept by the installation in an append-only log and verified by every sender before it encrypts (Chapter 13). This is intentional: a compromise of the transport PKI, including the device certificate CA, must not let anyone read messages or add a device that senders will encrypt to.

## 26.4 Vendor Signing Keys

Tosh Defence signs licences and release manifests with Ed25519 keys held as non-exportable keys in its own OpenBao. Installations hold only the public keys: a licence trust bundle, itself signed by a root key and listing active, previous and revoked licence keys, and a release verification key configured in the installation environment. A bundle cannot introduce its own trust anchor. Each release manifest also carries a monotonic sequence number and an expiry, and connected upgrades check a short-lived timestamp statement from the vendor registry signed with a separate key (Chapter 30). These signatures are classical Ed25519; hybrid Ed25519 plus ML-DSA signatures are on the roadmap and are held until an ML-DSA implementation has a published independent audit or an issued CMVP certificate (Chapter 39).

---

[^60]: D. Cooper et al., "Internet X.509 Public Key Infrastructure Certificate and CRL Profile", IETF RFC 5280, May 2008.
[^61]: S. Santesson et al., "X.509 Internet PKI Online Certificate Status Protocol - OCSP", IETF RFC 6960, June 2013.

---

[Previous: 25. Network Architecture and Segmentation](25-network-architecture-and-segmentation.md) | [Contents](../README.md) | [Next: 27. Administrative Security](27-administrative-security.md)

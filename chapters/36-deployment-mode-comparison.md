<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 36. Deployment Mode Comparison

*Part of the [SANKET Technical Security & Architecture Whitepaper](../README.md) by [Tosh Defence](../ABOUT.md#about-tosh-defence), public edition 2.0.*

SANKET is deployed in three modes. They are not ranked: each suits a different combination of security requirement, connectivity and operating capacity. In every mode the installation is dedicated to one customer, and every mode can run either the single-host Edge profile or the multi-host high-availability profile, with an optional second site for disaster recovery (Chapter 32). The topology is chosen per installation in its environment file and role overlays; the files shipped in a release are the same for every customer.

*Table 59: Deployment modes compared*

| Attribute | Sovereign hosted | Customer on-premise | Air-gapped |
| --- | --- | --- | --- |
| **Infrastructure ownership** | Dedicated installation in a sovereign data centre chosen by the customer, operated under contract | Customer's own servers and data centre | Customer's own servers in an isolated enclave |
| **Topology** | Single host, or multi-host high availability with an optional second site | Same | Same, with every host and the second site inside the enclave |
| **Network dependency** | Internet-reachable edge: one public TCP 443 (HTTPS and TURN) plus the media UDP port | Internet-reachable or private network, as the customer chooses | None outside the enclave; TURN off |
| **PKI** | ACME DNS-01 certificates or customer CA | Customer CA (recommended) or ACME | Customer internal CA |
| **External dependencies** | Optional Apple push; optional connected upgrades from Tosh Defence; no public STUN | Same, customer-controlled | None |
| **Notifications** | Android self-hosted push; iOS through Apple push | Same | Android self-hosted push; iOS without background wake-up |
| **Updates** | Offline package or optional connected upgrade, installed on approval | Same | Offline package on controlled media |
| **Administration** | Customer administrators; operator under contract for the host | Customer | Customer |
| **Data storage** | In the chosen sovereign data centre | On customer premises | Inside the enclave |
| **Logging** | Installation plus customer SIEM over TLS | Same | Internal SIEM |
| **Identity** | Administrator, invitation or SCIM provisioning; optional sign-in through the customer's own OIDC, SAML or LDAP directory | Same; SCIM and federation from the internal directory | Same; SCIM and federation from the enclave directory |
| **Backup** | Encrypted archives to customer-designated storage | Customer backup infrastructure | Offline media inside the enclave |
| **Licence** | Signed licence, verified locally | Same | Offline licence and offline renewal bundles |
| **Operational control** | Shared between customer and contracted operator | Customer | Customer |
| **Sovereignty level** | High: jurisdiction and data stay sovereign; the operator is a party to trust | Very high | Highest available |
| **Ideal use case** | Organisations needing fast deployment without their own data centre operations | Organisations with their own infrastructure and security operations | Classified-adjacent, defence and critical environments where any external path is unacceptable |

---

### About SANKET

SANKET (संकेत) by [Tosh Defence](../ABOUT.md#about-tosh-defence) is a sovereign secure communications platform for defence, government and other high-trust organisations: end-to-end encrypted messaging, file exchange, voice and video calls and priority broadcast, built on Signal's official libsignal library and AES-256, self-hosted on infrastructure the organisation owns, including air-gapped networks. [About SANKET and Tosh Defence](../ABOUT.md) | [Website](https://www.sanket.chat) | [Full whitepaper](../README.md)

---

[Previous: 35. Defensive Attack Scenarios](35-defensive-attack-scenarios.md) | [Contents](../README.md) | [Next: 37. Feature Catalogue](37-feature-catalogue.md)

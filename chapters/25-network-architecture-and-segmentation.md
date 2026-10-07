<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 25. Network Architecture and Segmentation

A SANKET installation exposes a deliberately small network surface. This chapter describes what the released profile exposes, how internal traffic flows, and the reference architectures Tosh Defence recommends for different environments.

## 25.1 Exposed Surface of the Released Profile

*Table 45: Network exposure of an installation*

| Listener | Protocol | Purpose | Exposure |
| --- | --- | --- | --- |
| **Edge** | TCP 443 | HTTPS: API, WebSocket, administrator console, media signalling, push relay. TURN over TLS: media relay for networks that allow only TCP 443 | Published (the only public TCP port) |
| **Media** | UDP (single multiplexed port) | Voice and video media to the SFU; the primary, lowest-latency path | Published |
| **Optional media fallbacks** | TCP | Media fallback where UDP is blocked, enabled by the customer | Optional |
| **Application and data services** | Internal | API, database, cache, object storage, key service | Not published; no host ports |

**One public TCP port.** The edge separates HTTPS and TURN on TCP 443 without decrypting the connection, and carries the real client address to the services behind it, so rate limits, the administrator network allowlist and audit records judge the real client; forwarding headers sent from outside are never believed. The TURN hop is terminated by the edge with TLS 1.3 and TLS_AES_256_GCM_SHA384 only, the media server holds no certificate key, and the relay reaches only the installation's own media address, so it is not an open relay. Calls over TCP 443 alone have been demonstrated on iOS, Android and desktop.

Internally, the key service sits on a dedicated internal-only network reachable only from the components that need it, and the other services share an isolated application network. Inter-service traffic never leaves the host in the single-host profile. In the high-availability profile, database replication and the OpenBao cluster use TLS between hosts; place inter-host links on a dedicated network.

![Network segmentation reference architecture](images/segmentation.png)

*Figure 14: Network segmentation reference architecture*

## 25.2 Reference Architectures

### 25.2.1 Single-site deployment

One hardened host (minimum eight virtual CPUs and 16 GB of memory) runs the complete installation behind the customer's perimeter firewall, which forwards TCP 443 and the media UDP port. Suitable for a unit, department or site of moderate size. This is the single-host Edge profile, released and supported.

### 25.2.2 DMZ-segmented deployment

The perimeter firewall terminates external connectivity in a DMZ that contains only the edge function; application and data services sit on an internal network reachable only from the edge. With the released Compose profile this is approximated by placing the host in the DMZ and firewalling all ports except HTTPS and media, with administrative access only from a management network through a bastion. Splitting the edge onto a separate host is a deployment option with a customer-operated reverse proxy forwarding to the installation.

### 25.2.3 Air-gapped security enclave

As in Chapter 23: no internet route; internal CA, DNS and time; offline releases; self-hosted push for Android.

### 25.2.4 Highly available on-premise deployment

The high-availability profile runs the same services across several hosts on Docker Compose. Two edge hosts sit behind a shared address or the customer's load balancer, each holding the TLS certificates and the device CA and revocation list, with the client address preserved end to end. Behind them run N+1 API and WebSocket nodes, Redis cache and state instances with Sentinel, PostgreSQL with streaming replication and fenced failover, distributed MinIO, a three-node OpenBao cluster, several media-server nodes and the push relay on more than one host. Each host's role and every peer address are set in the installation's environment file and role overlays; the files shipped in a release stay identical for every customer. Chapter 32 describes the topology and its failure behaviour. Status: IMPLEMENTED.

### 25.2.5 Multi-site and disaster-recovery deployment

A primary site with an asynchronously replicated second site: database write-ahead logs archived to the second site, object storage replicated between sites, and encrypted OpenBao snapshots shipped to it. Drill tooling and a runbook cover declaring a disaster, fencing the primary, promoting the second site, switching names and failing back. The reference topology is designed for a recovery point objective of 5 minutes and a recovery time objective of 1 hour; these are design targets, and each deployment's disaster-recovery drill records the figures it actually measured (Chapter 32). Independent installations per site remain an alternative where sites must not share data. Status: IMPLEMENTED.

### 25.2.6 Kubernetes-based deployment

Draft Helm charts exist, but the built high-availability profile is multi-host Docker Compose. Status: ROADMAP; not supported for production in the described release.

## 25.3 Network and Protocol Categories

*Table 46: Flows to permit at customer firewalls*

| Flow | From | To | Notes |
| --- | --- | --- | --- |
| **Client API and WebSocket** | User access zone | Edge | HTTPS only |
| **Client media** | User access zone | Edge host media port | UDP media port preferred; ICE over TCP optional; TURN over TLS on TCP 443 for networks that allow nothing else |
| **Administration (console)** | Management zone | Edge | Restrict by source address at the firewall |
| **Host administration** | Bastion | Installation host | Key-based SSH with MFA on the bastion |
| **Syslog** | Installation | SIEM | TLS, outbound only |
| **Webhooks** | Installation | Customer endpoints | HTTPS, outbound only, to configured hosts |
| **Identity provider (optional)** | Installation | Customer identity provider | OIDC or SAML over HTTPS, LDAP over ldaps or StartTLS; the customer's own provider only |
| **Site replication (high availability)** | Primary site | Second site | Database log archive and replication, object-storage replication, encrypted OpenBao snapshots |
| **Connected upgrade (optional)** | Installation | Tosh Defence release registry | HTTPS, outbound only; connected-mode licences only |
| **Backups** | Installation | Backup zone | Encrypted archives |
| **Apple push (connected, optional)** | Installation | Apple push service | Only if iOS background wake-up is required |
| **Everything else** | Installation | Anywhere | Deny by default |

---

[Previous: 24. Offline-First Operation](24-offline-first-operation.md) | [Contents](../README.md) | [Next: 26. PKI Architecture](26-pki-architecture.md)

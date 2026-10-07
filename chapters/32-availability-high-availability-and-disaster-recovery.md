<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 32. Availability, High Availability and Disaster Recovery

Communications matter most when other systems are under stress. This chapter covers denial-of-service resistance, the resilience of the single-host profile, the multi-host high-availability profile with second-site recovery, behaviour when an identity provider is unavailable, and backup and recovery.

## 32.1 Denial of Service and Resource Exhaustion

*Table 55: Availability controls*

| Threat | Controls |
| --- | --- |
| **External request floods** | Edge request-rate limits; Redis-backed global limit per user and device or address; adaptive tightening on error spikes; block manager; customer upstream filtering |
| **Authentication abuse** | Compound per-address and per-account limits; lockout; integration failure throttles |
| **Expensive operations** | Fan-out caps, file size and duration limits, prekey fetch limits, call rate limits, broadcast caps, metadata size limit |
| **Internal abuse by a user** | Per-user message send rate; typing throttle; group size limit |
| **Queue growth** | Tombstoning and retention jobs; Redis memory cap set per installation; state keys (sessions, single-use tokens, restore challenges, call rooms) held on a separate instance that never evicts, so cache pressure cannot remove them |
| **Resource exhaustion on the host** | Per-service CPU and memory limits in the Edge profile; log rotation |
| **Degraded operation** | Administrator-triggered protective degraded mode; maintenance mode |
| **Media floods** | Room tokens required; one call per device; participant limits; TURN allocations only with media-server-issued credentials; per-address connection limit on TCP 443 |
| **Analytics load** | Analytics never sits on a user path: bounded client queues and measured budgets; if the processor stops, chat and calls are unaffected and its lag is alerted |

A single-host installation cannot absorb volumetric attacks larger than the customer's network capacity. Deployments on hostile networks should front the edge with the customer's own DDoS protection, or run on private networks.

## 32.2 Resilience of the Single-Host Profile

The Edge profile is a single host by design. Services restart automatically and are health-checked; clients queue outbound messages, attachments and voice notes in an encrypted outbox and reconnect; the server holds ciphertext for offline devices. Redis runs as two instances on the host as well: an evictable cache and a persistent state instance that never evicts, with the memory cap set per installation. There is no in-profile redundancy: a host failure interrupts service until the host is repaired or the installation is restored on a standby host. Customers that need redundancy use the high-availability profile below. Tosh Defence does not publish an availability SLA figure for self-hosted installations, because availability depends on the customer's infrastructure and operations.

## 32.3 High-Availability Reference Topology

Status: IMPLEMENTED. The high-availability profile runs on multi-host Docker Compose (Kubernetes packaging is on the roadmap). Its reference topology comprises:

- two edge hosts behind a shared address or the customer's load balancer, each with the TLS certificates and the device CA and revocation list, with the client address preserved end to end;
- N+1 API and WebSocket nodes, with real-time delivery fanned out across nodes through Redis publish and subscribe, and exactly one elected runner for each singleton job, so no scheduled job runs twice;
- Redis split into an evictable cache and a persistent state instance that never evicts, with Sentinel failover;
- PostgreSQL streaming replication with fenced failover, so a former primary can never accept writes, and write-ahead-log archiving to the second site, for both the main and the threat-processor databases;
- distributed MinIO in each site with site replication between sites, keeping AES-256-GCM encryption at rest;
- a three-node OpenBao raft cluster with TLS between nodes, a per-node unseal ceremony under the same M-of-N custody, and encrypted snapshots shipped to the second site;
- several media-server nodes routed through the state Redis, each with the same AES-256 SRTP policy;
- the push relay on more than one host;
- a maintenance mode for planned work.

Host roles and every peer address are set in the installation's environment file and role overlays, so the files shipped in each release stay identical for every customer. Installation, upgrade, backup and restore are orchestrated across the hosts with health gates between steps, and every host performs the release signature, expiry, sequence and downgrade checks itself (Chapter 30). Backups are taken from a replica.

## 32.4 Behaviour When an Identity Provider Is Unavailable

Federated sign-in is optional and only ever uses the customer's own identity provider (Chapter 13). Its outage is contained by design:

- **Administrators.** At least one local administrator, signing in with a password and a second factor, always remains: the installation refuses any configuration that would leave none, and federation never disables local sign-in. When the provider cannot be reached, the console directs administrators to a local account.
- **End users.** The directory is consulted only at enrolment, device restore and new-device sign-in. Everyday sign-in on an existing device and all messaging and calls never contact it, so field devices are unaffected by an outage; enrolment and restore wait until the directory is back, and any exception is an audited owner decision.
- **Monitoring.** The administrator monitors show the result of the latest provider check.

## 32.5 Backup

*Table 56: Released backup capability*

| Aspect | Released behaviour |
| --- | --- |
| **Scope** | Main and threat-processor database dumps (taken from a replica in the high-availability profile); attachment bucket; OpenBao data and unseal material; installation configuration |
| **Not included** | Vault bucket and Redis (short-lived state) |
| **Encryption** | Each component is a separate artefact encrypted with its own AES-256-GCM key. The recovery artefact (OpenBao data, unseal material, configuration) has its key wrapped under a key derived from an operator passphrase with scrypt, because OpenBao itself is inside it; every other artefact has its key wrapped by the installation's OpenBao, so it cannot be read without that OpenBao |
| **Integrity** | A manifest listing every artefact with its size and SHA-256 is signed with a non-exportable Ed25519 key in the installation's OpenBao. The restore tool opens the recovery artefact with the passphrase, checks that the signing key sealed there is the one the manifest names, verifies the signature and every artefact, and refuses before changing anything if any of them differs |
| **Retention** | 14 days with a minimum of seven archives, pruned only after a successful run |
| **Restore** | Restore script with post-restore health check. The data keys are unwrapped by the restored OpenBao only after it is unsealed by its key custodians, and any recovery credential the custodians issue is short-lived and restore-scoped, so no long-lived token has to be kept for recovery |
| **Scheduling and offline copies** | Operator-scheduled; alternating offline media recommended in the operations runbook |
| **Upgrade restore point** | Each upgrade takes a protected restore point of the running service images and configuration before it switches anything and, where the switch changes the database schema, of the databases too, each dump encrypted with AES-256-GCM under its own key wrapped by the installation's OpenBao and never written in clear |
| **Upgrade switch** | An upgrade whose database changes the previous version can still run with switches without maintenance mode: the new API starts beside the old one, which stops only once the new one is healthy, so requests continue to be answered and chat connections reconnect within seconds; a new API that does not become healthy is removed and the old one keeps serving. Other releases switch inside a planned maintenance window. The other services the release changes are restarted after the API, and each is awaited until healthy |
| **Scheduled database backups** | Backup policies run by the platform scheduler on their own schedule: each database dump is encrypted with its own AES-256-GCM key, that key is wrapped by the installation's OpenBao (non-exportable), and the run's manifest is signed with an installation Ed25519 key and stored next to every copy. A restore verifies the signature, then the checksum, before it uses a copy, and refuses one signed by any other key |
| **Backup destinations** | Local or network-attached storage, S3-compatible object storage, or SFTP, with an optional second copy; no foreign cloud destination exists. A policy aimed at anything else is paused and raises an administrator alert |

> [!TIP]
> **Recommended Practice - Protect the backup passphrase and the backup keys**
>
> The full-installation archive contains the installation's own secrets, so its passphrase protects the whole installation: keep the passphrase in separate custody from the media. Scheduled backups need no passphrase, but they can be decrypted and verified only by the installation's own OpenBao, so the OpenBao unseal material and its recovery procedure are what must be held in separate custody. Sources other than the databases (attachment bucket, OpenBao data, configuration) are covered by the full-installation archive.

## 32.6 Disaster Recovery Methodology

The high-availability reference topology, with an asynchronous second site, is designed for a recovery point objective (RPO) of 5 minutes and a recovery time objective (RTO) of 1 hour. These are design targets, not measured claims: each deployment's disaster-recovery drill records the figures it actually achieved, and those recorded figures, not the targets, are what an installation can rely on. An RPO of zero with an RTO of 15 minutes would need synchronous replication and a third site or witness; it is available as a separately priced option and is not part of the built profile.

The drill tooling writes markers at the primary site, fences it, promotes the second site, switches the service names and later fails back. The achieved RPO is measured as the gap between the last marker acknowledged at the primary site and the last marker present at the second site; the achieved RTO runs from the declaration of the disaster to the first successful message and call on iOS, Android and desktop.

For single-host installations, and for any installation's backup-based recovery, RPO and RTO are set by the customer's backup frequency, media handling and standby capacity. The recommended methodology:

1. **Classify** the installation's criticality and set target RPO (maximum tolerable data loss) and RTO (maximum tolerable outage).[^66]
2. **Schedule** backups at an interval no longer than the RPO, and replicate encrypted archives to a recovery site or offline media.
3. **Prepare** a standby host with the same release and pre-staged images.
4. **Separate custody** of the backup passphrase, OpenBao unseal shares and CA keys.
5. **Test** restoration and, in the high-availability profile, site failover on a schedule, measuring the achieved recovery point and recovery time, and record each test.
6. **Recover keys:** installation keys come back with the backup; end-user message keys are not recoverable by design, and users re-establish sessions after restoration.
7. **Recover PKI:** re-issue the edge certificate from the customer CA if the original key is unavailable.

Customers should validate restoration on their own infrastructure as part of acceptance and at a regular interval thereafter.

---

[^66]: M. Swanson et al., "Contingency Planning Guide for Federal Information Systems", NIST SP 800-34 Rev. 1, May 2010.

---

[Previous: 31. Offline Licensing](31-offline-licensing.md) | [Contents](../README.md) | [Next: 33. Hardware Security](33-hardware-security.md)

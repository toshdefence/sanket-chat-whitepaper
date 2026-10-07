<!-- SANKET Technical Security & Architecture Whitepaper, public edition, version 2.0. Generated from docs/whitepaper; do not edit by hand. -->

# 22. Monitoring, Threat Detection and Observability

SANKET's detection capabilities are deterministic and rule-based. This chapter separates the protections that are always active from the optional components, and is explicit that the platform contains no machine-learning detection.

## 22.1 Always-Active Protections

*Table 40: Built-in detection and response*

| Control | Behaviour | Type |
| --- | --- | --- |
| **Account lockout** | Locks after configurable failures; alerts administrators | Deterministic |
| **Compound and global rate limits** | Per IP and account, per user and device, at edge and API | Deterministic |
| **Adaptive rate limiting** | Tightens limits in steps as the error rate rises, with gradual recovery | Statistical (simple) |
| **Refresh-token reuse detection** | Revokes the device; critical event | Deterministic |
| **Block manager** | Hard or soft blocks of users, devices, addresses and networks | Administrator action |
| **Protective degraded mode** | Administrator-triggered degraded mode that throttles non-essential traffic | Administrator action |
| **Integration abuse limits** | Failure throttles and change caps with critical alerts | Deterministic |
| **Lost-device contact, duress, locate** | Critical and high-severity events and webhooks | Deterministic |
| **Administrator security keys** | Anomalous security-key use is refused and raises a critical alert to the other owners | Deterministic |
| **Alerts feed** | Real-time administrator view of security events from the audit log | Deterministic |

## 22.2 Optional Stream Processor

An optional stream processor, deployed as a separate service with its own database, correlates security events over time and raises alerts that appear in the administrator console in real time, naming end users by Sanket ID. Most customers combine the built-in protections above with correlation in their own SIEM.

## 22.3 What Is Not Claimed

SANKET's detection is deterministic and rule-based; no machine-learning detection is claimed.

For most high-trust customers the right model is to forward SANKET's audit stream to their own security operations platform and correlate it there with network, endpoint and identity telemetry.

## 22.4 Usage Analytics on the Installation

Usage analytics (active users, messages and calls per day, app versions and platforms) are first-party: they are aggregated by a processor that ships with every release and runs on the installation itself. Clients and the server add content-free, pseudonymous events to a capped stream inside the installation; the processor rolls them up into counters in the installation's own database, in day buckets in the organisation's time zone, which the console's Analytics pages read. Only counts are kept, never content. Nothing leaves the installation and no third-party analytics SDK or service is used. The processor is signed, verified and upgraded like every other service, runs with a read-only file system and no added privileges, and publishes no port.

*Table 41: Control of usage analytics*

| Control | Behaviour | Status |
| --- | --- | --- |
| **Organisation switch** | On by default. An administrator can switch analytics off (or on again) on the console's analytics settings page; the change needs a fresh TOTP step-up, is recorded as a high-severity audit event with the old and new value and raises an administrator alert | **CONFIGURABLE** |
| **Installation lock** | Set at provisioning for customers who must not collect usage data at all (for example intelligence organisations): analytics is then always off, and no console action, vendor tool or stored setting can turn it on; the console shows that the installation has turned it off | **OPTIONAL** |
| **When off** | Mobile and desktop collect nothing and discard events they had queued; the server refuses new events and stores nothing; the processor does no work; events already waiting in the stream are cleared at once, so nothing queued before the change is ever processed; the Analytics pages say that analytics is off | **IMPLEMENTED** |
| **Delete all collected analytics** | Empties the event stream and every rollup, after a fresh TOTP step-up and a typed confirmation, and is audited; it never touches audit logs or device records | **IMPLEMENTED** |
| **Retention** | Rollups are kept for a configurable period (default 400 days, minimum 30 days) | **CONFIGURABLE** |
| **Out of scope of the switch** | Security audit logs, and the app version, platform and build that each device reports for update enforcement, are operational records rather than analytics and stay on | **BY DESIGN** |

Analytics never sits on a user's path. On the devices an event is only appended to a bounded in-memory queue, never awaited on a send, receive, decryption, call or rendering path, and sent in one batched request on an interval, deferred during calls and start-up; on the server, accepting events is a single capped append to the stream with no database write, and the processor runs in its own container under fixed resource limits, so it may fall behind but never slows the API. Written performance budgets, each enforced by a test, show that message sending, delivery and calls are unaffected with analytics on.

The processor's health is visible and alerted. Every Analytics page reports whether the processor is running, when the newest rollup was made and how many events are waiting, and shows a banner instead of empty charts when the processor is down or has stalled. A scheduled monitor raises an administrator alert when the backlog of unprocessed events, or the age of the oldest waiting event, passes a configurable threshold, and clears it when the processor recovers.

## 22.5 Observability Without Content Exposure

Administrators with the alerts-read permission see live operational monitors that read only counters, health probes and metadata:

*Table 42: Operational monitors*

| Monitor | What it shows |
| --- | --- |
| **Overview and synthetic checks** | Composite counters; live Redis write and read, database round trip, bucket check |
| **Infrastructure topology** | Component graph with live reachability |
| **API and WebSocket** | Request and error rates, latency percentiles, busiest routes; session, user, device and platform counts |
| **Database and storage** | Connection saturation, sizes, largest tables, long-running queries; object-store health and usage |
| **Retention and jobs** | Partition inventory, last retention run, recent scheduled jobs |
| **Broadcast and calls** | Delivery and acknowledgement progress; call activity |
| **Cryptographic keys** | Signal key inventory and one-time prekey exhaustion |
| **Licence and audit** | Plan, expiry, seats, audit chain head and verification |
| **Identity provider** | Where federated sign-in is configured, the result of the latest check of the customer's identity provider or directory |
| **Analytics processor** | Running state, newest rollup and backlog of waiting events |
| **Backup and recovery** | Backup policy, run and restore state |
| **Capacity and cost** | Scaling signals; seats, bytes, message and call volumes |

The edge records, for each TLS connection, only the time, the protocol version, the negotiated key-exchange group and the server name, with no client address, path or user, so the share of connections that use hybrid post-quantum key exchange can be counted without identifying anyone (Chapter 39).

A health endpoint reports database and licence state, and container health checks cover the principal services. A metrics endpoint is available internally and is not exposed through the edge. Tracing can be exported to a customer-operated collector. None of these channels carries message content; a container-log viewer for administrators redacts passwords, tokens and credential patterns before display and records every viewing in the audit log.

> [!NOTE]
> **Note - Container-log viewer and host privilege**
>
> The standard installation gives the application no host-level access for reading container logs; the administrator log viewer works only after an explicit operator opt-in. Customers with strict separation requirements collect container logs with their own host agents.

---

[Previous: 21. Audit Architecture](21-audit-architecture.md) | [Contents](../README.md) | [Next: 23. Air-Gapped Deployment](23-air-gapped-deployment.md)

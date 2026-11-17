# SunDog Appliance Security & Verification Architecture

This repository documents the security boundaries, runtime hardening controls, and acceptance gates enforced across SunDog Intelligent Systems on-premises appliances.

## 1. Defense-in-Depth Boundary Model

```text
+-----------------------------------------------------------------------+
| Customer Premises (Encrypted NVMe + FileVault / LUKS)                 |
|                                                                       |
|  [Internal Staff / SMB Drop Folder]                                   |
|                 |                                                     |
|                 v                                                     |
|  +-----------------------------------------------------------------+  |
|  | Rootless Service Container (--read-only, --cap-drop=ALL)        |  |
|  |  - SHA-256 Idempotent Ingestion                                 |  |
|  |  - Deterministic Math, Schema & Prompt-Injection Guardrails     |  |
|  |  - SQLite WAL State + Encrypted Snapshot Verification           |  |
|  +-----------------------------------------------------------------+  |
|                 | Loopback Only (AIR_GAP_ENFORCED=1)                  |
|                 v                                                     |
|  +-----------------------------------------------------------------+  |
|  | Local Unified-Memory Inference Engine                           |  |
|  |  - Dedicated Embedding & Quantized Reasoning Slots              |  |
|  |  - Zero Public Cloud Fallback (Fail-Closed HTTP 503)            |  |
|  +-----------------------------------------------------------------+  |
+-----------------------------------------------------------------------+
          | Outbound Health Telemetry Only (Scrubbed Metadata)
          v
  [SunDog Managed NOC Watchdog via Encrypted Mesh Tunnel]
```

## 2. Production Cutover Acceptance Gates

No customer appliance enters production without passing four automated verification gates:

1. **Air-Gap Endpoint Enforcement:** Startup and per-request assertions reject any non-loopback or non-RFC1918 inference target when `AIR_GAP_ENFORCED=1`.
2. **Idempotent State Integrity:** Repeated ingestion of identical files produces zero duplicate database chunks or payable tickets, backed by `PRAGMA integrity_check` validation on hot backups.
3. **Golden Evaluation Suite:** Client-specific test cases verify grounded inline citations, out-of-scope refusals, exact cent-level arithmetic reconciliation, and adversarial prompt-injection quarantine.
4. **Resource & Log Hygiene:** Container resident memory must remain within provisioned ceilings with zero unhandled exceptions or silent fallback warnings.

## 3. Managed Watchdog Retainer Scope

Appliances under monthly SunDog operational retainers receive automated 5-minute health checks, off-host encrypted backup verification, self-healing service recovery, and monthly executive SLA and drift attestations.

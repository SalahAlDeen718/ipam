# Architecture

This document describes how IPAM is structured. It describes design, not deployment-specific values.

## Overview

```
 Android app / Web (Flutter)
          |
          |  HTTPS + realtime (SSE)         private access via VPN (WireGuard)
          v
   Caddy (TLS reverse proxy)
          |
          v
   PocketBase  ---  JS hooks (server-side rules)
          |
        SQLite
```

One small Linux server runs everything. There is no required external cloud service.

## Client

- **Flutter** single codebase for Android and web.
- **Riverpod** for state management, **go_router** for navigation (hash URLs on web so the browser back button works correctly).
- **Repository layer** per domain (ranges, subnets, allocations, customers, branches, shifts, activity), keeping UI free of backend details.
- **Realtime** lists update as other users change data. Streams are wrapped with a retry policy to survive cold-start authentication races.
- **Bilingual UI** (Arabic/English) with right-to-left layout and an in-memory locale.

## Backend

**PocketBase** provides authentication, a SQLite database, realtime subscriptions, and JavaScript hooks.

### Data model (conceptual)

| Entity | Purpose |
|---|---|
| Branch | Organizational unit; the isolation boundary |
| User | Belongs to one branch, has a role |
| Range | A block of addresses (CIDR) |
| Subnet | A slice of a range with VLAN ID, gateway, usable-host count |
| Allocation | Addresses assigned to a customer from a subnet |
| Customer | Per-branch customer record |
| Activity log | Immutable record of changes |
| Shift task / Shift check-in | Shift handover board and the duty log |

### Server-side rules

All rules that protect data integrity run on the server through PocketBase hooks and collection rules:

- **Overlap protection:** an allocation is rejected if any address is already assigned, evaluated atomically so two simultaneous requests cannot both win.
- **Branch scope:** non-owner users can only read and write their branch's records.
- **Role restrictions:** user administration and destructive operations are limited to the owner role.
- **Shift guards:** shift check-in and check-out rules prevent duplicate or overlapping duty records.

The client is treated as untrusted. Hiding a button is never the only protection.

## Security model

- Authentication with per-user accounts; temporary passwords are rotated on first use.
- Roles: owner and branch-level roles with least privilege.
- TLS everywhere; remote access through a VPN so the service is not exposed publicly unless the operator chooses to.
- Secrets in environment variables; none are stored in the repository.
- Activity log records who did what and when.

## Deployment

- Ubuntu LTS server, PocketBase as a systemd service, Caddy for TLS, WireGuard for private access.
- Migrations are versioned JavaScript files applied on start.
- Planned: Docker Compose stack and an installer (see [ROADMAP.md](../ROADMAP.md), Milestone 2).

## Testing

- Rule-level tests verifying role and branch isolation.
- Repository tests for allocation invariants (no overlap, correct usable-host math).
- CI runs analysis and tests on every change.

## Planned components

- **Reservation agent:** separate service that keeps unassigned addresses from being used by mistake, synchronized in real time with the inventory, defensive only.
- **Monitoring collector:** SNMP poller for switch and port status, errors, and temperature, feeding the same notification system.

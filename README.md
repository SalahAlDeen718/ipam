# IPAM

**Self-hosted IP address management and network operations for small ISPs and telecom operators.**

Stop tracking IP space in spreadsheets. IPAM gives a network team one shared, branch-aware system to define address ranges, split them into VLAN-tagged subnets, assign addresses to customers without overlap, and hand over shifts, on phones and in the browser, from a single server you control.

> **Status:** in production use today by a network operations team of about **45 users across three branches**, with the user base expected to grow. The source code is being cleaned of all deployment-specific data and will be published in stages (see [ROADMAP.md](ROADMAP.md)). This repository currently holds the project's documentation, architecture, and plan.

---

## Why IPAM exists

Small and regional ISPs usually manage IP space in spreadsheets or paper. That leads to the same recurring problems:

- the same address assigned to two customers;
- nobody knows which subnet or VLAN is free;
- the day shift forgets to tell the night shift what is pending;
- one branch accidentally edits another branch's data;
- commercial IPAM suites are expensive, cloud-dependent, and built for large enterprises.

IPAM is built for the opposite environment: a few branches, a few dozen engineers, limited budget, unreliable internet, and a need to own your data.

## What it does

| Area | Capability |
|---|---|
| **Ranges** | Define a public or private range (for example `203.0.113.0/24`). |
| **Subnets and VLANs** | Split a range into smaller subnets (for example `/27`), each with a VLAN ID, gateway, and usable-host calculation. |
| **Allocations** | Assign addresses to a customer on a visual map (free vs reserved). The server rejects any overlap or double assignment. A ready-to-send message (gateway, addresses, VLAN) is generated for the customer. |
| **Customers** | Per-branch customer registry linked to their allocations. |
| **Branch isolation** | Every user belongs to a branch and only sees that branch's data. Enforced on the server, not just hidden in the UI. |
| **Roles** | Owner and branch roles with different permissions; destructive actions are restricted. |
| **Activity log and notifications** | Who changed what and when, with real-time notifications. |
| **Shift handover** | Shared task board per branch, check-in / check-out of shifts, and a shift log showing who was on duty and when. |
| **Bilingual UI** | Arabic and English, with proper right-to-left layout. |
| **Reports** | Arabic/English printable reports. |

## How it is built

- **Client:** Flutter, one codebase for Android and web (state management with Riverpod, routing with go_router).
- **Backend:** PocketBase (single binary with SQLite, auth, realtime, and server-side hooks).
- **Server-side rules:** JavaScript hooks and migrations enforce allocation integrity and access rules atomically.
- **Deployment:** one small Linux server, Caddy for TLS, WireGuard for private remote access. No external cloud required.
- **Quality:** rule-level tests, repository tests for allocation invariants, and CI.

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for details.

## Where it is going

IPAM is growing from an address ledger into a lightweight **network operations platform** for small operators:

1. **Open-source release** with a one-command install.
2. **IP reservation agent:** a defensive service that keeps unassigned addresses from being used by accident.
3. **Network monitoring:** switch and port status, interface errors, and temperature through SNMP, shown next to the inventory.
4. **Multi-branch scale:** more branches, more users, and better reporting.

Full plan with milestones: [ROADMAP.md](ROADMAP.md).

## Real-world validation

IPAM was not built as a demo. It has gone through the things that usually break internal tools:

- adoption by a working network team, now about 45 users in three branches;
- a full **backend migration** from a managed cloud database to a self-hosted PocketBase stack, with export, import, and a zero-mismatch verification step;
- fixes for real production incidents (for example a numeric-default edge case in shift timing and a realtime-stream race), each documented as lessons for other builders.

## Documentation

- [ROADMAP.md](ROADMAP.md): milestones, open-source release plan, and the monitoring vision.
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md): components, data model, security model, deployment.
- [docs/LESSONS.md](docs/LESSONS.md): engineering lessons from running IPAM in production.
- [CONTRIBUTING.md](CONTRIBUTING.md) and [SECURITY.md](SECURITY.md).

## Contributing

Operators, translators, network engineers, and developers are all welcome. Start with [CONTRIBUTING.md](CONTRIBUTING.md), or open an issue describing how you manage IP space today.

## License

[MIT](LICENSE)

## Author

Maintained by [@SalahAlDeen718](https://github.com/SalahAlDeen718), a networking student and solo developer.

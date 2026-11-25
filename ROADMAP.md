# Roadmap

Principle: **finish and verify each step before starting the next.** Dates are relative to the day the source release begins, so the plan stays honest.

## Where we are

- IPAM v2 is live in production for about 45 users in three branches.
- Core features are complete: ranges, subnets and VLANs, allocations with overlap protection, customers, branch isolation, roles, activity log, notifications, shift handover, bilingual UI.
- The backend runs self-hosted (PocketBase, Caddy, WireGuard) after a verified migration from a managed cloud database.
- This repository is the public home of the project. The source is being sanitized for release.

## Milestone 0: Public home (now)

- [x] README, architecture overview, lessons learned, contributing guide, security policy, license
- [ ] Repository topics, description, and a project board for the roadmap below

## Milestone 1: Sanitized source release

Goal: anyone can read, build, and run the code, and nothing private is inside it.

- [ ] Audit the whole history and working tree for secrets, real IP inventories, customer data, and deployment-specific values
- [ ] Publish from a **fresh history** (no old commits) to guarantee nothing leaks
- [ ] Replace all real data in fixtures and docs with documentation ranges (RFC 5737)
- [ ] Move every configurable value (server URL, branding, domain rules) to environment/config files
- [ ] Add `.env.example`, a seed script with demo data, and screenshots
- [ ] Tag `v0.1.0`

**Done when:** a stranger can clone the repo, follow the README, and see the app running with demo data.

## Milestone 2: One-command deployment

Goal: a small ISP can install IPAM in under 30 minutes.

- [ ] Docker Compose stack: PocketBase, Caddy (automatic TLS), optional WireGuard guide
- [ ] Installer script and a step-by-step guide for a fresh Ubuntu server
- [ ] Backup and restore scripts with a documented restore test
- [ ] Upgrade guide between versions

## Milestone 3: Quality and trust

- [ ] Port the rule tests and allocation-invariant tests to the public repo
- [ ] CI on every pull request: analyze, unit tests, server-rule tests
- [ ] Threat model and a documented security review
- [ ] Localization framework documented so new languages can be added (RTL supported)

## Milestone 4: IP reservation agent

A small standalone service that keeps addresses that are not assigned from being taken by mistake.

- Reserved set = usable addresses minus current allocations minus the gateway
- Runs as a separate service, synchronized in real time with the inventory
- **Defensive only:** it answers for reserved addresses following RFC 5227 address-conflict behavior and never poisons or attacks the network
- Requires a trunk-capable network port so one host can see every VLAN, so the design includes a pre-flight check and a safe "observe only" mode

## Milestone 5: Network monitoring

Turn the inventory into an operations view.

- SNMP polling of switches: interface up/down, error counters, utilization
- Temperature and basic equipment health
- Link each switch port to the subnet or customer it serves
- Alerts and notifications through the existing notification system
- Dashboards per branch

## Milestone 6: Scale and ecosystem

- [ ] Performance targets for hundreds of subnets and many branches, with benchmarks
- [ ] CSV/JSON import and export for migrating from spreadsheets
- [ ] Public REST/realtime API documentation
- [ ] More languages through community translation

## What I will do with Claude

The work above is large for one student developer. Claude is used here as a daily engineering partner:

- sanitizing and auditing the codebase before release (Milestone 1);
- writing and reviewing deployment scripts, tests, and documentation (Milestones 2 and 3);
- designing and implementing the reservation agent and SNMP monitoring (Milestones 4 and 5);
- reviewing security-sensitive changes and answering contributors faster.

## How progress is tracked

Each milestone becomes a GitHub milestone with issues. Releases are tagged and described in the changelog so progress is visible and verifiable.

# Contributing to IPAM

Thank you for your interest. The project is young, so contributions of every size matter: docs, translations, bug reports, tests, and code.

## Ways to help

- **Operators:** tell us how you track IP space today and what is missing. Open an issue using the "Feature request" template.
- **Translators:** the UI is bilingual (Arabic/English, RTL-aware). More languages are welcome once the source is published.
- **Developers:** look for issues labelled `good first issue` and `help wanted`.
- **Network engineers:** review the monitoring and reservation-agent designs in [ROADMAP.md](ROADMAP.md) and [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md). Design feedback is as valuable as code.

## Ground rules

1. Open an issue before large changes so we can agree on the approach.
2. Keep pull requests small and focused; include tests for behavior changes.
3. Never commit secrets, real customer data, real IP inventories, or production configuration. Use documentation ranges (RFC 5737: 192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24) in examples and fixtures.
4. Security problems go through [SECURITY.md](SECURITY.md), not public issues.

## Commit style

Short imperative subject line (`Fix overlap check for /31 ranges`), details in the body when needed.

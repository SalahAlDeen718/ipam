# Security Policy

IPAM manages network inventory data, so security reports are taken seriously.

## Reporting a vulnerability

Please do **not** open a public issue. Use GitHub's private vulnerability reporting ("Security" tab, "Report a vulnerability") on this repository. Include steps to reproduce, affected version, and impact. You will get an acknowledgement as soon as possible, and fixes will be released with credit unless you prefer otherwise.

## Scope

- Authentication, role and branch-isolation bypasses
- Allocation/overlap integrity bugs that allow double assignment
- Injection, XSS, or data exposure in the web client
- Insecure defaults in the deployment files

## Hardening principles

- Server-side enforcement: every rule that matters (role, branch scope, overlap) is checked on the server, never only in the client.
- Least privilege per role; destructive actions are restricted to the owner role.
- Everything runs behind a VPN or TLS reverse proxy by default; the documented deployment exposes no unauthenticated endpoints.
- Secrets live in environment variables, never in the repository.

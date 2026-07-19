# Security Policy

## What this project is

Markdown Agent Skills that instruct coding agents how to help with compliance and engineering tasks. They do **not** run as privileged services, but agents execute tools with the user’s permissions — treat skill content as code you install.

## Reporting a vulnerability

If you find a security issue in this repository (e.g. instructions that would exfiltrate secrets, unsafe shell patterns in docs/scripts, or malicious contribution drafts):

1. Prefer a **private** vulnerability report via GitHub Security Advisories on this repo if available
2. Or email the maintainer via the address on the [GitHub profile](https://github.com/KeshariPiyush24)

Please include: affected path, what an agent might do, and a minimal reproduction.

## Safe use recommendations

- Inspect `SKILL.md` and references before production use (`npx skills add … --copy` and review locally)
- Prefer pinning to a release tag for org-wide rollouts
- Do not paste production secrets into chat sessions used for audits

## Scope

- **In scope:** skill instructions that could cause data loss, secret leakage, or unintended network calls when followed
- **Out of scope:** whether following the skill makes a product legally compliant (that is counsel’s call)

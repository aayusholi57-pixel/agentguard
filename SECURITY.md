# Security Policy

## Reporting a vulnerability

Please do not publish sensitive vulnerability details in a public GitHub issue.

For security reports, use the repository's private GitHub security reporting mechanism when available. If private reporting is unavailable, open a minimal issue asking for a private communication channel without including exploit details.

## Scope

Security reports are especially relevant to:

- Scanner bypasses that allow known agent-directed payloads to evade detection
- Incorrect severity or exit-code behavior that could weaken CI enforcement
- Unsafe file handling
- Unexpected network access
- Supply-chain or release-process weaknesses

## Responsible disclosure

Please provide enough information to reproduce the issue safely, including the affected version, environment, expected behavior, actual behavior, and a minimal reproduction when possible.

Do not include secrets, credentials, private data, or destructive payloads in public reports.

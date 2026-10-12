# Security policy

## Release scope

ViolinNext is currently Beta v0.1 Build 2. Security fixes target the latest beta; older builds do not have a separate backport commitment. Beta status is not a security certification or a guarantee of production readiness.

## Private reporting

Email **[violintvex@gmail.com](mailto:violintvex@gmail.com)** with the subject `ViolinNext security report`.

Include the affected version, installation method, impact, prerequisites, and minimal reproduction steps. Use synthetic or redacted evidence. Do not send live passwords, private keys, tokens, complete databases, or customer configurations. If sensitive evidence is necessary, first ask the maintainer to agree on a suitable transfer method.

Do not publish exploit details or sensitive evidence in GitHub issues. Coordinate disclosure after the report has been investigated. There is no guaranteed response time or paid bounty program.

## Deployment practices

- Use SNMPv3 with authenticated encryption where supported, read-only device access, and explicit network allowlists.
- Restrict syslog and trap receivers to approved sources. UDP syslog is not cryptographically authenticated.
- Use HTTPS for shared access, TOTP for administrators, and least-privilege application roles.
- Keep the operating system and runtime updated; verify release signatures before installing packages.
- Protect backups, databases, encryption keys, and setup codes. Keep runtime data out of source archives and public repositories.
- Treat device-reported faults and inferred loop conditions as evidence requiring investigation. Monitoring does not automatically disable ports.

See the [README](README.md#release-status) for testing limitations and deployment guidance.

# Contributing to ViolinNext

Help improve monitoring accuracy, device compatibility, documentation, and operator workflows.

## Report a bug

Search existing issues first, then use the [bug report form](https://github.com/violintvex/ViolinNext/issues/new?template=bug_report.yml). Include:

- Application version and build, installation method, Linux distribution, and Node.js version.
- Reproduction steps, expected behavior, and actual behavior.
- For device issues: vendor/model, firmware, SNMP version, and collection timestamps.
- Sanitized logs or screenshots that demonstrate the problem.

Remove passwords, SNMP communities and keys, API tokens, session cookies, setup codes, private keys, database files, and customer information. Do not upload full configurations or runtime directories.

## Propose a feature

Use the [feature request form](https://github.com/violintvex/ViolinNext/issues/new?template=feature_request.yml). Describe the operational problem, expected outcome, current workaround, and device or protocol requirements. Distinguish observed behavior from assumptions.

## Submit changes

The public repository currently focuses on documentation and release assets. Confirm that the files needed for a code change are published before opening a pull request; see [source availability](README.md#source-and-development). Documentation and issue contributions do not require an application build.

For application changes, work from a clean source distribution and keep the change focused. Explain the problem, the resulting behavior, and the checks performed. Include regression coverage for changed behavior and screenshots for visible changes. Use English for product text and documentation.

```sh
npm ci --prefix backend
npm ci --prefix frontend-dashboard
node scripts/verify-production.mjs
```

Run verification before submitting application changes. Report failures and checks that could not be run. A passing suite does not establish real-device compatibility or enterprise capacity. Never run monitoring experiments against networks without authorization.

## Report security issues privately

Follow [SECURITY.md](SECURITY.md). Do not use a public issue for credentials, exploitable vulnerabilities, or sensitive evidence.

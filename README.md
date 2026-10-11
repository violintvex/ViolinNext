<div align="center">

<img src="assets/screenshots/violinnext-vn.svg" width="72" height="72" alt="ViolinNext" />

# ViolinNext

**Network monitoring and infrastructure management**

Monitor network health, investigate connectivity, and report on infrastructure from one self-hosted platform.

![Release](https://img.shields.io/badge/release-v0.1%20beta%20%C2%B7%20Build%202-217c76?style=flat-square)
![Platform](https://img.shields.io/badge/packages-DEB%20%7C%20RPM-52647e?style=flat-square)
![SNMP](https://img.shields.io/badge/SNMP-v1%20%7C%20v2c%20%7C%20v3-52647e?style=flat-square)

[Features](#features) · [Screenshots](#screenshots) · [Installation](#installation) · [Configuration](#initial-configuration) · [Operations](#operations) · [Support](#support)

</div>

## Overview

ViolinNext is a self-hosted network operations platform for IT teams. It combines device monitoring, infrastructure inventory, interface diagnostics, event management, and PDF reporting with role-based access controls.

Monitoring uses actual device responses and stored measurements. SNMP collection is read-only. New samples trigger dashboard updates, with periodic refresh as a fallback. The default SNMP collection interval is 30 seconds; measurement timestamps indicate the age of the data.

## Features

| Area | Capabilities |
| --- | --- |
| **Network monitoring** | SNMP v1/v2c/v3, interface status, bandwidth, errors, and counters |
| **Hardware telemetry** | CPU, memory, storage, temperature, PoE, VLAN, and STP where supported by device MIBs |
| **Service monitoring** | ICMP/Ping, HTTP/HTTPS, DNS, TCP, and numeric SNMP OID sensors |
| **Inventory and discovery** | Device inventory, interface discovery, telemetry profiles, and custom vendor OIDs |
| **Diagnostics** | Visual port detection, high inbound broadcast-rate warnings, increasing inbound error detection, IP/MAC lookup, MacTrack history, and bounded SNMP Walk |
| **Network mapping** | Topology views and WeatherMap with interface traffic visualization |
| **Events and alerts** | Syslog and SNMP trap receivers, custom thresholds, consecutive-sample evaluation, recovery events, and acknowledgment |
| **Notifications** | Telegram, Discord, WhatsApp, and custom HTTPS webhooks |
| **Access management** | Custom roles and permissions, TOTP authentication, session revocation, and audit logging |
| **Reporting** | PDF management summaries, inventory, events, interfaces, and filtered monitoring reports |
| **Configuration archives** | Encrypted storage, text imports, and retrieval from an existing Oxidized service |

Device measurements depend on firmware, MIB support, and configured access. Unsupported readings remain unavailable. IP/MAC observations can identify an uplink or virtual interface and do not always establish an endpoint's physical port.

## Screenshots

These captures show the actual application interface with **synthetic demonstration data**. Device identities, addresses, measurements, and events are fictional. They illustrate the interface, not a live deployment or a performance benchmark. Demo data is isolated from the production application and is not included in the installation packages.

### Operations overview

Device health, collection timestamps, and historical trends in a single view.

![ViolinNext Overview with synthetic demonstration data](assets/screenshots/overview.png)

### Port detection and diagnostics

Interface states and details, including an example of a high inbound broadcast rate and increasing inbound errors on a selected port.

![ViolinNext Port detection with simulated port states and broadcast warnings](assets/screenshots/ports.png)

### Management reporting

Prepare a PDF report with the organization, recipient, reporting period, and management notes.

![ViolinNext Reports form populated with fictional company information](assets/screenshots/reports.png)

## Port fault detection

**Available in Beta v0.1 Build 2:** automatic fault classification and evidence-based loop investigation.

Recognized syslog messages identify device-reported loops, MAC flapping, storm-control conditions, error-disabled ports, and link-down reports. The receiver shows a classification with extracted interface names. A message creates an event only when its source IP uniquely matches an inventory device; unmatched or ambiguous sources remain visible as messages. UDP syslog source identity is not authenticated, so a device report is evidence to investigate, not independent confirmation.

A **suspected loop** is raised when the same MAC has at least three recorded moves within five minutes and its mapped interface also has a high inbound broadcast rate. This requires MacTrack/discovery collection and supported forwarding tables. Mobility, uplinks, and legitimate topology changes can produce similar symptoms; verify the event evidence before acting.

Detections appear in **Events & follow-up** and use configured warning-event notification destinations. Repeated events of the same kind on the same device/port are suppressed for ten minutes. Acknowledgment records operator review; silence does not prove recovery. No ports are automatically disabled.


- **High broadcast rate:** flags a port when its measured inbound broadcast rate exceeds `BROADCAST_THRESHOLD_PPS` (default: **800 packets/second**). This is a threshold-based warning, not proof of a network loop.
- **Increasing inbound errors:** flags a port when its inbound error counter increases between valid collection samples.
- **Interface states:** displays operational and administrative states, including up, down, disabled/absent, and unknown. A gray port does not prove that the device is powered off.
- **STP visibility:** collects supported spanning-tree states through device MIBs for investigation. STP blocking alone is not classified as a loop.

Counter-based warnings need comparable samples and supported counters. Short bursts between collection intervals may not be captured. Monitoring is read-only: ViolinNext does not automatically shut down a port or change STP configuration.

## Installation

### Requirements

The Linux packages target **x86_64 / amd64** and require:

- **Node.js 22 or newer**, installed from a trusted distribution or vendor repository.
- **systemd** and **glibc 2.36 or newer**.
- Network access to the devices and services being monitored.

Application dependencies are included; Node.js is not bundled. ARM and Alpine Linux are not supported by these packages. ICMP monitoring requires the system `ping` utility and appropriate permissions.

### Download Beta v0.1 Build 2

| Platform | Package |
| --- | --- |
| Debian / Ubuntu | `violinnext_0.1.0~beta.1-2_amd64.deb` |
| Fedora / RPM | `violinnext-0.1.0-0.beta.1.2.x86_64.rpm` |

Download **ViolinNext Beta v0.1 Build 2** from this repository's **Releases** section. Select the package for your distribution and download `SHA256SUMS`, `SHA256SUMS.asc`, `VIOLINTVEX-PUBLIC-KEY.asc`, `RELEASE-NOTES.txt`, and `BUILD-VERIFICATION.json`. Debian users also need the matching `.deb.asc` file.

### Verify the download

**Build 2 signing status: verified.** The RPM contains an OpenPGP signature, and the Debian package has a detached `.deb.asc` signature. Both package signatures and the detached `SHA256SUMS.asc` manifest signature were verified locally on 11 October 2026. All files listed in the manifest matched their checksums.

Download `VIOLINTVEX-PUBLIC-KEY.asc` and the matching package signature files from the release assets. Build 1 signatures cannot verify Build 2 packages.

**Build 2 release key fingerprint:**

```text
70B2F33BB11B5C967073C57C649D9109AC9AA517
```

Before importing `VIOLINTVEX-PUBLIC-KEY.asc`, compare its full fingerprint with a separately trusted maintainer announcement:

```sh
gpg --show-keys --with-fingerprint VIOLINTVEX-PUBLIC-KEY.asc
```

For the Debian package:

```sh
gpg --import VIOLINTVEX-PUBLIC-KEY.asc
gpg --verify violinnext_0.1.0~beta.1-2_amd64.deb.asc violinnext_0.1.0~beta.1-2_amd64.deb
```

For the RPM package:

```sh
sudo rpmkeys --import VIOLINTVEX-PUBLIC-KEY.asc
rpmkeys --checksig --verbose violinnext-0.1.0-0.beta.1.2.x86_64.rpm
```

Require a valid signature from the expected key before installation. A digest-only result or missing key is not successful signature verification. Local APT installation does not automatically check the detached `.deb` signature.

To verify the release manifest and all listed assets, download them into the same directory and run:

```sh
gpg --import VIOLINTVEX-PUBLIC-KEY.asc
gpg --verify SHA256SUMS.asc SHA256SUMS
sha256sum -c SHA256SUMS
```

Verify the signed manifest before trusting its checksums. The checksums cover both packages, the Debian signature, public key, release notes, and verification record. The included release notes and verification record were finalized before manifest signing and still describe that earlier signing stage; the successfully verified `SHA256SUMS.asc` establishes the current manifest signature.

### Install the package

**Debian / Ubuntu**

```sh
sudo apt install ./violinnext_0.1.0~beta.1-2_amd64.deb
```

**Fedora**

```sh
sudo dnf install ./violinnext-0.1.0-0.beta.1.2.x86_64.rpm
```

Installation configures the API and dashboard as systemd services and enables them at boot. Open **http://127.0.0.1:3840** on the installed machine.

Retrieve the installation code to create the first administrator:

```sh
sudo violinnext setup-code
```

There are no default accounts or passwords.



## Initial configuration

1. Create the administrator account using the installation code.
2. Open **Settings → Network settings** and define the networks authorized for monitoring.
3. Add read-only SNMP profiles matching the credentials and security settings on your devices.
4. Add devices to **Device inventory** and save the inventory.
5. Enable **Telemetry & discovery** where metric history and discovery are required.
6. Configure sensors, threshold rules, and destinations under **Bots & notifications**.
7. Define team access through **Roles & permissions** and **User management**.

Permit the collector's source address in device ACLs. Restrict receiver ports to approved senders. Allow at least two collection cycles for bandwidth rates, and check timestamps when assessing device state.

## Operations

### Services

| Command | Purpose |
| --- | --- |
| `sudo violinnext status` | Inspect API and dashboard service status |
| `sudo violinnext start` | Start both services |
| `sudo violinnext restart` | Restart after configuration changes |
| `sudo violinnext stop` | Stop both services |
| `violinnext version` | Display the installed release |
| `journalctl -u violinnext-backend -u violinnext-web` | Inspect service logs |

### Configuration and data

| Location | Contents |
| --- | --- |
| `/opt/violinnext` | Installed application |
| `/etc/violinnext/backend.env` | Private runtime configuration |
| `/var/lib/violinnext` | Database, encryption keys, and persistent settings |
| `/usr/share/doc/violinnext` | Packaged documentation and proxy example |

ViolinNext currently runs as a single-server application using a Node.js API, a Next.js dashboard, and SQLite. Collection cycles do not overlap; slow devices can extend the configured interval.

### Shared HTTPS access

The application listens on loopback by default. For team access, configure a trusted HTTPS reverse proxy using `/usr/share/doc/violinnext/nginx.conf.example`, which targets port 3840.

Set the following values in `/etc/violinnext/backend.env`, replacing the example hostname with your own:

```ini
VIOLINNEXT_LOCAL_DESKTOP=0
ALLOWED_ORIGINS=https://noc.example.com
TRUST_PROXY=loopback
```

Keep the API and dashboard bound to loopback, configure the proxy's TLS certificate, and restart the services.

### Backup and upgrade

Before upgrading, stop the services and back up `/var/lib/violinnext` together with `/etc/violinnext`. Preserve the database's matching encryption key and private settings. Install the newer package, then verify service health and sample timestamps.

Uninstalling the package, including Debian purge, deliberately preserves persistent data and configuration. Remove these directories manually only when permanently retiring the installation. Source installations store their local runtime data under `.violinnext/`.

## Release status

**Beta v0.1 Build 2** includes automatic fault detection, receiver classifications, and per-port warning events. The backend suite passed 89 tests; frontend lint, build, and HTTP smoke checks passed. Both package payloads were extracted and passed application startup and API smoke checks.

Build 1 installation, reinstallation, and data preservation were tested in isolated Ubuntu 24.04 and Fedora 44 environments. Those distribution-level install/upgrade checks have not been repeated for Build 2.

Systemd unit syntax was checked during package preparation. Full hardened service startup still requires a VM or server test because rootless-container namespace restrictions prevented that check. These results do not establish compatibility with every device or capacity for enterprise workloads. See `BUILD-VERIFICATION.json` and `RELEASE-NOTES.txt` in the release assets for details.

## Security

Use least-privilege monitoring credentials, restrict authorized networks, enable TOTP for administrator accounts, and grant only the permissions each user needs. Protect database backups, encryption keys, and exported configuration archives.

Report suspected vulnerabilities privately to [violintvex@gmail.com](mailto:violintvex@gmail.com). Do not include credentials or customer network information in public issues.

## Contributing

Include the application version, operating system, reproduction steps, and expected versus actual behavior in bug reports. For device issues, include the model, firmware, SNMP version, and sanitized diagnostic evidence.

Run the verification suite before submitting code changes:

```sh
npm ci --prefix backend
npm ci --prefix frontend-dashboard
node scripts/verify-production.mjs
```

## License

Copyright © 2026 violintvex.

This project is free software licensed under the **GNU General Public License v3.0 (GPL-3.0-only)**.

You may use, modify, and redistribute this software under the terms of the GNU GPL v3.

See the [LICENSE](LICENSE) file for the complete license terms.

Developed and maintained by **violintvex**.

## Support

For questions, suggestions, and feedback, contact [violintvex@gmail.com](mailto:violintvex@gmail.com).

Voluntary donations support the project:

[![Support ViolinNext](https://img.shields.io/badge/Support-ViolinNext-217c76?style=for-the-badge)](https://sociabuzz.com/violintvex/tribe)

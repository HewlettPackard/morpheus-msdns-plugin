# Morpheus Microsoft DNS Plugin

This plugin provides a DNS integration between [Microsoft DNS](https://learn.microsoft.com/en-us/windows-server/networking/dns/dns-top) and [Morpheus](https://morpheusdata.com). It enables DNS zone sync, DNS record sync, DNS record creation and removal, and optional PTR record creation from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.0 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-msdns-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **Microsoft DNS** integration type will appear after the plugin loads.

## Configuration

When adding a Microsoft DNS integration in Morpheus (**Administration → Integrations → Add Integration**), provide the following:

| Field | Description |
|-------|-------------|
| **RPC Server** | Windows server Morpheus connects to for RPC access. This can also be the DNS server. |
| **Rpc Port** | WinRM RPC port used when agent RPC is disabled. Defaults to `5985`. |
| **Use Agent for Rpc** | Use the Morpheus Agent on the RPC server as the RPC transport instead of WinRM. |
| **Credentials** | Morpheus username/password credential used by the integration. |
| **Username** | Local username field used when not selecting a stored credential. |
| **Password** | Local password field used when not selecting a stored credential. |
| **DNS Server** | DNS server to manage when it is different from the RPC server. |
| **Service Type** | How the RPC server accesses the DNS server: local, WMI, or WinRM. |
| **Zone Filter** | Comma-separated glob-style zone filters. Leave blank to import all zones. |
| **Inventory Existing** | Import existing resource records from matching zones. |
| **Create Pointers** | Attempt to create PTR records when forward records are created. |

## Features

### DNS Management
The plugin registers a `DNSProvider` for Microsoft DNS. Supported operations include:

- Create DNS records during Morpheus provisioning workflows
- Remove DNS records during teardown or manual cleanup workflows
- Validate and normalize DNS records before creation
- Support `A`, `AAAA`, `CNAME`, and `PTR` records
- Optionally request PTR record creation when creating forward records

### DNS Sync
The following resources are discovered and kept in sync from Microsoft DNS:

- **DNS Zones** — forward and reverse zones matching the configured zone filter
- **DNS Records** — existing zone records when **Inventory Existing** is enabled

Any additions, updates, and removals discovered in Microsoft DNS are reflected in Morpheus on the next integration refresh.

### RPC Connectivity Options
Microsoft DNS operations are executed through a Windows RPC server. The integration supports:

- WinRM-based RPC access to the configured RPC server
- Morpheus Agent-based RPC access from a managed Windows server
- Local, WMI, or WinRM access from the RPC server to the DNS server
- A built-in service test endpoint for validating integration connectivity

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2022 the original author or authors. Licensed under the [Apache License, Version 2.0](LICENSE).

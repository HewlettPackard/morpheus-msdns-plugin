# Morpheus Microsoft DNS Plugin

The Morpheus Microsoft DNS Plugin integrates Morpheus with Microsoft Windows DNS Server to provide DNS record automation. The plugin manages DNS records by executing PowerShell commands on the Windows DNS server via WinRM or a Morpheus agent (RPC), enabling automatic DNS registration and deregistration during instance provisioning and decommissioning.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### Microsoft DNS Record Management

Create and delete DNS records on a Windows DNS server when instances are provisioned or decommissioned. Supports WinRM and Morpheus agent transport for remote PowerShell execution.

### Cloud Sync

Morpheus synchronises the following Microsoft DNS resources for inventory:

- DNS zones and their records

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.0 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A Windows Server with the DNS Server role installed
- WinRM enabled and accessible on the Windows DNS server, OR a Morpheus agent installed on the server for agent-based RPC
- A Windows user account with permission to manage DNS records (typically Domain Admin or DNS Admin)
- Network access from the Morpheus appliance (or agent) to the DNS server on the WinRM port (default: 5985/5986)

---

## Repository structure

```
src/main/groovy/com/morpheusdata/msdns/
├── MicrosoftDnsPlugin.groovy           - Plugin entry point; registers MicrosoftDnsProvider, MicrosoftDnsOptionSourceProvider, and MicrosoftDnsPluginController
├── MicrosoftDnsProvider.groovy         - DNSProvider implementation; DNS operations, sync, and OptionTypes
├── MicrosoftDnsOptionSourceProvider.groovy - UI option source data
├── MicrosoftDnsPluginController.groovy - Custom UI controller for plugin endpoints
├── MicrosoftDnsPluginHelper.groovy     - Shared helper methods
└── MicrosoftDnsPluginRpcService.groovy - RPC service; executes PowerShell commands via WinRM or agent
src/main/resources/hbs/                 - Handlebars UI templates
build.gradle, gradle.properties         - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-msdns-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > DNS > Add** and select **Microsoft DNS** to configure the integration.

---

## Detailed Usage Steps

### Adding a Microsoft DNS Integration

1. Go to **Infrastructure > DNS > Add**.
2. Select **Microsoft DNS** as the DNS provider type.
3. Configure:
   - **RPC Server** — hostname or IP of the Windows DNS server
   - **RPC Port** — WinRM port (default: 5985 for HTTP, 5986 for HTTPS)
   - **Use Agent for RPC** — enable to use a Morpheus agent instead of WinRM
   - **Credentials** — Windows user credentials with DNS management permissions
   - **Username** and **Password**
   - **DNS Server** — DNS server hostname (may differ from the RPC server)
4. Save. Morpheus validates connectivity and syncs DNS zones.

### Creating DNS Records Automatically

When an instance is provisioned on a network with Microsoft DNS configured, Morpheus automatically creates the DNS A record (and optionally PTR record) on the Windows DNS server using PowerShell via WinRM or agent.

### Removing DNS Records

When an instance is decommissioned or its DNS record removed, Morpheus executes the appropriate PowerShell command to delete the record from the Windows DNS server.

---

## API Endpoints

This plugin does not use a REST API. It communicates with the Windows DNS server by executing **PowerShell commands via WinRM** (HTTP port 5985 or HTTPS port 5986) or via a **Morpheus agent** installed on the DNS server. DNS operations are performed using the Windows `DnsServer` PowerShell module.

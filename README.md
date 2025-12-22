# SCORCH Collection for Ansible and [Ludus](https://ludus.cloud)

An Ansible collection that installs System Center Orchestrator (SCORCH) deployments with optional configurations for security testing.

## Overview

System Center Orchestrator stores credentials for numerous enterprise systems through Integration Packs and encrypted variables. This makes it a high-value target for red team operations. This Ludus collection automates deployment of realistic SCORCH environments for security testing and tool development.

For the corresponding research and tools, see the [scorch-tools](https://github.com/professor-moody/scorch-tools) repository.

## Roles Included

| Role | Description |
|------|-------------|
| `disable_firewall` | Disable Windows Firewall on lab hosts |
| `create_scorch_accounts` | Create service accounts and groups in AD |
| `install_mgmt_server` | Install Management Server and create database |
| `install_runbook_server` | Install additional Runbook Server |
| `install_webservices` | Install Web API and Orchestration Console |
| `create_variables` | Create test encrypted variables for security testing |
| `create_runbooks` | Create sample runbooks, folders, schedules, and configs |

> **Note:** SQL Server is installed using `badsectorlabs.ludus_mssql` (auto-downloads, no ISO needed). SCORCH is also auto-downloaded from Microsoft Evaluation Center.

## Installation in [Ludus](https://ludus.cloud)

1. Add the required roles and collection to your Ludus server

```bash
ludus ansible role add badsectorlabs.ludus_mssql
ludus ansible collection add professor_moody.ludus_scorch
```

2. Set your config and deploy

```bash
ludus range config set -f distributed.yml
ludus range deploy
# Wait for the range to successfully deploy
# You can watch the logs with `ludus range logs -f`
# Or check the status with `ludus range status`
```

**No file uploads required** - everything is automatically downloaded during deployment.


## Prerequisites

### Required Roles

The collection depends on `badsectorlabs.ludus_mssql` for SQL Server installation:

```bash
ludus ansible role add badsectorlabs.ludus_mssql
```

### Media

Both SQL Server and SCORCH are automatically downloaded during deployment:

| Component | Source | Size |
|-----------|--------|------|
| SQL Server 2022 | Auto-downloaded by `badsectorlabs.ludus_mssql` | ~1.5GB |
| SCORCH 2022 | Auto-downloaded from Microsoft Evaluation Center | ~258MB |

The SCORCH installer is downloaded from:
```
https://go.microsoft.com/fwlink/p/?LinkID=2195531
```

This is a 180-day evaluation that requires no product key.

### Templates Required

- `win2022-server-x64-template`
- `win11-22h2-x64-enterprise-template` (optional, for workstation)

## Deployment Architecture

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│    DC01     │      │ SCORCH-SQL  │      │ SCORCH-MGMT │
│  Domain     │◄────►│ SQL Server  │◄────►│ Management  │
│  Controller │      │  Database   │      │ Runbook Srv │
│             │      └─────────────┘      │ Designer    │
└─────────────┘             ▲             └─────────────┘
                            │
         ┌──────────────────┼──────────────────┐
         │                  │                  │
         ▼                  ▼                  ▼
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│ SCORCH-RB   │      │ SCORCH-WEB  │      │    WS01     │
│ Runbook     │      │ Web API     │      │ Workstation │
│ Server (HA) │      │ Console     │      │             │
└─────────────┘      └─────────────┘      └─────────────┘
```

**VMs: 6** | **RAM: 28GB** | **Time: ~90 min**

Use: `ludus range config set -f distributed.yml`

## Configuration Variables

### Service Accounts

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_svc_account` | `scorch_svc` | Management/Runbook service account |
| `ludus_scorch_svc_password` | `ScorchP@ss123!` | Service account password |
| `ludus_scorch_web_account` | `scorch_web` | IIS App Pool identity |
| `ludus_scorch_web_password` | `ScorchWebP@ss123!` | Web service password |

### Database

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_db_server` | (required) | SQL Server hostname |
| `ludus_scorch_db_name` | `Orchestrator` | Database name |
| `ludus_scorch_db_create_new` | `true` | Create new database |
| `ludus_scorch_db_trust_cert` | `true` | Trust SQL Server certificate |

### Web Services

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_api_port` | `81` | Web API port |
| `ludus_scorch_console_port` | `82` | Orchestration Console port |
| `ludus_scorch_enable_anonymous_access` | `false` | Enable anonymous API access |
| `ludus_scorch_disable_ntlm_channel_binding` | `true` | Disable EPA for relay attacks |

### Security Testing

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_create_default_vars` | `true` | Create test encrypted variables |
| `ludus_scorch_enable_anonymous_access` | `false` | Enable unauthenticated access |

## Testing Your Deployment

After deployment, verify with [scorch-tools](https://github.com/professor-moody/scorch):

```bash
# Security assessment (no auth required)
./scorch assess -t SCORCH-WEB.scorch.ludus.domain

# Enumerate with domain credentials
./scorch enum -t SCORCH-WEB.scorch.ludus.domain -d SCORCH -u domainadmin -p password -all

# Extract credentials from database (using Windows auth via domain creds)
./scorch dump -t SCORCH-SQL.scorch.ludus.domain -db Orchestrator -d SCORCH -u domainadmin -p password -all -decrypt

# Pass-the-hash enumeration
./scorch enum -t SCORCH-WEB.scorch.ludus.domain -d SCORCH -u domainadmin -H <nthash> -all

# Password spray
./scorch spray -t SCORCH-WEB.scorch.ludus.domain -d SCORCH -users users.txt -pass 'Summer2024!'
```

## Default Credentials

| Account | Username | Password |
|---------|----------|----------|
| Domain Admin | `domainadmin` | `password` |
| SCORCH Service | `scorch_svc` | `ScorchP@ss123!` |
| SCORCH Web | `scorch_web` | `ScorchWebP@ss123!` |
| SQL Server | Windows Auth | (uses domain accounts) |

> **Note:** SQL Server is configured for Windows Authentication. SCORCH uses the service account to connect.

## Test Variables Created

The `create_variables` role populates these encrypted variables for credential extraction testing:

**Credentials:**
- `SQL_ServiceAccount_Password`
- `Domain_Admin_Password`
- `Exchange_ServiceAccount`
- `SCOM_RunAs_Password`
- `VMM_ServiceAccount`

**Secrets:**
- `API_Key_Production`
- `Azure_ClientSecret`
- `AWS_SecretAccessKey`
- `GitHub_PAT`

**Connection Strings:**
- `ProductionDB_ConnString`
- `HRIS_Database_Password`

## Sample Runbooks Created

The `create_runbooks` role creates a realistic runbook environment:

### Folder Structure
```
Runbooks/
├── IT Operations/
│   ├── Server Management/
│   ├── User Management/
│   └── Backup Operations/
├── Security/
│   ├── Incident Response/
│   └── Compliance/
├── Cloud/
│   ├── Azure/
│   └── AWS/
└── Integration/
    ├── ServiceNow/
    ├── SCOM/
    └── Exchange/
```

### Sample Runbooks (18 total)
| Runbook | Folder | Uses Credentials |
|---------|--------|------------------|
| Server Health Check | IT Operations\\Server Management | ✓ |
| Restart Windows Service | IT Operations\\Server Management | ✓ |
| Deploy Security Patches | IT Operations\\Server Management | ✓ |
| Create New User | IT Operations\\User Management | ✓ |
| Disable Terminated User | IT Operations\\User Management | ✓ |
| Reset User Password | IT Operations\\User Management | ✓ |
| SQL Database Backup | IT Operations\\Backup Operations | ✓ |
| Verify Backup Status | IT Operations\\Backup Operations | ✓ |
| Lock Compromised Account | Security\\Incident Response | ✓ |
| Collect Forensic Data | Security\\Incident Response | ✓ |
| Compliance Audit Report | Security\\Compliance | ✓ |
| Start Azure VMs | Cloud\\Azure | ✓ |
| Azure Resource Report | Cloud\\Azure | ✓ |
| AWS EC2 Snapshot | Cloud\\AWS | ✓ |
| Create ServiceNow Ticket | Integration\\ServiceNow | ✓ |
| SCOM Alert Handler | Integration\\SCOM | ✓ |
| Exchange Mailbox Report | Integration\\Exchange | ✓ |

### Schedules
- Daily 6AM
- Weekly Sunday 2AM
- Hourly

### Global Configurations (Integration Pack Settings)
| Configuration | Value | Category |
|--------------|-------|----------|
| SCOM_ManagementServer | scom-mgmt.corp.local | SCOM |
| SCOM_RunAsAccount | CORP\\scom_action | SCOM |
| Exchange_Server | exchange.corp.local | Exchange |
| ServiceNow_Instance | corpinc.service-now.com | ServiceNow |
| VMware_vCenter | vcenter.corp.local | VMware |
| Azure_TenantId | a1b2c3d4-... | Azure |
| AWS_Region | us-east-1 | AWS |

### Sample Runbook Export File

A sample `.ois_export` file is included at:
```
roles/create_runbooks/files/sample_runbooks.ois_export
```

This file can be manually imported into SCORCH via Runbook Designer for additional testing.

## Web Endpoints

| Endpoint | URL |
|----------|-----|
| Web API (OData) | `http://SCORCH-WEB:81/Orchestrator2012/Orchestrator.svc/` |
| Web API (REST) | `http://SCORCH-WEB:81/api/` |
| Orchestration Console | `http://SCORCH-WEB:82/` |

## Troubleshooting

### Installation Logs

```powershell
# SCORCH setup logs
Get-ChildItem "$env:LOCALAPPDATA\SCO\LOGS\*.log" | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | Get-Content -Tail 100

# Event logs
Get-WinEvent -LogName Application -MaxEvents 50 | Where-Object { $_.ProviderName -like "*Orchestrator*" }
```

### Service Status

```powershell
Get-Service Orchestrator* | Format-Table Name, Status, StartType
```

### Database Connectivity

```powershell
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Server=SCORCH-SQL;Database=Orchestrator;Integrated Security=True;TrustServerCertificate=True"
$conn.Open()
$conn.Close()
Write-Output "Connected successfully"
```

### Web API Test

```powershell
Invoke-WebRequest http://localhost:81/Orchestrator2012/Orchestrator.svc/ -UseDefaultCredentials
```

## Security Testing Scenarios

### 1. Unauthenticated Enumeration
Set `ludus_scorch_enable_anonymous_access: true` and test API enumeration without credentials.

### 2. NTLM Relay
With `ludus_scorch_disable_ntlm_channel_binding: true`, test NTLM relay attacks against the Web API.

### 3. Credential Extraction
Extract encrypted variables from the Orchestrator database using SQL access.

### 4. Runbook Execution
Execute runbooks that use stored credentials to pivot or extract data.

## Important Notes

⚠️ **Domain Suffix**: Do NOT use `.local` as the domain suffix. SCORCH has known issues with `.local` domains. Use `.ludus.domain` or similar.

⚠️ **Hostname Length**: All SCORCH VM hostnames must be 15 characters or less due to NetBIOS limitations.

⚠️ **Firewall**: The `disable_firewall` role disables Windows Firewall completely. Only use in isolated lab environments.

## References

- [SCORCH System Requirements](https://learn.microsoft.com/en-us/system-center/orchestrator/system-requirements-orch)
- [SCORCH Install Guide](https://learn.microsoft.com/en-us/system-center/orchestrator/install)
- [Fox-IT SCORCH Decryption](https://github.com/fox-it/Decrypt-OrchestratorSecretVariables)
- [SpecterOps SCOM Research](https://specterops.io/blog/2025/12/10/scommand-and-conquer-attacking-system-center-operations-manager-part-1/)

## License

GPL-3.0

## Credits

- Inspired by [ludus_scom](https://github.com/Synzack/ludus_scom) and [ludus_sccm](https://github.com/Synzack/ludus_sccm) by Zach Stein ([@synzack21](https://twitter.com/synzack21))
- Thanks to the [Ludus](https://ludus.cloud) team at Bad Sector Labs

# install_sql_server

Installs Microsoft SQL Server for SCORCH Orchestrator database.

## Features

- Auto-downloads SQL Server ISO (no manual upload required)
- Command-line installation (avoids WinRM DPAPI issues)
- Configurable SQL version (2019 or 2022)
- Optional SSMS installation
- Proper service account configuration

## Requirements

- Windows Server 2019 or 2022
- Domain-joined (for service account)
- Internet access for downloads

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_sql_version` | `"2019"` | SQL Server version |
| `ludus_scorch_sql_svc_account` | `"scorch_sql"` | SQL service account |
| `ludus_scorch_sql_svc_password` | `"ScorchSqlP@ss123!"` | SQL service password |
| `ludus_scorch_sql_instance_name` | `"MSSQLSERVER"` | SQL instance name |
| `ludus_scorch_sql_install_ssms` | `true` | Install SSMS |

## Example Usage

```yaml
roles:
  - professor_moody.ludus_scorch.install_sql_server
role_vars:
  ludus_scorch_sql_version: "2019"
  ludus_scorch_sql_install_ssms: true
```

## License

GPL-3.0-or-later

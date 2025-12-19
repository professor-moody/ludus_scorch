# install_mgmt_server

Installs SCORCH Management Server, Runbook Server, and Runbook Designer. SCORCH is automatically downloaded from Microsoft Evaluation Center.

## Requirements

- Windows Server 2019/2022
- Domain joined
- SQL Server accessible

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_components` | `ManagementServer,RunbookDesigner` | Components to install |
| `ludus_scorch_svc_account` | `scorch_svc` | Service account |
| `ludus_scorch_svc_password` | Required | Service account password |
| `ludus_scorch_db_server` | Required | SQL Server hostname |
| `ludus_scorch_db_name` | `Orchestrator` | Database name |
| `ludus_scorch_db_create_new` | `true` | Create new database |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.install_mgmt_server
role_vars:
  ludus_scorch_db_server: "SCORCH-SQL"
  ludus_scorch_svc_account: "scorch_svc"
  ludus_scorch_svc_password: "ScorchP@ss123!"
```

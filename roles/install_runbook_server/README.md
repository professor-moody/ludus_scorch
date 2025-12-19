# install_runbook_server

Installs an additional SCORCH Runbook Server for HA/load distribution. SCORCH is automatically downloaded from Microsoft Evaluation Center.

## Requirements

- Windows Server 2019/2022
- Domain joined
- Existing SCORCH database

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_svc_account` | `scorch_svc` | Service account |
| `ludus_scorch_svc_password` | Required | Service account password |
| `ludus_scorch_db_server` | Required | SQL Server hostname |
| `ludus_scorch_db_name` | `Orchestrator` | Database name |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.install_runbook_server
role_vars:
  ludus_scorch_db_server: "SCORCH-SQL"
  ludus_scorch_svc_account: "scorch_svc"
  ludus_scorch_svc_password: "ScorchP@ss123!"
```

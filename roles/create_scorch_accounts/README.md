# create_scorch_accounts

Creates SCORCH service accounts and security groups in Active Directory.

## Requirements

- Windows Domain Controller
- Active Directory PowerShell module

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_svc_account` | `scorch_svc` | Service account username |
| `ludus_scorch_svc_password` | `ScorchP@ss123!` | Service account password |
| `ludus_scorch_web_account` | `scorch_web` | Web service account username |
| `ludus_scorch_web_password` | `ScorchWebP@ss123!` | Web service account password |
| `ludus_scorch_users_group` | `OrchestratorUsers` | Orchestrator users group |
| `ludus_scorch_admins_group` | `OrchestratorAdmins` | Orchestrator admins group |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.create_scorch_accounts
role_vars:
  ludus_scorch_svc_account: "scorch_svc"
  ludus_scorch_svc_password: "ScorchP@ss123!"
```

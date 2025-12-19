# install_webservices

Installs SCORCH Web API and Orchestration Console. SCORCH is automatically downloaded from Microsoft Evaluation Center.

## Requirements

- Windows Server 2019/2022
- Domain joined
- Existing SCORCH database

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_web_account` | `scorch_web` | Web service account |
| `ludus_scorch_web_password` | Required | Web service account password |
| `ludus_scorch_db_server` | Required | SQL Server hostname |
| `ludus_scorch_api_port` | `81` | Web API port |
| `ludus_scorch_console_port` | `82` | Orchestration Console port |
| `ludus_scorch_enable_anonymous_access` | `false` | Enable anonymous API access |
| `ludus_scorch_disable_ntlm_channel_binding` | `true` | Disable EPA for relay testing |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.install_webservices
role_vars:
  ludus_scorch_db_server: "SCORCH-SQL"
  ludus_scorch_web_account: "scorch_web"
  ludus_scorch_web_password: "ScorchWebP@ss123!"
```

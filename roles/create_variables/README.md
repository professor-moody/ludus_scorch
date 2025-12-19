# create_variables

Creates test encrypted variables in the SCORCH database for credential extraction testing.

## Requirements

- SCORCH installed and database accessible

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_db_server` | Required | SQL Server hostname |
| `ludus_scorch_db_name` | `Orchestrator` | Database name |
| `ludus_scorch_create_default_vars` | `true` | Create default test variables |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.create_variables
role_vars:
  ludus_scorch_db_server: "SCORCH-SQL"
  ludus_scorch_create_default_vars: true
```

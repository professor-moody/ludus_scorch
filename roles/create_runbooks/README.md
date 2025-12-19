# create_runbooks

Creates sample runbooks, folders, schedules, and global configurations in SCORCH for realistic testing.

## Requirements

- SCORCH installed and database accessible

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_scorch_db_server` | Required | SQL Server hostname |
| `ludus_scorch_db_name` | `Orchestrator` | Database name |
| `ludus_scorch_create_default_runbooks` | `true` | Create default sample runbooks |

## Example

```yaml
roles:
  - professor_moody.ludus_scorch.create_runbooks
role_vars:
  ludus_scorch_db_server: "SCORCH-SQL"
  ludus_scorch_create_default_runbooks: true
```

# SCORCH Prerequisites Role

Install all prerequisites required for SCORCH 2022 components.

## Requirements

Windows Server 2019 or 2022.

## Role Variables

See `defaults/main.yml` for all configurable variables including:

- `.NET Framework` features
- `IIS` features and modules
- `VC++ Redistributable` download URL
- `.NET Hosting Bundle` download URL
- `Firewall` port configuration

## Dependencies

None.

## Example Playbook

```yaml
- hosts: scorch_servers
  roles:
    - professor_moody.ludus_scorch.prereqs
```

## License

GPL-3.0-or-later

# install_integration_packs

Downloads SCORCH Integration Pack files and provides manual installation instructions.

## Overview

Integration Packs extend SCORCH with activities for managing external systems. Each IP stores connection credentials in the database - making them targets for credential extraction.

**Important:** There is **no supported CLI method** to install Integration Packs in SCO 2022. This role downloads the files and provides instructions for manual installation via Deployment Manager GUI.

## What This Role Does

1. Downloads enabled IP installer files from Microsoft
2. Creates `INSTALL_INSTRUCTIONS.txt` with step-by-step guide
3. Optionally restores a pre-configured database backup (for automation)

## Default Variables

```yaml
# Enable specific IPs for download
ludus_scorch_integration_packs:
  - name: "Active Directory"
    download_id: "104333"
    enabled: true
  - name: "REST"
    download_id: "104337"
    enabled: true

# Optional: Restore pre-configured database
ludus_scorch_preconfigured_db: ""
# ludus_scorch_preconfigured_db: "C:\\Backups\\Orchestrator_with_IPs.bak"
```

## Usage

```yaml
- vm_name: "{{ range_id }}-SCORCH-MGMT"
  roles:
    - professor_moody.ludus_scorch.install_mgmt_server
    - professor_moody.ludus_scorch.install_integration_packs
```

After deployment:
1. RDP to SCORCH-MGMT
2. Check `C:\IntegrationPacks\INSTALL_INSTRUCTIONS.txt`
3. Follow the manual installation steps

## Automation via Database Restore

For fully automated deployments:

1. Deploy SCORCH and manually install IPs once
2. Configure test connections with credentials
3. Backup the database:
   ```powershell
   Backup-SqlDatabase -ServerInstance "SCORCH-SQL" -Database "Orchestrator" `
       -BackupFile "C:\Backups\Orchestrator_with_IPs.bak"
   ```
4. Use the backup in future deployments:
   ```yaml
   role_vars:
     ludus_scorch_preconfigured_db: "C:\\Backups\\Orchestrator_with_IPs.bak"
   ```

## Available Integration Packs

| IP | Download ID | Stores |
|----|-------------|--------|
| Active Directory | 104333 | Domain/User/Password |
| REST | 104337 | API tokens |
| Operations Manager | 104339 | SCOM RunAs |
| Configuration Manager | 104338 | SCCM admin |
| Exchange Admin | 104335 | Exchange creds |
| VMware vSphere | 106163 | vCenter creds |

## Why No Automation?

- Microsoft provides **no CLI tools** for IP registration
- SCO 2022 is 64-bit; old automation tools (OrchestratorCOM) were 32-bit
- The COM interfaces completely changed from SCO 2019

The database backup/restore approach is the most reliable automation path.

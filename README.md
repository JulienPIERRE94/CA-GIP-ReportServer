# CA-GIP – SSRS Migration kit

PowerShell scripts and documentation to migrate a SQL Server Reporting Services (SSRS) instance away from an end-of-life server, using the Microsoft-supported approach (database + encryption key restore).

## Contents

```
docs/
  Guide_Migration_SSRS_EN.md     ← full migration guide (English)
  Guide_Migration_SSRS_EN.docx   ← same, Word version
  Mail_SSRS_Migration_EN.md      ← email template to share the kit
scripts/
  ssrs/
    README.md                    ← script index
    ssrs-config.sample.ps1       ← config template (duplicate as ssrs-config.ps1)
    01-Audit-SSRS.ps1            ← inventory (read-only, RELIABLE)
    02-Backup-SSRS.ps1           ← BACKUP DATABASE + rskeymgmt -e (RELIABLE)
    03-Restore-SSRS.ps1          ← RESTORE DATABASE + rskeymgmt -a (RELIABLE)
    04-Migrate-SSRSPermissions.ps1   ← OPTIONAL / EXAMPLE
    05-Migrate-SSRSDataSources.ps1   ← OPTIONAL / EXAMPLE
    06-Migrate-SSRSReports.ps1       ← OPTIONAL / EXAMPLE
    07-Migrate-SSRSSubscriptions.ps1 ← OPTIONAL / EXAMPLE
    08-Test-SSRSReports.ps1      ← URL-Access render tests (RELIABLE)
    09-Switch-SSRSProduction.ps1     ← OPTIONAL / EXAMPLE (cutover automation)
```

## Recommended migration path

The supported Microsoft path is:

1. **Audit** the source instance with `01-Audit-SSRS.ps1`.
2. **Back up** the `ReportServer` and `ReportServerTempDB` databases + encryption key with `02-Backup-SSRS.ps1`.
3. **Install** the new SSRS instance and **restore** the databases + key with `03-Restore-SSRS.ps1`.
4. **Validate** the new instance: re-run the audit, then `08-Test-SSRSReports.ps1` against critical reports.
5. **Cut over** manually (DNS switch + subscription enable/disable via T-SQL on `dbo.Subscriptions.InactiveFlags`).

When the database is restored, **all metadata is migrated automatically** (folders, reports, datasources, subscriptions, permissions, custom roles).

The `04` / `05` / `06` / `07` / `09` scripts target a clean-install scenario where the database is not restored as-is. They are **provided as examples** and must be tested on a non-prod environment before any production use. See Appendix E of the guide.

## Prerequisites

```powershell
Install-Module SqlServer            -Scope CurrentUser
# Only required if you need the optional / example scripts:
Install-Module ReportingServicesTools -Scope CurrentUser
```

The `rskeymgmt.exe` and `rsconfig.exe` utilities ship with SSRS.

## Usage

See [docs/Guide_Migration_SSRS_EN.md](docs/Guide_Migration_SSRS_EN.md) for the full procedure, RACI, monitoring queries, rollback plan and links to official Microsoft documentation.

## License

Internal use – CA-GIP BI Governance team.

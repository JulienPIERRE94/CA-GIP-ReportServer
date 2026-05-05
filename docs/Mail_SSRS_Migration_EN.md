# Email – SSRS Migration kit delivery

**To:** Sabrina <sabrina@company.local>
**Cc:** Saurabh <saurabh@company.local>; BI Governance team
**Subject:** SSRS migration – ready-to-use guide and PowerShell scripts (9 steps)

---

Hi Sabrina,

Following your request regarding the SSRS migration driven by the obsolescence of the current server, please find below a complete migration kit aligned with the 9 steps you listed.

**Documentation**

- English guide (DOCX): `docs/Guide_Migration_SSRS_EN.docx`
- Source markdown (versioned): `docs/Guide_Migration_SSRS_EN.md`

The guide covers prerequisites, invocation examples, a RACI matrix, watchpoints (TLS, SPN/Kerberos, custom assemblies, licensing) and a rollback plan.

**PowerShell scripts** – one script per step, located in `scripts/ssrs/`:

| # | Step | Script |
|---|---|---|
| 1 | Audit – inventory of reports, data sources, subscriptions, permissions | `01-Audit-SSRS.ps1` |
| 2 | Backup – databases (`ReportServer`, `ReportServerTempDB`), encryption key, configuration files | `02-Backup-SSRS.ps1` |
| 3 | Restore on the new server + reapply encryption key | `03-Restore-SSRS.ps1` |
| 4 | Access migration – export/import permissions and policies | `04-Migrate-SSRSPermissions.ps1` |
| 5 | Shared data sources – copy + connection-string remap | `05-Migrate-SSRSDataSources.ps1` |
| 6 | Reports – RDL/RSDS download/upload + folder hierarchy + data source rebinding | `06-Migrate-SSRSReports.ps1` |
| 7 | Subscriptions – export/import including schedules and email remap | `07-Migrate-SSRSSubscriptions.ps1` |
| 8 | Validation – batch report rendering with HTTP and duration checks | `08-Test-SSRSReports.ps1` |
| 9 | Production cutover – disable old subs, DNS switch, enable new subs, user communication | `09-Switch-SSRSProduction.ps1` |

**Key points**

- Required modules: `ReportingServicesTools` and `SqlServer` (PowerShell Gallery).
- Guiding principle: restore the `ReportServer` / `ReportServerTempDB` databases AND reapply the `.snk` encryption key on the target. This keeps stored connection strings working without retyping credentials.
- Data-driven subscriptions are flagged explicitly and must be reviewed manually (data source + parameter query).
- Security: every script that writes to the target supports `-WhatIf` / `-Confirm`. The cutover script is set to `ConfirmImpact = High`.
- Post-migration monitoring: a ready-to-use `ExecutionLog3` query is provided and can be plugged into the existing governance Power BI dashboard (`powerbi/api-monitoring/`).

**Next steps – please confirm**

1. Source and target SSRS versions (e.g. SSRS 2016 → SSRS 2022).
2. Topology: single-server or scale-out deployment.
3. Target maintenance window for steps 2, 3 and 9.
4. SMTP server and distribution list to be used for the user communication step.

The scripts are independent: if Saurabh has already started some steps (e.g. audit), we can resume the migration from any step without rework.

Please let me know if you would like a working session to walk through the kit and align the schedule with the production team.

Best regards,
Julien
BI / Power BI Governance team

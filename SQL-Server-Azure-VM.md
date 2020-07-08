# SQL Server Azure VM (IaaS)

## Management

### SQL Resource Provider and SQL IaaS Extension
- Recommend you register any virtual machines which are running SQL Server workloads with the [SQL Resource Provider](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=powershell) and Install the SQL Server IaaS Extension. 
- Registering with the resource provider creates the SQL virtual machine resource within your subscription, which is a separate resource from the virtual machine resource.
- Registering your SQL Server VM with the resource provider requires installing the SQL Server IaaS Extension which unlocks automated patching, automated backup, and monitoring and manageability capabilities. It also unlocks licensing and edition flexibility. Previously, these features were available only to SQL Server VM images from Azure Marketplace.
- SQL IaaS extension has three management modes Lightweight (license type/edition management only), Full , No Agent (for SQL2008/SQL2008R2 on W2008).
- Steps to [install the SQL Resource Provider](https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-vm-resource-provider-register?tabs=azure-cli%2Cpowershell#azure-portal). Note: This may restart the SQL Server service.
- Steps to [install SQL Server IaaS Agent Extension in Full mode](https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management#installation). Note: This may restart the SQL Server service.
- Registering a VM with the SQL Resource provider automatically installs the SQL Server IaaS extension. But the reverse is not true. So, installing the SQL Server IaaS extension alone does not automatically register the VM with the SQL Resource Provider.
- [Bulk Register](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-vm-resource-provider-bulk-register) multiple SQL virtual machines in Azure with the SQL VM resource provider.
  - The Register-SqlVMs cmdlet can be used to register all virtual machines in a given list of subscriptions, resource groups, or a list of specific virtual machines. The cmdlet will register the virtual machines in lightweight management mode, and then generate both a report and a log file.

## [Backup](https://docs.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/backup-restore#backup-and-restore-options)

### 1. Manual
- Custom backup such as SQL Agent/PowerShell backup scripts or 3rd party SQL Server backup solution.
- VLDB Backup options:
  - [File-Snapshot Backups for Database Files in Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure?view=sql-server-ver15). SQL Server File-snapshot backup uses Azure snapshots to provide nearly instantaneous backups and quicker restores for database files stored using the Azure Blob storage service. [Video demonstration](https://channel9.msdn.com/Blogs/Azure/File-Snapshot-Backups-Demo) of Azure File Snapshot Backups for SQL Server
  - [Back Up Files and Filegroups + Partitioning](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server?view=sql-server-ver15). When the database size and performance requirements make a full database backup impractical, you can create a file backup instead.

### 2. SQL IaaS Extension’s Automated Backup
- SQL IaaS Extension’s [Automated Backup](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery#automated) feature. This feature must be managed and monitored at the individual VM level.

### [3. Azure Backup](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery#azbackup)
 - Allows you to manage multiple servers within one dashboard.
 - This short [video](https://www.youtube.com/watch?time_continue=4&v=wmbANpHos_E) gives a great overview of what this solution looks like
 - Zero-infrastructure backup: You do not have to manage backup servers or storage locations.
 - Scale: Protect many SQL VMs and thousands of databases.
 - Central management and monitoring: Centrally manage all of your backups, including other workloads that Azure Backup supports, from a single dashboard in Azure. Use the portal to recover databases to a specific point in time without having to manually restore multiple full, differential, and log backups.
 - Support for SQL Always On: Detect and protect a SQL Server Always On configuration and honor the backup Availability Group backup preference
 - Policy driven backup and retention: Create standard backup policies for regular backups. Establish retention policies to maintain backups for years.
 - 15-minute Recovery Point Objective (RPO): Configure SQL transaction log backups up to every 15 minutes.
 - Consolidated email alerts for failures: Configure consolidated email notifications for any failures.
 - [Azure Backup Considerations](https://docs.microsoft.com/en-us/azure/backup/backup-azure-sql-database#backup-process)
   - Once you specify the SQL Server VM that you want to protect and query for the databases in it, Azure Backup service will install a workload backup extension on the VM by the name AzureBackupWindowsWorkload extension.
   - To be able to discover databases on a VM, Azure Backup creates the account NT SERVICE\AzureWLBackupPluginSvc. This account is used for backup and restore and requires SQL sysadmin permissions
   - Recommended: Please review the supported operating systems and SQL Server versions as well as the feature considerations and limitations listed here: [Support matrix for SQL Server Backup in Azure VMs](https://docs.microsoft.com/en-us/azure/backup/sql-support-matrix#back-up-behavior-with-always-on-availability-groups)
 - [Back up behavior for Always On Availability Groups](https://docs.microsoft.com/en-us/azure/backup/sql-support-matrix#back-up-behavior-with-always-on-availability-groups)
 - Walkthrough: [How to Configure Azure Backup for SQL Server databases in Azure VMs](https://docs.microsoft.com/en-us/azure/backup/backup-sql-server-database-azure-vms)
 - Tutorial - [Back up a SQL Server database in an Azure VM](https://docs.microsoft.com/en-us/azure/backup/tutorial-sql-backup)
 - How to [Restore Azure Backup Protected Databases]([https://](https://docs.microsoft.com/en-us/azure/backup/restore-sql-database-azure-vm)link)
 - [Retention](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix#retention-limits)
   - Azure Backup has a limit of [9999 recovery points](https://docs.microsoft.com/en-us/azure/backup/backup-azure-backup-faq#is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created), also known as backup copies or snapshots, per protected instance.
   - Your backup policy configuration determines how quickly you consume the recovery points. For example, if you create a recovery point each day, then you can retain recovery points for 27 years before you run out. If you take a monthly recovery point, you can retain recovery points for 833 years before you run out.
 - [Manage and monitor](https://docs.microsoft.com/en-us/azure/backup/manage-monitor-sql-database-backup) backed up SQL Server databases
 - PowerShell
   - [Enable backup protection for an item](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection?view=azps-2.8.0)
   - [Disable backup protection for an item](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.8.0)
 - If you are having trouble with Azure Backup detecting your SQL Instances, please review these articles which walk through VM permission and connectivity requirements:
   - [Establish network connectivity](https://docs.microsoft.com/en-us/azure/backup/tutorial-sql-backup#establish-network-connectivity)
   - [Set VM permissions](https://docs.microsoft.com/en-us/azure/backup/tutorial-sql-backup#set-vm-permissions)
   - [Database naming guidelines](https://docs.microsoft.com/en-us/azure/backup/tutorial-sql-backup#verify-database-naming-guidelines-for-azure-backup)
   - Azure Backup leverages the [NT AUTHORITY\SYSTEM](https://docs.microsoft.com/en-us/azure/backup/backup-azure-sql-database#backup-process) account for database discovery/inquiry, so this account needs to be a public login on SQL.


## Monitoring

### 1. Manual
- Custom monitoring such as SQL Agent/PowerShell scripts and 3rd party monitoring tools

### 2. Guest VM Azure Metrics (Currently in Preview)
- [Azure Diagnostics extension](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostics-extension-overview) is an agent in Azure Monitor that collects monitoring data from the guest operating system of Azure compute resources including virtual machines.
- Collects guest metrics (typically time series/performance counter)  into Azure Monitor Metrics.
- [Data which can be collected](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostics-extension-overview#data-collected)
- It can send data to Azure Storage, Azure Monitor Metrics (Windows VM only) and Event Hubs.

### 3. Azure Monitor Logs (aka Azure Log Analytics)
- Uses the [Log Analytics agent](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/log-analytics-agent) (aka MicrosoftMonitoringAgent, MMA)
- Collects data to a Log Analytics workspace (aka Azure Monitor Logs)
- To ship SQL Server Audit logs to Azure Monitor Logs, you can choose to write audit logs to the Windows event Logs and then use the Log Analytics agent to collect the event logs. This approach is documented here
- Specify [custom list of performance counters](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-sources-performance-counters) to collect. You can either select a counter from the list or type in one of your own.
- You can also [write custom logs to Azure Monitor Logs through the agent via custom data sources](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-sources-custom-logs) or manually through [REST API](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-collector-api).
- [SQL Health Check solution](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/sql-assessment#prerequisites) for Azure Monitor Logs (aka Log Analytics). This also relies on data collected by the Log Analytics agent (aka MicrosoftMonitoringAgent, MMA)

### 4. Security Monitoring
- [Advanced data security for SQL machines (Preview)](https://docs.microsoft.com/en-us/azure/security-center/security-center-iaas-advanced-data). Includes Advanced Threat Protection and Vulnerability Assessment

### Reference: Key SQL Server Performance Counters
- **I/O: IOPS**. Disk Reads/sec, Disk Writes/sec
- **I/O: Throughput**. Disk Read Bytes/Sec, Disk Write Bytes/Sec, Disk Bytes/Sec
- **I/O: Latency**. Avg. Disk sec/Read, Avg. Disk sec/Write
- **CPU**. Processor. % Processor Time
- **Memory**.
   - Memory. Available Megabytes
   - SQL Server: Memory Manager. Total Server Memory (KB),   SQLServer:Memory Manager\Target Server Memory (KB),    SQL Server Buffer Manager: Page Life Expectancy
- **Waits**. SQL Server: Wait Statistics. Lock waits, Log buffer waits, Log write waits, Memory grant queue waits, Network IO waits, Non-Page latch waits, Page IO latch waits, Page latch waits, Thread-safe memory objects waits, Transaction ownership waits, Wait for the worker, Workspace synchronization waits
- **Query/Usage Patterns**.
   - SQLServer:Workload Group Stats. Max request memory grant (KB), Active requests, Blocked requests, Max request CPU time (ms), Query optimizations/sec
   - SQLServer:SQL Statistics. Batch Requests/sec, SQL Attention rate, SQL Compilations/sec, SQL Re-Compilations/sec
   - SQLServer:Access Methods. Full Scans/sec, Index Searches/sec, Page Splits/sec
   - SQLServer:Batch Resp Statistics. Batches >=050000ms & <100000ms , Batches >=100000ms
   - SQLServer:General Statistics. User Connections, Transactions
- **T-Log**. SQLServer:Databases. Log Bytes Flushed/sec, Percent Log Used, Transactions/sec
- **Tempdb Activity**:
   - SQLServer:Access Methods. Workfiles Created/sec, Worktables Created/sec
- **Always On Availability Groups**:
   - SQL Server: Database Replica. Transaction Delay, Mirrored Write Transaction/sec, Log Send Queue
- **Errors**.
   - SQLServer:SQL Errors. DB Offline Errors, Kill Connection Errors
   - SQLServer:General Statistics. Processes blocked
- [Monitor Resource Usage (System Monitor)](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/monitor-resource-usage-system-monitor?view=sql-server-ver15)
- [SQL Server Performance Object reference](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-ver15#SQLServerPOs)
- [Monitor SQL Server Resource usage](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/monitor-resource-usage-system-monitor?view=sql-server-ver15)
- [Monitor performance for Always On availability groups](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups?view=sql-server-ver15)

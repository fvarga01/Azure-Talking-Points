# Azure SQL Database

## Single and Managed Instance

### Pricing
- [Azure Hybrid Benefit Savings Calculator](https://azure.microsoft.com/en-us/pricing/hybrid-benefit/#sql-database)
  - [FAQ](https://azure.microsoft.com/en-us/pricing/hybrid-benefit/faq/), see section: “How does the Azure Hybrid Benefit work with Azure SQL Database
- Save costs for SQL Database compute resources with Azure SQL Database [reserved capacity](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-reserved-capacity)
- [Enterprise Dev/Test subscription](https://azure.microsoft.com/en-us/offers/ms-azr-0148p/)
  - Provides special lower Dev/Test rates
  - Only users with MSDN (Visual Studio) subscription users would be able to access a Dev/Test subscription: https://azure.microsoft.com/en-us/offers/ms-azr-0148p/
  - Azure SQL Database managed Instance is currently supported on [these subscription types](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits)

### Feature Comparison
- Azure SQL Database (ASDB): [Single Database vs Managed Instance]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-paas-vs-sql-server-iaas)
  - Single 
    - Most commonly used SQL Server features are available
    - Ability to assign necessary resources (CPU/storage) to individual databases.
    - Supports private link via [private endpoint](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-private-endpoint-overview)
  - Managed Instance
    - High compatibility with SQL Server features on-premises
    - Private IP address, injected within Azure VNet
    - Supports [public endpoints](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) as well, should you want applications to connect from outside your virtual network
  - [Feature Comparison with SQL Server](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-features)
    - [Managed instance T-SQL differences, limitations, and known issues](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-transact-sql-information)
    - Note: SQL Server Agent, Cross database-queries, linked servers, server logins, Transparent Data Encryption

### Business Continuity
- High Availability [Architecture Differences Between General Purpose and Business Critical](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-high-availability)
  - Local HA is built-into ASDB: Single + MI. 
  - General Purpose Tier
    - Replication of compute. Conceptionally similar to a SQL Server failover clustered instance.
  - Business Critical Tier
    - Replication of compute and (local SSD) storage.
    - Local synchronous replicas. Conceptionally similar to a SQL Server always on availability group.
    - This edition has fast local SSD storage. 
    - Read-Only replica is available at no extra cost for reporting queries.
    - Zone redundant configuration is available in certain regions.
- Remote BCDR is provided by the [Failover Group](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auto-failover-group) feature
  - When created for SQL Managed Instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  - Auto-failover groups support replication of all databases in the group to only one secondary server in a different region
  - You can failover a failover group manually. You can also define an automatic failover policy (which defines a grace period) so that any outage which impacts one or several of the databases in the group results in automatic failover. The SQL Database service triggers failover after the failure is detected and the grace period has expired. If an outage is detected, SQL waits for the period you specified by GracePeriodWithDataLossHours. The default value is 1 hour. If you cannot afford **data loss**, make sure to set GracePeriodWithDataLossHours to a sufficiently large number, such as 24 hours.
  - Primary and secondary servers for the databases in the failover group must be in the same subscription
  - Consider dependencies external to the databases such as VNET's, server level logins, and server level firewall rules. Some of these are outlined [here](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-business-continuity#perform-post-failover--recovery-tasks).
  - [Failover Group SLAs](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-business-continuity#recover-a-database-to-the-existing-server)
  - This article discusses common application patterns used to create globally distributed applications optimized for local access to the data, including the benefits and trade-offs of each option: [Designing globally available services using Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery).

- [Automated Backups](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups)
  - _"SQL Database uses SQL Server technology to create full backups every week, differential backups every 12 hours, and transaction log backups every 5-10 minutes. The backups are stored in RA-GRS storage blobs that are replicated to a paired data center for protection against a data center outage. When you restore a database, the service figures out which full, differential, and transaction log backups need to be restored."_
  - [Azure Paired Regions](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)
  - [Backup Storage Costs](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups#storage-costs)
    - 100% of database size is provided at no extra charge
- [Backup Integrity](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups#how-does-microsoft-ensure-backup-integrity)
  - On an ongoing basis, the [Azure SQL Database engineering team automatically tests the restore](https://azure.microsoft.com/en-us/blog/data-integrity-in-azure-sql-database/) of automated database backups of databases across the service. Upon restore, databases also receive integrity checks using DBCC CHECKDB. Any issues found during the integrity check will result in an alert to the engineering team.
- [Long Term Backup Retention](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-long-term-backup-retention-configure)
  - Up to 10 years for single db. Currently in public preview for managed instance
- [Point In Time Restore](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore). Restore a database to a point-in-time within the retention period. This operation will create a new database in the same server as the original database.
- [Restore a deleted database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups#deleted-database-restore). 	○ Restore a deleted database to the time it was deleted or any time within the retention period. The deleted database can only be restored in the same server where the original database was created.
- [Export a Single DB Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-export). When you need to export a database for archiving or for moving to another platform, you can export the database schema and data to a BACPAC file. A BACPAC file is a ZIP file with an extension of BACPAC containing the metadata and data from the database. 
- [Copy Only Backups](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/copy-only-backups-sql-server?view=sql-server-ver15) are available in SQL Managed Instance
- [Geo-Restore](https://docs.microsoft.com/en-us/azure/azure-sql/database/recovery-using-backups#geo-restore-by-using-the-azure-portal) allows you to restore a database to another geographical region.

### Maintenance
- Azure automatically handles patching, backups, failure detection, underlying potential hardware, software or network failures, deploying bug fixes, failovers, database upgrades, and other maintenance tasks.
- [Planned Maintenance](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-planned-maintenance#what-to-expect-during-a-planned-maintenance-event)
  - See section “What to expect during a planned maintenance event…"
  - It is recommended that you [add connection retry logic in your applications](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connectivity-issues#retry-logic-for-transient-errors)
  - [Hot patching SQL Server Engine in Azure SQL Database](https://azure.microsoft.com/en-us/blog/hot-patching-sql-server-engine-in-azure-sql-database/)

### Migration
- [Azure Database Migration Guide](https://datamigration.microsoft.com)
- [Azure Database Migration Service](https://docs.microsoft.com/en-us/azure/dms/dms-overview) allows for online migrations
  - [Migrate SQL Server to SQL Database Managed Instance Online using DMS](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-managed-instance-online)
  - [Migrate SQL Server to SQL Database  single DB Online using DMS](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-azure-sql-online)
- [Import a Database via BacPac](https://docs.microsoft.com/en-us/azure/azure-sql/database/database-import?tabs=azure-powershell)
- [Restore a SQL Server Backup to SQL Managed Instance](https://docs.microsoft.com/en-us/azure/azure-sql/managed-instance/migrate-to-instance-from-sql-server#native-restore-from-url)
- Sizing your database workload for SQL Managed Instance
  - This article discusses the [resource limits for ASDB MI](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#instance-level-resource-limits)
  - The easiest approach would be to use the [DTU calculator](http://dtucalculator.azurewebsites.net/). Managed instance only supports VCore (not DTU), but you can easily convert DTUs to vCores.
  - You can also use custom perf counters or the [SkuRecommendationDataCollectionScript.ps1 script](https://docs.microsoft.com/en-us/sql/dma/dma-sku-recommend-sql-db?view=sql-server-ver15) which is part of the Database Migration Assistant.
  - Some key [Sysmon performance counters](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/monitor-resource-usage-system-monitor?view=sql-server-ver15) for OLTP workloads
    - IOPS: LogicalDisk: Disk Reads/sec, LogicalDisk: Disk Writes/sec
    - IO Latency: Avg. Disk sec/Read, Avg. Disk sec/Write
    - Database t-log activity: Log Bytes Flushed/sec
    - CPU: Processor - % Processor Time
    - Memory:    Memory: Available Megabytes,    SQL Server: Memory Manager: Total Server Memory (KB),    \SQLServer:Memory Manager\Target Server Memory (KB),    SQL Server Buffer Manager: Page Life Expectancy 
    - [SQL Server Performance Objects](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-ver15#SQLServerPOs)
- [Database Experimentation Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54090) is an A/B testing solution for SQL Server upgrades. It will assist in evaluating a targeted version of SQL for a given workload


## Single Instance

### Secure
- [What is the difference between VNET Service Endpoint and Private Endpoint](https://docs.microsoft.com/en-us/azure/private-link/private-link-faq#what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints)
  - _"When using Private Endpoints, network access is granted to specific resources behind a given service providing granular segmentation, also traffic can reach the service resource from on premises without using public endpoints. A service endpoint remains a publicly routable IP address. A private endpoint is a private IP in the address space of the virtual network where the private endpoint is configured."_
  - [Steps to enable Private Endpoint](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)
  - [Steps to enable VNET Service Endpoint connectivity](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#azure-portal-steps)
  - In the Azure portal, when the [Deny Public Network Access](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connectivity-settings#deny-public-network-access) setting is set to Yes, only connections via private endpoints are allowed. When this setting is set to No, clients can connect using the private or public (vnet service) endpoint. 
- [Authentication](https://docs.microsoft.com/en-us/azure/azure-sql/database/logins-create-manage)
  - SQL Authentication vs AAD Authentication
  - Single DB supports DB level AAD Users
  - An Azure AD administrator must be configured if you want to use Azure AD accounts to connect to SQL Database, SQL Managed Instance, or Azure Synapse
  - [Only the administrator based on an Azure AD account can create the first Azure AD contained database user in a user database]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure?view=sql-server-2017)
  - [Azure AD server level principals (logins) and users are supported for SQL Managed Instance](https://docs.microsoft.com/en-us/azure/azure-sql/database/authentication-aad-overview).
- Authorization
  - [Database Roles](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/database-level-roles)
- [Auditing for Azure SQL Database and Azure Synapse Analytics]( https://docs.microsoft.com/en-us/azure/azure-sql/database/auditing-overview)
- [Azure SQL Managed Instance auditing](https://docs.microsoft.com/en-us/azure/azure-sql/managed-instance/auditing-configure)

### Business Continuity
- [Failover group vs Geo-Replication](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-business-continuity#compare-geo-replication-with-failover-groups)

### Monitoring
- [Azure Metrics](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). You can pin metrics to an Azure Dashboard.
- [Stream Diagnostic Logs](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-metrics-diag-logging?tabs=azure-portal) to Log Analytics for centralized longer term monitoring
  - SQL Log Analytics/Intelligent Insights
    - [SQL Analytics Solution](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/azure-sql). If you configure [Intelligent Insights](https://docs.microsoft.com/en-us/azure/azure-sql/database/intelligent-insights-overview#how-does-intelligent-insights-work) log data to be streamed to Azure SQL Analytics workspace, Azure can use built-in intelligence to detect and provide detailed analysis of disruptive events that cause poor performance.
- [Query Performance Insights](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-query-performance) provides intelligent query analysis for single and pooled databases.
- [Query Store](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-query-performance) can be accessed through SSMS + Azure Portal provides intelligent query analysis. This is used by the Query Performance Insights feature.
- [Extended Events](https://docs.microsoft.com/en-us/azure/azure-sql/database/xevent-db-diff-from-svr)  + [DMVs](https://docs.microsoft.com/en-us/azure/azure-sql/database/monitoring-with-dmvs) for advanced troubleshooting and monitoring.

### Scale: Tuning
- Can scale tier manually or programmatically
- Within an elastic pool, individual databases are given the flexibility to [auto-scale](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-pool) within set parameters
- [Query Tuning Guidance](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-monitor-tune-overview)
- Consider using [columnstore indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/columnstore-indexes-design-guidance?view=sql-server-ver15#choose-the-best-columnstore-index-for-your-needs) for your larger analytics tables, non-clustered columnstore indexes are available for HTAP tables.
- [Automatic tuning](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning)
- [Database Advisor Performance Recommendations](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-advisor)
- [Update Statistics/Defragment/Re-index](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-maintain-azure-sql-indexes-and-statistics/ba-p/368787)
  - [Update Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/statistics/update-statistics?view=sql-server-ver15)
  - [Rebuild Indexes](ttps://docs.microsoft.com/en-us/sql/t-sql/statements/alter-index-transact-sql?view=sql-server-ver15#rebuilding-indexes)
- [Database Compatibility Level 15](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Memory-Grant-Feedback-SQL-2019-new-feature-applied-to-Azure-SQL/ba-p/1020997)
  - Memory Grant Feedback - SQL 2019 new feature applied to Azure SQL DB
  - SQL Server 2019 includes built-in query processing capabilities called Intelligent Query Processing. By updating your database compatibility level to 150 (the default level for SQL Server 2019), the query processor in the SQL Server engine can enhance performance through capabilities like batch-mode on row store, scalar UDF inlining,or table variable deferred compilation. 
  - It can automatically correct memory-related query execution issues through memory grant feedback.

### Scale: Data Replication
- You can configure an Azure SQL Database as the push subscriber in a one-way transactional or snapshot replication [replication](https://docs.microsoft.com/en-us/azure/azure-sql/database/replication-to-sql-database) topology. The SQL Server acting as publisher and/or distributor can be an instance of SQL Server running on-premises, an Azure SQL Managed Instance, or an instance of SQL Server running on an Azure virtual machine in the cloud.
- [SQL Data Sync](https://docs.microsoft.com/en-us/azure/azure-sql/database/sql-data-sync-data-sql-server-sql-database)is a service built on Azure SQL Database that lets you synchronize the data you select bi-directionally across multiple databases, both on-premises and in the cloud.

### Scale: Data Distribution + Partitioning
- [Sharding](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-scale-introduction). Scale out databases in Azure SQL Database using the Elastic Database tools. Sharding is a technique to distribute large amounts of identically structured data across a number of independent databases. It is especially popular with cloud developers creating Software as a Service (SAAS) multi-tenant offerings for end customers or businesses. Sharding works best when every transaction in an application can be restricted to a single value of a sharding key. That ensures that all transactions are local to a specific database.
- [Partitioned Indexed View](https://docs.microsoft.com/en-us/sql/relational-databases/views/create-indexed-views?redirectedfrom=MSDN&view=sql-server-ver15). Indexed views can also be created on a partitioned table, and can themselves be partitioned.
- [Filtered Indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes?view=sql-server-ver15)
- Partitioned Tables and Indexes
  - Identify which indexes to partition.  - Indexes are put in place to optimize queries. So, first you’ll want to identify the top queries and the top indexes being used in your environment.
  - Partition indexes which will (1) benefit from partition elimination to help improve query performance or (2) benefit from reduced impact from  maintenance tasks (such as reindexing, statistics rebuild, and bulk loads) by moving to partition level operations instead.
  - [Performance Guidelines](https://docs.microsoft.com/en-us/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-ver15#queries) “If you frequently run queries that involve an equi-join between two or more partitioned tables, their partitioning columns should be the same as the columns on which the tables are joined”
  - Be aware of limitations when using non-aligned partitioned indexes. 
    - Creating and rebuilding nonaligned indexes on a table with [more than 1,000 partitions is possible, but is not supported](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-index-transact-sql?view=sql-server-ver15#partitioned-indexes). Doing so may cause degraded performance or excessive memory consumption during these operations. We recommend using only aligned indexes when the number of partitions exceed 1,000… column_name must be chosen from among those used as the unique key. This restriction allows the Database Engine to verify uniqueness of key values within a single partition only… You cannot change the compression setting of a single partition if the table has nonaligned indexes.
    - [Some operations such as switch will not be possible with non-aligned indexes](https://docs.microsoft.com/en-us/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-ver15#non-aligned-index). When a table and its indexes are in alignment, SQL Server can switch partitions quickly and efficiently while maintaining the partition structure of both the table and its indexes.
    - [To switch partitions, all nonclustered indexes must include the partition key](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql?view=sql-server-ver15)


  |  | Partition Switch Operations Allowed?   | Per-Partition Operations Allowed? (Truncate/ Compression/ Reindex/ Reorg) | 
  |:---|:---|:---|
  | Partition Aligned| Y | Y |
  | Non-Partition Aligned (clustered index/heap is partitioned + non-clustered indexes are not partitioned)  | Y, but must disable NC indexes | Y, but must disable NC indexes  |
  | Non-Partition Aligned (clustered index/heap is not partitioned + non-clustered indexes are partitioned)  | N | N |

  - [Walkthrough: Partitioned Table Demonstration](https://github.com/fvarga01/sample-code/blob/master/AzureSQLDatabase/DemoPartitionedTables.sql)

## Managed Instance

### Scale: Tuning
- Throughput and IOPS in the General Purpose Tier [also depend on the file size](https://docs.microsoft.com/en-us/azure/azure-sql/managed-instance/resource-limits#file-io-characteristics-in-general-purpose-tier). If you notice high IO latency on some database file or you see that IOPS/throughput is reaching the limit, you might improve performance by increasing the file size.


## Hyperscale
- Time to resize compute is independent of the size of data
- Decouple storage, compute, transaction logs
- [Use](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tier-hyperscale#who-should-consider-the-hyperscale-service-tier ) for VLDBs and when you need flexibility to scale compute/storage while maintaining many of the performance benefits of business critical (fast IOPS)
- [Capabilities](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tier-hyperscale#what-are-the-hyperscale-capabilities) include Constant time operations (backups, restores, scale), support for VLDBs, ability to provision one or more read-only/hot-standby nodes, and improved performance due to higher log throughput and faster transaction commit times
- [Distributed Architecture](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)
- Migration to Hyperscale tier is currently [one-way](https://docs.microsoft.com/en-us/azure/azure-sql/database/service-tier-hyperscale#upgrade-existing-database-to-hyperscale)
- [Connect to READ replica](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tier-hyperscale#connect-to-a-read-scale-replica-of-a-hyperscale-database)


## Serverless
- Automatically scales compute [based on workload demand](https://docs.microsoft.com/en-us/azure/azure-sql/database/serverless-tier-overview) and bills for the amount of compute used per second.
- [When to use](https://docs.microsoft.com/en-us/azure/azure-sql/database/serverless-tier-overview#scenarios-well-suited-for-provisioned-compute)
- [Autoscaling performance/responsiveness](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-serverless#scaling-responsiveness)
- [Video: Serverless Architecture](https://youtu.be/2ykwUOfEPoU?t=578) shows 	three tiers (1) control plan manages connectivity and management ops (2) Backend tier (Data Plane) with database compute (3) Storage tier where files reside


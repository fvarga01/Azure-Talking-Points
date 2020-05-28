# Azure SQL Database

## Single and Managed Instance

### Pricing
- Azure Hybrid Benefit Savings Calculator https://azure.microsoft.com/en-us/pricing/hybrid-benefit/#sql-database
  - FAQ https://azure.microsoft.com/en-us/pricing/hybrid-benefit/faq/, see section: “How does the Azure Hybrid Benefit work with Azure SQL Database
- Save costs for SQL Database compute resources with Azure SQL Database reserved capacity https://docs.microsoft.com/en-us/azure/sql-database/sql-database-reserved-capacity
- Enterprise Dev/Test subscription https://azure.microsoft.com/en-us/offers/ms-azr-0148p/
  - Provides special lower Dev/Test rates
  - Only users with MSDN(Visual Studio) subscription users would be able to access a Dev/Test subscription: https://azure.microsoft.com/en-us/offers/ms-azr-0148p/
  - Azure SQL Database managed Instance is currently supported on the following subscription types https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits

### Feature Comparison
- Azure SQL Database (ASDB): Single Database vs Managed Instance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-paas-vs-sql-server-iaas
  - Single 
    - Most commonly used SQL Server features are available
    - Ability to assign necessary resources (CPU/storage) to individual databases.
    - Supports private link via private endpoint: https://docs.microsoft.com/en-us/azure/sql-database/sql-database-private-endpoint-overview
  - Managed Instance
    - High compatibility with SQL Server features on-premises
    - Private IP address, injected within Azure VNet
    - Supports public endpoints as well, should you want applications to connect from outside your virtual network: https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-public-endpoint-configure
  - Recommend __thoroughly__ reviewing these two articles:
    - Feature Comparison with SQL Server https://docs.microsoft.com/en-us/azure/sql-database/sql-database-features
    - Managed instance T-SQL differences, limitations, and known issues https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-transact-sql-information
    - Note: SQL Server Agent, Cross database-queries, linked servers, server logins, Transparent Data Encryption

### Business Continuity
- High Availability Architecture Differences Between General Purpose and Business Critical https://docs.microsoft.com/en-us/azure/sql-database/sql-database-high-availability
  - Local HA is built-into ASDB: Single + MI. 
  - General Purpose Tier:  Replication of compute. Conceptionally similar to a SQL Server failover clustered instance. 
  - Business Critical Tier: Replication of compute and (local SSD) storage. Conceptionally similar to a SQL Server always on availability group
- Automated Backups https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups
  - _"SQL Database uses SQL Server technology to create full backups every week, differential backups every 12 hours, and transaction log backups every 5-10 minutes. The backups are stored in RA-GRS storage blobs that are replicated to a paired data center for protection against a data center outage. When you restore a database, the service figures out which full, differential, and transaction log backups need to be restored."_
  - Business continuity and disaster recovery (BCDR): Azure Paired Regions https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions 
b.	Storage Costs https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automated-backups#storage-costs

### Maintenance
- Azure automatically handles patching, backups, failure detection, underlying potential hardware, software or network failures, deploying bug fixes, failovers, database upgrades, and other maintenance tasks.
- Planned Maintenance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-planned-maintenance#what-to-expect-during-a-planned-maintenance-event
  - See section “What to expect during a planned maintenance event…"
  - It is recommended that you add connection retry logic in your applications: https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connectivity-issues#retry-logic-for-transient-errors
  - Hot patching SQL Server Engine in Azure SQL Database https://azure.microsoft.com/en-us/blog/hot-patching-sql-server-engine-in-azure-sql-database/

### Migration
- Sizing your database workload for SQL Managed Instance
  - This article discusses the resource limits for ASDB MI. 
  - Overview Azure SQL Database managed instance resource limits https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#instance-level-resource-limits
  - The easiest approach would be to use the DTU calculator: http://dtucalculator.azurewebsites.net/. Managed instance only supports VCore (not DTU), but you can easily convert DTUs to vCores.
- You can also use custom perf counters or the SkuRecommendationDataCollectionScript.ps1 script which is part of the Database Migration Assistant:  Identify the right Azure SQL Database/Managed Instance SKU for your on-premises database https://docs.microsoft.com/en-us/sql/dma/dma-sku-recommend-sql-db?view=sql-server-ver15
- Some key Sysmon performance counters for OLTP workloads
  - IOPS: LogicalDisk: Disk Reads/sec, LogicalDisk: Disk Writes/sec
  - IO Latency: Avg. Disk sec/Read, Avg. Disk sec/Write
  - Database t-log activity: Log Bytes Flushed/sec
  - CPU: Processor - % Processor Time
  - Memory:    Memory: Available Megabytes,    SQL Server: Memory Manager: Total Server Memory (KB),    \SQLServer:Memory Manager\Target Server Memory (KB),    SQL Server Buffer Manager: Page Life Expectancy 
  - Monitor Resource Usage (System Monitor)https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/monitor-resource-usage-system-monitor?view=sql-server-ver15
  - SQL Server Performance Objects https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-ver15#SQLServerPOs

## Single Instance

### Secure
- What is the difference between VNET Service Endpoint and Private Endpoint https://docs.microsoft.com/en-us/azure/private-link/private-link-faq#what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints
  - _"When using Private Endpoints, network access is granted to specific resources behind a given service providing granular segmentation, also traffic can reach the service resource from on premises without using public endpoints. A service endpoint remains a publicly routable IP address. A private endpoint is a private IP in the address space of the virtual network where the private endpoint is configured."_
- Steps to enable Private Endpoint https://docs.microsoft.com/en-us/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database
- Steps to enable VNET Service Endpoint connectivity https://docs.microsoft.com/en-us/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#azure-portal-steps
- https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connectivity-settings#deny-public-network-access In the Azure portal, when the Deny public network access setting is set to Yes, only connections via private endpoints are allowed. When this setting is set to No, clients can connect using the private or public (vnet service) endpoint. 



### Monitoring
- Azure Metrics Dashboard (pin to dashboard)  https://docs.microsoft.com/en-us/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring
- SQL Log Analytics – Stream Diagnostic Logs to Log Analytics for centralized long term monitoring https://docs.microsoft.com/en-us/azure/sql-database/sql-database-metrics-diag-logging?tabs=azure-portal
  - SQL Log Analytics/Intelligent Insights - SQL Analytics
https://docs.microsoft.com/en-us/azure/azure-monitor/insights/azure-sql 
  - https://docs.microsoft.com/en-us/azure/sql-database/sql-database-intelligent-insights
  - https://docs.microsoft.com/en-us/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance
- Query Performance Insights https://docs.microsoft.com/en-us/azure/sql-database/sql-database-query-performance
- Query Store (you can access through SSMS, Azure Portal – Query Performance Insights, DMV’s) provides intelligent query analysis https://docs.microsoft.com/en-us/azure/sql-database/sql-database-query-performance
- [Extended Events](https://docs.microsoft.com/en-us/azure/azure-sql/database/xevent-db-diff-from-svr)  + [DMVs](https://docs.microsoft.com/en-us/azure/azure-sql/database/monitoring-with-dmvs) for troubleshooting

### Tuning
- Query Tuning Guidance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-monitor-tune-overview
- Consider using columnstore indexing for your larger analytics tables, non-clustered columnstore indexes are available for HTAP tables: https://docs.microsoft.com/en-us/sql/relational-databases/indexes/columnstore-indexes-design-guidance?view=sql-server-ver15#choose-the-best-columnstore-index-for-your-needs
- Automatic tuning https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning
- Database Advisor Performance Recommendations https://docs.microsoft.com/en-us/azure/sql-database/sql-database-advisor
- Update Statistics/Defragment/Re-index https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-maintain-azure-sql-indexes-and-statistics/ba-p/368787
  - Update Statistics https://docs.microsoft.com/en-us/sql/relational-databases/statistics/update-statistics?view=sql-server-ver15
  - Rebuild Indexes https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-index-transact-sql?view=sql-server-ver15#rebuilding-indexes
- Database Compatibility Level 15
  - Memory Grant Feedback - SQL 2019 new feature applied to Azure SQL DB
  - SQL Server 2019 includes built-in query processing capabilities called Intelligent Query Processing. By updating your database compatibility level to 150 (the default level for SQL Server 2019), the query processor in the SQL Server engine can enhance performance through capabilities like batch-mode on row store, scalar UDF inlining,or table variable deferred compilation. 
  - It can automatically correct memory-related query execution issues through memory grant feedback.
  - https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Memory-Grant-Feedback-SQL-2019-new-feature-applied-to-Azure-SQL/ba-p/1020997
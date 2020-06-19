# Azure Monitor

- [Service Health](https://docs.microsoft.com/en-us/azure/service-health/service-health-overview#get-links-and-downloadable-explanations)
  - Service Health provides you with a customizable dashboard which tracks the health of your Azure services in the regions where you use them.
  - Consider configuring service health alerts
- [Resource Health](https://docs.microsoft.com/en-us/azure/service-health/resource-health-overview)
  - Azure Resource Health helps you diagnose and get support for service problems that affect your Azure resources. It reports on the current and past health of your resources.
- [Azure Status Page](https://status.azure.com/status/)
  - Informs you of service outages. It provides a global view of the health of all Azure services across all Azure regions.
- [Activity Log](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/activity-log-collect#activity-logs-analytics-monitoring-solution)
  - Provides insight into the operations on each Azure resource in the subscription from the outside (the management plane) in addition to updates on Service Health events.
  - While you can view the Activity log in the Azure portal, you should configure it to send to a Log Analytics workspace to enable additional features of Azure Monitor. 
- [Azure Metrics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-getting-started)
  - Azure Monitor metrics explorer is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values. Use the metrics explorer to investigate the health and utilization of your resources.
  - Pin Metrics to an Azure Dashboard
- [Metric Alerts](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-metric)
  - Metric alerts in Azure Monitor provide a way to get notified when one of your metrics crosses a threshold
- [Azure Monitor Workbooks](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/workbooks-overview).
  - Workbooks provide a flexible canvas for data analysis and the creation of rich visual reports within the Azure portal.
  - They allow you to tap into multiple data sources from across Azure, and combine them into unified interactive experiences.
- [Cost management](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/quick-acm-cost-analysis)
  - You can use Azure Cost Management and Billing features to conduct billing administrative tasks and manage billing access to costs.
  - Can customize cost vews, filter by tag, export, etc.
  - [Create Budgets](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets)


## [Azure Monitor Logs](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview)/Log Analytics

- Log Analytics is a service that collects telemetry and other data from a variety of sources and provide a query language for advanced analytics. 
- Query logs for troubleshooting, visualize the data for monitoring, or even create alerts based on log search. 
- We are [updating the terminology](https://docs.microsoft.com/en-us/azure/azure-monitor/terminology#february-2019---log-analytics-terminology) to Azure Monitor Logs from Log Analytics. Log data is still stored in a Log Analytics workspace and is still collected and analyzed by the same Log Analytics service.
- You can export the results of a Log Analytics query to Power BI to use different visualizations and share with users outside of Azure.
- You can use the [HTTP Data Collector API](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/data-collector-api) (in public preview) to send custom log data to Azure Monitor
- Create and share [dashboards](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-logs-dashboards) of Log Analytics data
- Create an [alert](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-response#create-alerts) from Log Analytics query

### [Designing your Azure Monitor Logs (Log Analytics) deployment](https:/docs.microsoft.com/en-us/azure/azure-monitor/platform/design-logs-deployment)
- Avoid outbound (egress) data transfer charges by having a [log analytics] workspace in the same region as the Azure resources it manages
- Choose either a [centralized, decentralized, or an in-between hybrid approach](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/design-logs-deployment#important-considerations-for-an-access-control-strategy)

### Collect [resource logs](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-resource-logs) for an Azure Resource 
1. Create Log Analytics Workspace in Azure Monitor (recommend in the same region as the resource)
2. Enable resource log collection in [diagnostic settings](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings) for the resource
   - Metrics. Routes a resource's platform metrics into the Azure Logs store, but in log form. This option may not be available for all resource types. When it is supported, [Azure Monitor supported metrics site](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-supported) lists what metrics are collected for what resource types.
   - Logs. Routes a resource's logs into the Azure Logs store. It includes different categories which vary based on the resource type.

### View resource log data using prebuilt [SQL Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/azure-sql#azure-sql-analytics-options) (Preview) Azure Marketplace solution
- Azure SQL Analytics is an advanced cloud monitoring solution for monitoring performance of all of your Azure SQL databases at scale and across multiple subscriptions in a single view. Azure SQL Analytics collects and visualizes key performance metrics with built-in intelligence for performance troubleshooting.
- It uses Azure resource metrics and logs to display details about all your Azure SQL databases in a single Log Analytics workspace.
- Use the process described in [Add Azure Monitor solutions from the Solutions Gallery](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/solutions) to add Azure SQL Analytics (Preview) to your Log Analytics workspace.

###	View resource log data using custom [log analytics queries](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview)
- SQL to Azure Monitor log query [cheat sheet](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/sql-cheatsheet)
- [Tutorial](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal): Get started with Log Analytics queries
  - Has a link to a demo environment with sample data
- Log Analytics limits results to a maximum of 10,000 records.
- Log query [scope and time range](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/scope) in Azure Monitor Log Analytics
- [Sample Log Analytic queries](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/azure-sql#creating-alerts-for-azure-sql-database) for Azure SQL Database Resources

### Schema for Azure Resource Logs
- [Common properties](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/resource-logs-schema#top-level-common-schema) for all resources
- [Structure of Azure Monitor Logs](a.%09https:/docs.microsoft.com/en-us/azure/azure-monitor/log-query/logs-structure). Note the **AzureMetrics** and **AzureDiagnostics** tables for Azure resource logs.
- The Log Analytics tables used depend on what type of collection the resource is using:
  - Azure diagnostics - All data written is to the **AzureDiagnostics** table. A combination of the resource type (available in the resourceId property) and the category uniquely identify a schema.
  - Resource-specific - Data is written to individual table for each category of the resource. Some resources, such as [ADF](https://azure.microsoft.com/en-us/updates/adf-logs-in-dedicated-tables/), support creating a resource specific table in Log Analytics. All Azure services will eventually migrate to the Resource-Specific mode.
  - See the [documentation for each service](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-logs-schema) for details on which mode it uses
- List of service specific [schemas](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/resource-logs-schema#service-specific-schemas)
- Azure SQL Database [Schema](https://docs.microsoft.com/en-us/azure/azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure?tabs=azure-portal#metrics-and-logs-available); Supported [Resource Categories](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/resource-logs-categories#microsoftsqlserversdatabases) for Azure SQL Database
- [ADF Schema](https://docs.microsoft.com/en-us/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events). In Resource-Specific mode, diagnostic logs from Azure Data Factory flow into the ADFPipelineRun, ADFTriggerRun, and ADFActivityRun tables.
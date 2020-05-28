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
- [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview).
  - Use Log Analytics in the Azure portal to write log queries and interactively analyze log data using the powerful Data Explorer analysis engine.
  - You can export the results of a Log Analytics query to Power BI to use different visualizations and share with users outside of Azure.
- [Azure Monitor Workbooks](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/workbooks-overview).
  - Workbooks provide a flexible canvas for data analysis and the creation of rich visual reports within the Azure portal.
  - They allow you to tap into multiple data sources from across Azure, and combine them into unified interactive experiences.
- [Resource Logs (Diagnostics Settings)](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal). You can configure a resource to write diagnostic logs to Log Analytics The content of resource logs varies by the Azure service and resource type.
- [Cost management](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/quick-acm-cost-analysis)
  - You can use Azure Cost Management and Billing features to conduct billing administrative tasks and manage billing access to costs.
  - Can customize cost vews, filter by tag, export, etc.
  - [Create Budgets](https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets)




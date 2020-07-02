# Logic Apps
## Overview
- https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview.
- Azure Logic Apps is a cloud service that helps you schedule, automate, and orchestrate tasks, business processes, and workflows when you need to integrate apps, data, systems, and services across enterprises or organizations.
- Minimize time to deploy integration solutions
- Easy workflow creation with triggers and actions

## How It Works
- https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview#how-does-logic-apps-work +  https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview#key-terms
- Every logic app workflow starts with a trigger, which fires when a specific event happens, or when new available data meets specific criteria. 
- Actions are all the steps that happen after the trigger.
- You can use prebuilt Microsoft-managed connectors that are designed to connect, access, and work with your data or create your own custom connectors.
- Logic Apps offers [hundreds of connectors](https://docs.microsoft.com/en-us/connectors/connector-reference/)
- Sample Walkthrough
  - Create logic app resource https://docs.microsoft.com/en-us/azure/logic-apps/quickstart-create-first-logic-app-workflow
  - At creation, you can select an Azure region or Logic App ISE if one has been created


## Connect to On-Premises 
- On-Premises Data Gateway
  - High level steps: 1) Create on-prem resource in portal 2) Install gateway executable on your private network machine 3) Use a supported data source within the Logic App
  - https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-gateway-connection
  - See section supported data sources
- Create a Logic Apps ISE Environment
  - https://docs.microsoft.com/en-us/azure/logic-apps/connect-virtual-network-vnet-isolated-environment
  - https://azure.microsoft.com/nl-nl/blog/announcing-azure-integration-service-environment-for-logic-apps/
  - Steps 1) create a Logic App ISE (must have existing vnet)  2) Create a logic app  and choose the ISE as the resource Location 3) Use Core/ISE connectors within your logic app https://docs.microsoft.com/en-us/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#isolated-versus-global
  - ISE gives your logic apps and integration accounts direct access to your Azure virtual network. When you create an ISE, Azure injects that ISE into your Azure virtual network, which then deploys a private and isolated instance of the Logic Apps service into your Azure virtual network.
  - For on-premises systems that aren't connected to a virtual network or don't have ISE-version connectors, you must first set up the on-premises data gateway before your logic apps can connect to those systems.
  - Logic apps, built-in triggers, built-in actions, and connectors that run in your ISE use a pricing plan that differs from the consumption-based pricing plan.
  - Your ISE also has increased limits on run duration, storage retention, throughput, HTTP request and response timeouts, message sizes, and custom connector requests. 
  - https://docs.microsoft.com/en-us/azure/logic-apps/connect-virtual-network-vnet-isolated-environment The Premium ISE base unit has fixed capacity, so if you need more throughput, you can add more scale units, either during creation or afterwards. You can autoscale based on performance metrics or based on a number of additional processing units

## Data Operations and Actions
- REST
  - HTTP - basic calling of REST endpoint https://docs.microsoft.com/en-us/azure/connectors/connectors-native-http
  - HTTP Swagger - more descriptive https://docs.microsoft.com/en-us/azure/connectors/connectors-native-http-swagger
  - Native Webhook https://docs.microsoft.com/en-us/azure/connectors/connectors-native-webhook
- Data Operations https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-perform-data-operations 
- Data operation code samples for Azure Logic Apps https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-data-operations-code-samples
- Reference for trigger and action types in Workflow Definition Language for Azure Logic Apps https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-workflow-actions-triggers
- Limits and configuration information for Azure Logic Apps https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-limits-and-config
- Sample Walkthrough
  - Create SOAP custom connector https://docs.microsoft.com/en-us/connectors/custom-connectors/create-register-logic-apps-soap-connector which uses https://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl
    - You can use GetOpenOrders and customerid=1001 per https://azure.microsoft.com/en-us/blog/soap-to-rest/
  - Tutorial: Create automated approval-based workflows by using Azure Logic Apps https://docs.microsoft.com/en-us/azure/logic-apps/tutorial-process-mailing-list-subscriptions-workflow
  - Add an HTTP Action https://docs.microsoft.com/en-us/azure/connectors/connectors-native-http#add-an-http-action
  - Add an HTTP with Swagger Action https://docs.microsoft.com/en-us/azure/connectors/connectors-native-http-swagger#add-an-http--swagger-action
  - Add an HTTP Trigger https://docs.microsoft.com/en-us/azure/connectors/connectors-native-http#add-an-http-trigger
    - This can be used to pass parameters to a Logic App


## API Connections to On-Prem Data Sources

### API Management
- Use API Management to create consistent and modern API gateways for existing back-end services and to simplify API usage (for exaample to convert from SOAP to REST) 
- Consider this approach for data sources which do not have built-in Logic App or ADF connectors
- It is also possible to expose a Logic App as an API and use API Management to front the API to your API consumers
- To minimize network latency, put API Management and Logic Apps/ADF in the same region.
- Architecture Patterns and Reference
  - Basic enterprise integration on Azure https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration
  - Migrating a legacy web application to an API-based architecture on Azure https://docs.microsoft.com/en-us/azure/architecture/example-scenario/apps/apim-api-scenario
- Sample Swagger API definition: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236

## Connect to SharePoint
SharePoint provides a few ways to retrieve data:
- OData: https://docs.microsoft.com/en-us/azure/data-factory/connector-odata 
- REST API: Working with folders and files with REST https://docs.microsoft.com/en-us/sharepoint/dev/sp-add-ins/working-with-folders-and-files-with-rest 

The Azure Data Factory (ADF) Copy activity supports generic ODATA + REST sources. So you can directly access SharePoint from ADF this way.
- Connector Reference https://docs.microsoft.com/en-us/azure/data-factory/connector-overview
- Copy Activity Supported Data Stores https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats
- Copy data from an OData source by using Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/connector-odata 
- Copy data from a REST endpoint by using Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/connector-rest

It is easier to connect to SharePoint via a Logic App (or Power Automate) because Logic Apps (and Power Automate) has a SharePoint connector (vs having to use a generic OData/REST). 
- Connector reference: https://docs.microsoft.com/en-us/connectors/sharepoint/
- How to Monitor and manage SharePoint resources with Azure Logic Apps https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-sharepoint

## Integrate Logic App with an Azure Data Factory Pipeline

To tie in the Logic App with your Azure Data Factory pipeline, you can use the Azure Data Factory action within the logic app: https://docs.microsoft.com/en-us/connectors/azuredatafactory/.

You can also call the Logic App within the ADF pipeline via the Web activity in Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/control-flow-web-activity. 
- This article shows how to call a Logic app within an ADF pipeline: https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-refresh-logic-app#consume-the-logic-app-with-azure-data-factory


## BizTalk Migration to Logic Apps
- https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-move-from-mabs

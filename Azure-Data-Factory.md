# Azure Data Factory

## Overview
- https://docs.microsoft.com/en-us/azure/data-factory/introduction. Azure Data Factory is a cloud-based data integration service that allows you to create data-driven workflows in the cloud for orchestrating and automating data movement and data transformation. It’s built for complex hybrid extract-transform-load (ETL), extract-load-transform (ELT), and data integration projects.
- Choosing a data pipeline orchestration technology in Azure https://docs.microsoft.com/en-us/azure/architecture/data-guide/technology-choices/pipeline-orchestration-data-movement#capability-matrix

## How It Works
- Ingest: With Data Factory, you can use the Copy Activity in a data pipeline to move data from both on-premises and cloud source data stores to a centralization data store in the cloud for further analysis. 
- Transform: After data is present in a centralized data store in the cloud, process or transform the collected data by using compute services such as HDInsight Hadoop, Spark, Data Lake Analytics, and Machine Learning.
- Monitor: Azure Data Factory has built-in support for pipeline monitoring via Azure Monitor, API, PowerShell, Azure Monitor logs, and health panels on the Azure portal.
- Key Concepts https://docs.microsoft.com/en-us/azure/data-factory/introduction#top-level-concepts
  - Pipeline: A pipeline is a logical grouping of activities that performs a unit of work. It allows you to manage the activities as a set instead of managing each one individually. The activities in a pipeline can be chained together to operate sequentially, or they can operate independently in parallel.
  - Activity: A processing step in a pipeline. Three types: Movement, Transformation, Control
  - Datasets represent data structures within the data stores, which simply point to or reference the data you want to use in your activities as inputs or outputs.
  - Linked services are much like connection strings, which define the connection information that's needed for Data Factory to connect to external resources.
    - To represent a data store 
    - To represent a compute resource that can host the execution of an activity
    - Recommend you store linked service credentials in Azure key Vault https://docs.microsoft.com/en-us/azure/data-factory/store-credentials-in-key-vault
  - Triggers represent the unit of processing that determines when a pipeline execution needs to be kicked off.
    - Schedule: Daily, weekly and monthly frequencies, in addition to minute and hour based settings
    - Tumbling: This trigger type is good for automating historical data type loads. Supports the use of the WindowStart and WindowEnd system variables. Users can access triggerOutputs().windowStartTime and triggerOutputs().windowEndTime as trigger system variables in the trigger definition. These can be passed to other pipeline activities to process data range specific activities. https://docs.microsoft.com/en-us/azure/data-factory/concepts-pipeline-execution-triggers#trigger-type-comparison 
    - Event based - Blob created/deleted.
  - A pipeline run is an instance of the pipeline execution. Pipeline runs are typically instantiated by passing the arguments to the parameters that are defined in pipelines
- Sample Walkthrough
  - Create a pipeline manually https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal
  - Create a pipeline through copy data wizard: https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-copy-data-tool#start-the-copy-data-tool


## Data Movement and Transformations
- Copy activity in Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-overview
  - Copy activity performance and scalability guide https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-performance
  - List of data stores that Copy Activity supports as sources and sinks: https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats
- Parameterize linked services in Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/parameterize-linked-services
- Copy data from a REST endpoint by using Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/connector-rest
  - REST connector specifically supports copying data from RESTful APIs
- Code: Transform un-staged data with the Copy Activity
  - When copying data into Azure SQL Database or SQL Server, you can configure the SqlSink in the copy activity to invoke a stored procedure
  - Steps to invoke a stored procedure from a SQL sink https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink
- Code: Transform staged data
  - Transform data in Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/transform-data 
  - Spark activity https://docs.microsoft.com/en-us/azure/data-factory/tutorial-transform-data-spark-portal
- Codeless: Transform data with the Mapping Data Flow https://docs.microsoft.com/en-us/azure/data-factory/concepts-data-flow-overview 
  - Allows you to build visual, code-free, data transformations within ADF
  - Supports flexible schemas. You can derive columns using template expression patterns based on name and type matching instead of defining static field names.
  - You may need to first stage the data into a supported source (such as Data Lake or Azure SQL Database staging tables)
  - Sample Walkthrough
    - Overview: https://www.youtube.com/watch?v=ZMG-qPqeH50 
    - Create Azure Data Factory Data Flow https://docs.microsoft.com/en-us/azure/data-factory/data-flow-create
    - Joining Data: https://docs.microsoft.com/en-us/azure/data-factory/data-flow-join +  https://www.youtube.com/watch?v=zukwayEXRtg
    - SCD Type 1 with Mapping Data Flows https://www.youtube.com/watch?v=Rz2zx5GRbrA
    - Using Parameters https://docs.microsoft.com/en-us/azure/data-factory/parameters-data-flow +  https://www.youtube.com/watch?v=vpuuQcFojt8
## Connect to On-Premises 
- https://docs.microsoft.com/en-us/azure/data-factory/concepts-integration-runtime#integration-runtime-types. 
- Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. 
- Use a self-hosted integration runtime to connect to on-premises data sources 
- Use SSIS integration runtime to run SSIS packages in ADF. Azure-SSIS IR can be provisioned in either public network or private network. On-premises data access is supported by joining Azure-SSIS IR to a Virtual Network that is connected to your on-premises network.
  - Steps to create an Azure-SSIS Integration Runtime in Azure Data Factory https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime
  - Steps to migrate SSISDB to Azure SQL Database managed instance https://docs.microsoft.com/en-us/azure/data-factory/scenario-ssis-migration-ssisdb-mi
- Self-Hosted Runtime Architecture Guidance
  - https://docs.microsoft.com/en-us/azure/data-factory/data-migration-guidance-netezza-azure-sqldw
- Sample Walkthrough:
  - Copy data from an on-premises SQL Server database to Azure Blob storage https://docs.microsoft.com/en-us/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline
  - Steps to create self-hosted IR https://docs.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime#high-level-steps-for-creating-a-linked-self-hosted-ir



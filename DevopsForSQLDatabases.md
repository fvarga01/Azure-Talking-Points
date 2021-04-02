# Devops for SQL Databases


## Overview

### DevOps
DevOps is a set of practices that combines software development ( Dev) and IT operations ( Ops ).

Key tenets:
* Embraces agile  + lean approaches
* Automation (codify your infrastructure, automate CI processes, run acceptance tests, allow manual testing and approvals as needed)
* Source control
* Continuous Integration ( integrate code into a build pipeline - commit, build, test)
* Continuous Delivery (create releases regularly after successful testing and deploy in a repeatable fashion)
  
### Database DevOps
* Includes tools and processes to:
  1. Extract and update database state locally and push changes to a shared code repository 
  2. Generate migration scripts after changes are made or detect and merge needed changes while enforcing custom checks and policies (run required unit tests, build rules)
  3. Migrate databases to new versions in an automated or repeatable fashion (build and release pipelines)

#### _Migration Based Database DevOps_
* Database is the system of record
* Imperative (procedural/script-driven). Defines __how__ the task should be performed.
* Incremental scripts are generated as changes to the database are made. Method and order of changes must be taken into account.
* Assumes that database is in a known state that can be directly upgraded with the upgrade scripts. Problematic if changes have been made directly in production.
* Build process typically generates migration scripts, packaged as artifacts. Deployment process runs the generated migration scripts
* Example: Entity Framework code-first migrations: [Entity Framework Code First Migrations](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/)
  1. Create your models in C# code
  2. Run a command to generate the migrations (EF tools produce SQL Scripts)). See migration file snippets [here](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing?tabs=dotnet-core-cli#customize-migration-code).
  3. __Commit the migration scripts to source control__
  4. Deploy migration scripts using Github Actions/Azure DevOps (release pipeline -> AzureSQL SqlTask)
 

#### State Based Database DevOps
* Code repository is the system of record
* Declarative approach. Defines the desired state, frameworks determine how to apply the changes.
* Typically lower turnaround time (because of lower development overhead) and easier to manage database state.
* Database model is modeled offline, framework tools work out needed mechanisms to deploy the changes.
* Build process generates dacpac file. Deployment process merges the changes (using available tools such as sqlpackage.exe, Github Actions, Azure DevOps)

##  Key Tools
### Data Tier Application
* A [Data Tier Application](https://docs.microsoft.com/en-us/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-ver15) is a database lifecycle management and productivity tool that enables declarative database development to simplify deployment and management.
* A data-tier application (DAC) is a __logical database management entity__ that defines all SQL Server objects - such as tables, views, and instance objects - associated with a user's database.
* It is a self-contained unit of SQL Server database deployment that enables data-tier developers and DBAs to package SQL Server objects into a portable artifact called a DAC package, or .dacpac file.
* A SQL Server database can be registered as a DAC. When a database is registered, the DAC version and other properties are recorded as part of the metadata of the database. Conversely, a database can also be unregistered and have its DAC properties removed.
* [DAC Tools](https://docs.microsoft.com/en-us/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-ver15#dac-tools)

### DACPAC
* A DACPAC is a Windows file with a .dacpac extension. The file supports an open format consisting of multiple XML sections representing details of the DACPAC origin, the objects in the database, and other characteristics.

### SqlPackage.exe
* [sqlpackage.exe](https://docs.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage?view=sql-server-ver15) is a command-line utility that automates the following database development tasks: Version, Extract, Publish, Export, Import, Deploy/Report, DriftReport, Script
* [Properties](https://docs.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage?view=sql-server-ver15#properties) used to control sqlpackage.exe actions. Note: DoNotDropObjectTypes, DoNotAlterReplicatedObjects, DropObjectsNotInSource, etc.

### DACFx API
* [DacFx](https://docs.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage-download?view=sql-server-ver15#dacfx) is the engine for SSDT. Core technology SSDT leverages for incremental database deployments, modeling and schema validation. 
* Key features
  * sqlpackage.exe operations (Microsoft.SqlServer.Dac)
  * Code analysis (Microsoft.SqlServer.Dac.CodeAnalysis)
  * Customize your deployment (Microsoft.SqlServer.Dac.Deployment)
  * Interrogate and alter the database model (Microsoft.SqlServer.Dac.Model)
* Actions:
  * BuildContributor
    * Use code analysis to enforce custom code rules at build time (disallow nulls, enforce datetime2, nchar instead char, no text/image columns, etc.)
    * Add your custom class via `/p:BuildContributor=<myclass>` parameter to your build (MSBUILD.exe)
  * DeploymentPlanModifier
    * Add your custom class via `/p:AdditionalDeploymentContributors=<myclass>` parameter to you deployment (sqlpackage.exe)
    * Ex. Customize your deployment to throw an error if there are table drop events identified in the deployment plan
  * DeploymentPlanExecutor
* [DacFx: What is it, why should you care and what you can use it for](https://channel9.msdn.com/Events/Ignite/New-Zealand-2016/M404)
  * Sample showing how to automatically generate table column documentation
* [DACExtensions](http://github.com/Microsoft/DacExtensions) contains API extensions and samples using the DacFx API to develop Data-Tier Applications.
* [Microsoft.SqlServer.DACFx Nuget Package](https://www.nuget.org/packages/Microsoft.SqlServer.DacFx)



## Source Controlled Database Projects

### SQL Server Data Tools (Now integrated into Visual Studio 2019 Database Projects)
* There's [no SSDT standalone installer](https://docs.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-ver15) for Visual Studio 2019.
* With Visual Studio 2019, the required functionality to enable Analysis Services, Integration Services, and Reporting Services projects has moved into the respective Visual Studio (VSIX) extensions only.
* Key features and functionality:
  * Database project for new or existing databases
  * Import\Export DACPAC
  * Support for offline + connected database development
  * Compile time code verification
  * SQL Server Object explorer
  * Rich code navigation (find all references, go to table definition, etc.)
  * GUI table designer to manage tables and relationships
  * [Generate unit tests](https://docs.microsoft.com/en-us/sql/ssdt/walkthrough-creating-and-running-a-sql-server-unit-test?view=sql-server-ver15#DeployDBProj)
  * Schema comparison
  * Generate merge scripts
  * Publish changes
  * Source code control

### Azure Data Studio
* [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is-azure-data-studio?view=sql-server-ver15) is a cross-platform database tool for data professionals using on-premises and cloud data platforms on Windows, macOS, and Linux.
* Extensions
  * [SQL Database Projects extension (Preview)](https://docs.microsoft.com/en-us/sql/azure-data-studio/extensions/sql-database-project-extension?view=sql-server-ver15)
  * [Schema Compare extension](https://docs.microsoft.com/en-us/sql/azure-data-studio/extensions/schema-compare-extension?view=sql-server-ver15)
  * [SQL Server dacpac extension](https://docs.microsoft.com/en-us/sql/azure-data-studio/extensions/sql-server-dacpac-extension?view=sql-server-ver15)
* [Source control in Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/source-control?view=sql-server-ver15)
* Key features and functionality:
  * Compatible with SSDT projects
  * Cross platform support (Windows, MacOS, Linux)
  * Database project for new or existing databases
  * Import\Export DACPAC
  * Support for offline + connected database development
  * Schema comparison
  * Generate merge scripts
  * Publish changes
  * Source code control
* Unit testing code can be created with [tSQLt](https://github.com/tSQLt-org/tsqlt) which is an open source unit testing framework.
  * tSQLt allows you to implement unit tests in T-SQL, you don’t have to switch between various tools to create the code and unit tests.
  *  Assemble (get things ready), act (run proc), assert (confirm expected behavior)
### SSMS
* Some third party source control integrations are available
* [Azure Data Studio vs SSMS Feature Comparison](https://docs.microsoft.com/en-us/sql/azure-data-studio/what-is-azure-data-studio?view=sql-server-ver15#shell-features)

## DevOps Build and Release Pipeline 

### Azure DevOps
* Build Tasks
  * [Visual Studio Build task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/build/visual-studio-build?view=azure-devops)
    * also supports MSBuild project (.*proj) files.
    * If you are using Azure Data Studio database projects, there is no solution (.sln) file generated. Instead a (.sqlproj) file will be created. So, you would specify 	`**\*.sqlproj` for the `Solution` property.
  * [Copy Files task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/copy-files?view=azure-devops&tabs=yaml)
  * [Publish Build Artifacts task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/utility/publish-build-artifacts?view=azure-devops)
* Release
  * [Azure SQL Database Deployment task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops)
  * Supports DACPAC deployment (for state based) and running SQL scripts (for migration based)
* [Invoking SQLPackage in Azure DevOps to Publish incremental changes](https://docs.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb?view=azure-devops&tabs=yaml#publish)
* Walkthrough: [Continuous Delivery for Azure SQL DB using Azure DevOps Multi-stage Pipelines](https://devblogs.microsoft.com/azure-sql/continuous-delivery-for-azure-sql-db-using-azure-devops-multi-stage-pipelines/)
* [Grant Azure DevOps permission to Azure Subscription](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)

### Github Actions
* [Use GitHub Actions to connect to Azure SQL Database]( https://docs.microsoft.com/en-us/azure/azure-sql/database/connect-github-actions-sql-db)
* [Azure SQL Deploy action](https://github.com/marketplace/actions/azure-sql-deploy) uses a Connection string for authentication and DACPAC or SQL scripts to deploy to your SQL database. [SQL-OnAzure.yml template](https://github.com/Azure/actions-workflow-samples/blob/master/Database/SQL-on-Azure.yml) file.
* Migration Based Samples
  * [Sequential SQL Scripts - Azure SQL Database CI/CD Pipeline with GitHub Actions](https://docs.microsoft.com/en-us/samples/azure-samples/azure-sql-db-ci-cd/azure-sql-db-ci-cd/)
  * [EF Containerized ASP.NET Core and Azure SQL db for GitHub Actions](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions)

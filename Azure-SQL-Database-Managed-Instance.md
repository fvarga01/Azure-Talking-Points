# Azure SQL Database Managed Instance

## Provision
  - Azure SQL Database managed instance resource limits https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits
    - Gen4 (SSD) vs Gen5 (NVMe SSD). Gen4 hardware is being phased out.
    - General purpose vs Business Critical. Note IOPS and Storage IO latency. Add more vCores to get better IOPs
- Steps to create https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-get-started
    - Note managed instance requires a virtual network, however it is possible to allow a public endpoint connection using the steps outlined here: https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-public-endpoint-configure
- It is possible to restore a SQL Server backup into a managed instance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-get-started-restore
- Consider using the Azure Database Migration Service to migrate your on-premises database into Azure SQL Database Managed Instance: https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-managed-instance?toc=%2Fazure%2Fsql-database%2Ftoc.json&view=sql-server-2017

## Secure
- Azure Active Directory Connectivity https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-dotnet-core#get-adonet-connection-information-optional
    - Windows authentication is not supported and should be replaced with Azure Active Directory logins
    - Create additional Azure AD server principals (logins) using SSMS https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-aad-security-tutorial?view=sql-server-2017#create-additional-azure-ad-server-principals-logins-using-ssms
- By default, Transparent Data Encryption (TDE) is enabled for all newly deployed Azure SQL databases. https://docs.microsoft.com/en-us/azure/sql-database/transparent-data-encryption-azure-sql
  - TDE with Azure Key Vault integration (Bring Your Own Key) for Azure SQL Database Managed Instance https://docs.microsoft.com/en-us/azure/sql-database/transparent-data-encryption-byok-azure-sql
  - When migrating a database protected by Transparent Data Encryption to Azure SQL Database Managed Instance using native restore option, the corresponding certificate from the on-premises or IaaS SQL Server needs to be migrated before database restore. Steps to migrate the certificate of a TDE protected database to Azure SQL Database Managed Instance: https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate

## Networking
- Azure SQL Database Managed Instance must be deployed within an Azure virtual network (VNet).
  - Determine VNet subnet size for Azure SQL Database Managed Instance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet
  - Create a virtual network for Azure SQL Database Managed Instance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-create-vnet-subnet
- It's possible to configure a public endpoint for ASDB SQL Managed instance.
  - Use an Azure SQL Database managed instance securely with public endpoints https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-public-endpoint-securely.
- Unless a public endpoint has been configured, Managed Instance can be accessed only through a private IP address. So, in order to access it from Azure App Service you first need to make a connection between the application and the Managed Instance VNet.
  - Connect your application to Azure SQL Database managed instance https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-connect-app.
  - Connectivity architecture for a managed instance in Azure SQL Database https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-connectivity-architecture
 
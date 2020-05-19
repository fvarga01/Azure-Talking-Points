# Azure Synapse Analytics - SQL Pool (formerly Azure SQL Data Warehouse)

## Use Azure Automation PowerShell Script to PAUSE/RESUME/SCALE

1. Create an Azure Automation Account https://docs.microsoft.com/en-us/azure/automation/automation-quickstart-create-account
2. Add support for Az.Accounts  and Az.Sql PowerShell modules https://docs.microsoft.com/en-us/azure/automation/az-modules#import-the-az-modules
3. Create PowerShell runbook  https://docs.microsoft.com/en-us/azure/automation/automation-first-runbook-textual-powershell#step-6---add-code-to-start-a-virtual-machine

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

#get azure connection
$connection = Get-AutomationConnection -Name AzureRunAsConnection
while(!($connectionResult) -And ($logonAttempt -le 10))
{
    $LogonAttempt++
    # Log on to Azure
    $connectionResult =    Connect-AzAccount `
        -ServicePrincipal `
        -Tenant $connection.TenantID `
        -ApplicationId $connection.ApplicationID `
        -CertificateThumbprint $connection.CertificateThumbprint

    Start-Sleep -Seconds 30
}

#command to programmatically pause an azure synapse analytics sql pool (aka sql data warehouse)
Suspend-AzSqlDatabase -ResourceGroupName "my-rg-name" -ServerName "myservername" -DatabaseName "my-synapsesqldb-name"

#command to programmatically start/resume an azure synapse analytics sql pool (aka sql data warehouse)
Resume-AzSqlDatabase -ResourceGroupName "my-rg-name" -ServerName "myservername" -DatabaseName "my-synapsesqldb-name"

#command to programmatically scale an azure synapse analytics sql pool (aka sql data warehouse)
Set-AzSqlDatabase -ResourceGroupName "my-rg-name" -ServerName "myservername" -DatabaseName "my-synapsesqldb-name" -RequestedServiceObjectiveName "DW1000c"
```
4. Schedule your runbook https://docs.microsoft.com/en-us/azure/automation/shared-resources/schedules#to-create-a-new-schedule-in-the-azure-portal


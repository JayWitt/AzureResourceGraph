# AzureResourceGraph
Jay's notes on Azure Resource Graph (https://azure.microsoft.com/en-us/features/resource-graph/)


Details | Query 
--------|-------
Show all virtual machines ordered by name in descending order|project name, location, type \| where type =~ 'Microsoft.Compute/virtualMachines' \| order by name desc
Count virtual machines by OS Type (Windows or Linux)|where type =~ 'Microsoft.Compute/virtualMachines' \| summarize count() by tostring(properties.storageProfile.osDisk.osType)
List virtual machines that match something in their name (Example is for SQL) | where type =~ 'microsoft.compute/virtualmachines' and name contains "sql" \| project name \| order by name asc
List virtual machines that used a 2012 version of the marketplace image (By SKU) |where type =~ 'microsoft.compute/virtualmachines' and tostring(properties.storageProfile.imageReference.sku)<br>contains "2012" \| project name, tostring(properties.storageProfile.imageReference.sku) \| order by name asc
List virtual machines that used a SQL based marketplace image (By Offer) | where type =~ 'microsoft.compute/virtualmachines' and tostring(properties.storageProfile.imageReference.offer)<br>contains "sql" \| project name, tostring(properties.storageProfile.imageReference.offer) \| order by name asc
List storage account count by subscription and location | where type =~ 'Microsoft.Storage/storageaccounts'\| summarize count() by tostring(subscriptionId), tostring(properties.primaryLocation)
List all virtual machines in a certain region | project name, location, type \| where type =~ 'Microsoft.Compute/virtualMachines' and location == 'eastus2'
List all virtual machines with a certain VALUE in a TAG | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' and tags.TAG contains 'VALUE'
List all virtual machines that have been registered with the SQL Virtual Machine Resource Provider and list the license type, the type of agent that is installed onto the VM, the image type along with the version of SQL | where type == "microsoft.sqlvirtualmachine/sqlvirtualmachines" \| <br> project name, properties.sqlServerLicenseType, properties.sqlManagement, properties.sqlImageOffer, properties.sqlImageSku
List VMs that are using unmanaged disks | where properties.storageProfile.osDisk.vhd.uri <> ""<br>\| where type =~ 'Microsoft.Compute/virtualMachines' 
List all VMs that report to a Log Analytics Workspace | Resources <br>\| where type == "microsoft.compute/virtualmachines/extensions" and name == "MicrosoftMonitoringAgent"<br>\| extend VM1 = split(id,"/")<br>\| extend VMName = VM1[8]<br>\| extend LogAnalyticsWorkspaceID = properties.settings.workspaceId<br>\| extend ProvisionState = properties.provisioningState<br>\| join kind=leftouter (ResourceContainers<br>\| where type=='microsoft.resources/subscriptions'<br>\| project SubName=name, subscriptionId) on subscriptionId<br>\| project SubName, VMName, ProvisionState, LogAnalyticsWorkspaceID

# azure-powershell-command

## Install the AzureAD PowerShell Module (if not already installed):

Open PowerShell as an administrator and run the following command to install the AzureAD module:

````
Install-Module -Name AzureAD
````
## Connect to your Azure AD Tenant:

Run the following command to connect to your Azure AD tenant:

````
Connect-AzureAD
````
## Retrieve Groups:

Once you're connected, you can use the Get-AzureADGroup cmdlet to retrieve groups from your Azure AD tenant. Here's how to use it:

````
# Retrieve all groups
$groups = Get-AzureADGroup

# Display group information
$groups | Select-Object DisplayName, ObjectId

````
The Get-AzureADGroup cmdlet retrieves all groups in your Azure AD tenant, and the Select-Object cmdlet is used to display selected properties like "DisplayName" and "ObjectId" (which uniquely identifies each group).

Remember that the Azure AD PowerShell module offers various cmdlets to manage Azure AD resources, so you can perform more advanced operations as needed.

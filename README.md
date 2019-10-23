Install Terraform


To copy
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
Configure Terraform access to Azure
Create an Azure AD service principal to allow Terraform to provision resources in Azure. The service principal allows your Terraform scripts to provision resources in your Azure subscription.
If you have multiple Azure subscriptions, first query your account with az account list for a list of Subscription ID and Subscriber ID values:
Azure CLI

To copy

Try
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
To use a selected subscription, set the subscription for this session with az account set . Set the environment variable to contain the value of the field returned from the subscription you want to use: SUBSCRIPTION_IDid
Azure CLI

To copy

Try
az account set --subscription="${SUBSCRIPTION_ID}"
You can now create a service master for use with Terraform. Use az ad sp create-for-rbac and set the scope to your subscription as follows:
Azure CLI

To copy

Try
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
Your values appId, password, sp_nameand tenantare returned. Note the values and . appIdpassword
Configure Terraform environment variables
To configure Terraform to use your Azure AD service principal, set the following environment variables, which are then used by Azure Terraform modules . You can also set the environment if you are working on an Azure cloud other than Azure Public.
ARM_SUBSCRIPTION_ID
ARM_CLIENT_ID
ARM_CLIENT_SECRET
ARM_TENANT_ID
ARM_ENVIRONMENT
You can use the following shell script example to set these variables:
bash

To copy
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
Run a sample script
Create a file test.tfin an empty directory and paste the following script.
HCL

To copy
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
Save the file and then initiate the Terraform deployment. This step downloads the Azure modules needed to create an Azure resource group.
bash

To copy
terraform init
The result looks like the following example:
console

To copy
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
You can preview the actions to be performed by the Terraform script with terraform plan. When you are ready to create the resource group, apply your Terraform plan as follows:
bash

To copy
terraform apply
The result looks like the following example:
console

To copy
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s


 

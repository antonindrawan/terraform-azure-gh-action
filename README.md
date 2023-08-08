# Showcase deployment of Azure resources using Terraform and GitHub Actions

Based on 

* https://github.com/Azure-Samples/terraform-github-actions/blob/main/README.md
* https://thomasthornton.cloud/2021/03/19/deploy-terraform-using-github-actions-into-azure


## Prerequisites
### Terraform must store state for managed infrastructure and configuration in a storage account
```
# Create a Resource Group
az group create -n rg-terraform-azure-gh-action-states -l westeurope
 
# Create Storage Account: 
az storage account create -n antonterraformstorage -g rg-terraform-azure-gh-action-states -l westeurope --sku Standard_LRS
 
# Create Storage Account Container
az storage container create -n tfstatedevops --account-name antonterraformstorage
```

### Create Azure Service Principals

Create two azure service principals:

* Azure-GH-contrib
* Azure-GH-readonly

Assign Role `Reader and Data Access` to the azure principals

* Go to resource group: rg-terraform-azure-gh-action-states
* Select Access Control (IAM)
* Select Add Role Assignment
* Select Reader and Data Access
* Select members: Azure-GH-contrib and Azure-GH-readonly
* Review + Assign

### Try it out

Make a pull request to the repository and see the GitHub Action in action.

### Clean up resources

Destroy the created azure resources created by terraform
    terraform plan -destroy -out=destroy.tfplan

Destroy the azure resource group where the storage account is created. It can later be re-created for practicing.

    az group delete -n rg-terraform-azure-gh-action-states


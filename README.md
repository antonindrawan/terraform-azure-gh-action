# Showcase deployment of Azure resources using Terraform and GitHub Actions

Based on 

* https://github.com/Azure-Samples/terraform-github-actions/blob/main/README.md
* https://thomasthornton.cloud/2021/03/19/deploy-terraform-using-github-actions-into-azure


## Prerequisites
### Terraform must store state for managed infrastructure and configuration in a storage account
```
# Create Resource Group
az group create -n rg-terraform-azure-gh-action-states -l westeurope
 
# Create Storage Account: 
az storage account create -n antonterraformstorage -g rg-terraform-azure-gh-action-states -l westeurope --sku Standard_LRS
 
# Create Storage Account Container
az storage container create -n tfstatedevops --account-name antonterraformstorage
```
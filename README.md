# Try a deployment of Azure resources using Terraform and GitHub Actions

Based on 

* https://github.com/Azure-Samples/terraform-github-actions/blob/main/README.md
* https://thomasthornton.cloud/2021/03/19/deploy-terraform-using-github-actions-into-azure


## Prerequisites
### Terraform must store state for managed infrastructure and configuration in a storage account
```
# Create a resource Group for the storage account
az group create -n rg-terraform-azure-gh-action-states -l westeurope
 
# Create Storage Account: 
az storage account create -n antonterraformstorage -g rg-terraform-azure-gh-action-states -l westeurope --sku Standard_LRS
 
# Create Storage Account Container
az storage container create -n tfstatedevops --account-name antonterraformstorage
```

### Create Azure Service Principals

1. Create two azure service principals:

* Azure-GH-contrib
* Azure-GH-readonly

Assign Role `Reader and Data Access` to the azure principals

* Go to resource group: rg-terraform-azure-gh-action-states
* Select Access Control (IAM)
* Select Add Role Assignment
* Select Reader and Data Access
* Select members: Azure-GH-contrib and Azure-GH-readonly
* Review + Assign

2. Add federated credentials to each service principal

https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure?tabs=azure-portal%2Clinux#use-the-azure-login-action-with-openid-connect

Within each App (Service Principal), go to ``Certificates & secrets``, in the ``federated credentials`` tab, select ``Add credential``.

Select the credential scenario ``GitHub Actions deploying Azure resources``. 

For the ``Azure-GH-contrib`` identity create 1 federated credential as follows:

* Set ``Entity Type`` to ``Environment`` and use the ``production`` environment name.

For the ``Azure-GH-readonly`` identity create 2 federated credentials as follows:

* Set ``Entity Type`` to ``Pull Request``.
* Set ``Entity Type`` to ``Branch`` and use the ``main`` branch name.


### Prepare GitHub Environment

Follow step 2 in Getting Started to create `production` GitHub environment because the 
`terraform-apply` job is only run on `production.`
https://github.com/Azure-Samples/terraform-github-actions/blob/main/README.md


## Try it out

Create a commit on the main branch or make a pull request to the main branch. See the GitHub Action in action.


### Clean up resources

Destroy the created azure resources created by terraform

    terraform plan -destroy -out=destroy.tfplan
    terraform apply destroy.tfplan

Destroy the azure resource group where the storage account is created. It can later be re-created for practicing.

    az group delete -n rg-terraform-azure-gh-action-states

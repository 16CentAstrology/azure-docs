---
title: 'Quickstart: Back up a virtual machine in Azure with Terraform'
description: In this quickstart, you learn how to configure Azure Backup to run a backup on demand by creating an Azure Windows virtual machine (VM), virtual network, subnet, public IP, network security group, network interface, storage account, Backup recovery services vault, Backup policy, and a protected VM for backup. 
ms.topic: quickstart
ms.date: 11/27/2024
ms.custom: devx-track-terraform
ms.service: windows
author: AbhishekMallick-MS
ms.author: v-abhmallick
#customer intent: As a Terraform user, I want to see how to configure Azure Backup to run a backup on demand by creating and configure an Azure virtual network, subnet, public IP, network security group, network interface, storage account, virtual machine, Backup recovery services vault, and Backup policy.
content_well_notification: 
  - AI-contribution
---

# Quickstart: Back up a virtual machine in Azure with Terraform

In this quickstart, you create an Azure Windows virtual machine (VM) and associated resources using Terraform. An Azure Windows VM is a scalable computing resource that Azure provides. It's an on-demand, virtualized Windows server in the Azure cloud. You can use it to deploy, test, and run applications, among other things. In addition to the VM, this code also creates a virtual network, subnet, public IP, network security group, network interface, storage account, Azure Backup recovery services vault, and Backup policy.

[!INCLUDE [About Terraform](~/azure-dev-docs-pr/articles/terraform/includes/abstract.md)]

> [!div class="checklist"]

> * Create an Azure resource group with a unique name.
> * Create a virtual network with a unique name and a specified address space.
> * Create a subnet within the virtual network with a unique name and a specified address prefix.
> * Create a public IP address with a unique name.
> * Create a network security group with two security rules for remote desk protocol and web traffic.
> * Create a network interface with a unique name, and attach it to the subnet and public IP address.
> * Associate the network security group with the network interface.
> * Generate a random ID for a unique storage account name.
> * Create a storage account for boot diagnostics.
> * Create a Windows VM with a unique name.
> * Generate a random password for the VM.
> * Create a Backup recovery services vault with a unique name.
> * Create a Backup policy for the VM with daily frequency and a retention period of seven days.
> * Protect the VM with the created Backup policy.

## Prerequisites

- Create an Azure account with an active subscription. You can [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Install and configure Terraform](/azure/developer/terraform/quickstart-configure).

## Implement the Terraform code

> [!NOTE]
> The sample code for this article is located in the [Azure Terraform GitHub repo](https://github.com/Azure/terraform/tree/master/quickstart/101-backup-vm). You can view the log file containing the [test results from current and previous versions of Terraform](https://github.com/Azure/terraform/tree/master/quickstart/101-backup-vm/TestRecord.md).
> 
> See more [articles and sample code showing how to use Terraform to manage Azure resources](/azure/terraform).

1. Create a directory in which to test and run the sample Terraform code, and make it the current directory.

1. Create a file named `main.tf`, and insert the following code:
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-backup-vm/main.tf":::

1. Create a file named `outputs.tf`, and insert the following code:
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-backup-vm/outputs.tf":::

1. Create a file named `providers.tf`, and insert the following code:
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-backup-vm/providers.tf":::

1. Create a file named `variables.tf`, and insert the following code:
    :::code language="Terraform" source="~/terraform_samples/quickstart/101-backup-vm/variables.tf":::

## Initialize Terraform

[!INCLUDE [terraform-init.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-init.md)]

## Create a Terraform execution plan

[!INCLUDE [terraform-plan.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-plan.md)]

## Apply a Terraform execution plan

[!INCLUDE [terraform-apply-plan.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-apply-plan.md)]

## Verify the results

### [Azure CLI](#tab/azure-cli)

1. Get the Azure resource group name.

    ```console
    resource_group_name = $(terraform outout -raw azurerm_resource_group_name)
    ```

1. Get the Backup recovery services vault name.

    ```console
    recovery_services_vault_name = $(terraform output -raw azurerm_recovery_services_vault_name)
    ```

1. Get the Windows VM name.

    ```console
    windows_virtual_machine_name = $(terraform output -raw azurerm_windows_virtual_machine_name)
    ```

1. Run [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) to start a backup job.

    ```azurecli
    az backup protection backup-now --resource-group $resource_group_name \
                                    --vault-name $recovery_services_vault_name \
                                    --container-name $windows_virtual_machine_name \
                                    --item-name $windows_virtual_machine_name \
                                    --backup-management-type AzureIaaSVM
    ```

1. Run [az backup job list](/cli/azure/backup/job#az-backup-job-list) to monitor the backup job.

    ```azurecli
    az backup job list --resource-group $resource_group_name \
                       --vault-name $recovery_services_vault_name \
                       --output table
    ```

    The output is similar to the following example, which shows the backup job is *InProgress*:

    ```output
    Name      Operation        Status      Item Name    Start Time UTC       Duration
    --------  ---------------  ----------  -----------  -------------------  --------------
    a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
    fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
    ```

When the *Status* of the backup job reports *Completed*, your VM is protected with Backup recovery services and has a full recovery point stored.

---

## Clean up resources

[!INCLUDE [terraform-plan-destroy.md](~/azure-dev-docs-pr/articles/terraform/includes/terraform-plan-destroy.md)]

## Troubleshoot Terraform on Azure

[Troubleshoot common problems when using Terraform on Azure](/azure/developer/terraform/troubleshoot).

## Next steps

> [!div class="nextstepaction"]
> [See more articles about Azure Windows VMs](/search/?terms=Azure%20windows%20virtual%20machine%20and%20terraform).

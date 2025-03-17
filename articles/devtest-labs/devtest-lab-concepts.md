---
title: Azure DevTest Labs concepts
description: Learn about some basic Azure DevTest Labs concepts related to labs, virtual machines (VMs), and environments.
ms.topic: conceptual
ms.author: rosemalcolm
author: RoseHJM
ms.date: 03/17/2025
ms.custom: UpdateFrequency2

#customer intent: As a lab user or administrator, I want to understand basic DevTest Labs concepts so I can create, manage, or use labs, VMs, and environments.
---

# DevTest Labs concepts

This article describes key [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) concepts and definitions. DevTest Labs is a service for easily creating, using, and managing Azure virtual machines (VMs) and other resources.

## Labs

A DevTest Labs lab is the infrastructure that encompasses a group of resources such as VMs. DevTest Labs administrators can:

- Add and configure lab users.
- Create ready-made VMs for lab users to claim and use.
- Create and use Azure Resource Manager (ARM) templates to create and configure labs, VMs, and environments.
- Connect artifact and template source control repositories to the lab.
- Let users create and configure their own lab VMs and environments.
- Specify allowed virtual machine (VM) limits, sizes, and configurations.
- Set autoshutdown and autostartup policies.
- Track and manage lab costs.

### Roles

[Azure role-based access control (Azure RBAC)](/azure/role-based-access-control/overview) defines DevTest Labs access and roles. DevTest Labs has three roles that define lab member permissions: **Owner**, **Contributor**, and **DevTest Labs User**.

- Lab **Owners** can do all lab tasks, including creating labs and managing policies and users. For more information about managing user access and roles, see [Add lab owners, contributors, and users](devtest-lab-add-devtest-user.md).

  A lab Owner must have at least Contributor rights in the Azure subscription the lab is in. Azure subscription Owners have access to all resources and user assignments in their subscriptions, so they automatically inherit the lab Owner role.

  Lab Owners can also create and assign custom DevTest Labs roles. For more information, see [Grant user permissions to specific lab policies](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

- Lab **Contributors** can do everything that Owners can, such as create and configure labs and policies, but can't assign or manage users and roles.

- **DevTest Labs Users** can view all lab resources and policies and can create and modify their own VMs and environments, within policy restrictions such as number of VMs per user.

  DevTest Labs Users can't modify lab policies, or change any other users' VMs. DevTest Labs Users automatically have Owner permissions on their own VMs.

### Policies

Lab policies help control costs and reduce waste. For example, policies can automatically shut down lab VMs based on a defined schedule, or limit the number or sizes of VMs per user or lab. For more information, see [Manage lab policies to control costs](devtest-lab-set-lab-policy.md).

## Templates

You can use ARM templates to create and update DevTest Labs labs, environments, VMs, and artifacts.

[!INCLUDE [About Azure Resource Manager](~/reusable-content/ce-skilling/azure/includes/resource-manager-quickstart-introduction.md)] For more information about ARM template structure and properties, see [Template format](/azure/azure-resource-manager/templates/syntax#template-format).

For more information about using ARM templates in DevTestLabs, see:

- [Create labs from ARM templates](create-lab-windows-vm-template.md)
- [Create environments from ARM templates](devtest-lab-create-environment-from-arm.md)
- [Create ARM templates for VMs](devtest-lab-use-resource-manager-template.md)

### Repositories

Lab users can use templates and artifacts from public and private Git source control repositories to create lab VMs and environments. The [DevTest Labs public GitHub repositories](https://github.com/Azure/azure-devtestlab) offer many ready-to-use artifacts and ARM templates.

Lab administrators can also store custom artifacts and ARM templates in private Git repositories and connect the repositories to their labs. Lab users and automated processes can then use the templates and artifacts. You can add the same repositories to multiple labs in your organization, promoting consistency, reuse, and sharing. For more information, see [Add template repositories to labs](devtest-lab-use-resource-manager-template.md#add-template-repositories-to-labs) and [Add an artifact repository to a lab](add-artifact-repository.md).

## Virtual machines

You can use templates, artifacts, custom images, and formulas to create and manage DevTest Labs VMs.

Azure VMs are [on-demand, scalable computing resources](/azure/architecture/guide/technology-choices/compute-decision-tree) that give you the flexibility of virtualization without having to buy and maintain the physical hardware to run it. For more information about Azure VMs, see [Windows virtual machines in Azure](/azure/virtual-machines/windows/overview). 

### Base images

A base image is a VM image that can have software and settings preinstalled and configured. Using base images reduces VM creation time and complexity. Lab administrators can choose which base images to make available for their lab users to use for VM creation. For more information, see [Create and add virtual machines to a lab](devtest-lab-add-vm.md).

### Artifacts

Artifacts are tools, actions, or software you can add to lab VMs during or after VM creation. For example, artifacts can be:

- Tools to install on the VM, like agents, Fiddler, or Visual Studio.
- Actions to take on the VM, such as cloning a repository or joining a domain.
- Applications that you want to test.

For more information, see [Add artifacts to DevTest Labs VMs](add-artifact-vm.md).

Lab administrators can specify mandatory artifacts to be installed on all lab VMs during VM creation. For more information, see [Specify mandatory artifacts for DevTest Labs VMs](devtest-lab-mandatory-artifacts.md).

### Claimable VMs

Lab administrators can prepare VMs with specific configurations and save them to a shared pool, where they appear in the lab's **Claimable virtual machines** list. Any lab user can claim a VM from the claimable pool when they need a VM with that configuration.

After a lab user claims a VM, the VM moves to that user's **My virtual machines** list, and the user becomes the owner of the VM. The VM is no longer claimable or configurable by other users. For more information, see [Create and manage claimable VMs](devtest-lab-add-claimable-vm.md).

### Custom images and formulas

DevTest Labs custom images and formulas are mechanisms for fast VM creation and provisioning.

- A custom image is a VM image created from an existing VM or virtual hard drive (VHD), which can have software and other artifacts installed. Lab users can create identical VMs from the custom image. For more information, see [Create a custom image from a VM](devtest-lab-create-custom-image-from-vm-using-portal.md).

- A formula is a list of default property values for creating a lab VM, such as base image, VM size, virtual network, and artifacts. When you create a VM from a formula, you can use the default values as-is or modify them. For more information, see [Manage Azure DevTest Labs formulas](devtest-lab-manage-formulas.md).

For more information about custom images and formulas, see [Compare custom images and formulas](devtest-lab-comparing-vm-base-image-types.md).

## Environments

A DevTest Labs environment is a collection of Azure platform-as-a-service (PaaS) resources, such as an Azure Web App or a SharePoint farm, that's defined by an ARM template. Lab administrators can add public or privately created environment tamplates to labs, and lab users can use them to quickly create environments. For more information, see [Use ARM templates to create DevTest Labs environments](devtest-lab-create-environment-from-arm.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

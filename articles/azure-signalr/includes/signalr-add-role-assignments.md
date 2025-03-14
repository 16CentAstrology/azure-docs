---
title: include file
description: include file
author: terencefan
ms.service: azure-signalr-service
ms.topic: include
ms.date: 03/12/2025
ms.author: tefa
ms.custom: include file
---

The following steps describe how to assign a SignalR App Server role to a service principal over an Azure SignalR Service resource. For detailed steps, see [Assign Azure roles using the Azure portal](/azure/role-based-access-control/role-assignments-portal.yml).

> [!NOTE]
> A role can be assigned to any scope, including management group, subscription, resource group, or single resource. To learn more about scope, see [Understand scope for Azure RBAC](../role-based-access-control/scope-overview.md).

1. In the [Azure portal](https://portal.azure.com/), go to your Azure SignalR Service resource.

1. Select **Access control (IAM)**.

1. Select **Add** > **Add role assignment**.

   :::image type="content" source="~/reusable-content/ce-skilling/azure/media/role-based-access-control/add-role-assignment-menu-generic.png" alt-text="Screenshot that shows the page for access control and selections for adding a role assignment.":::

1. On the **Role** tab, select **SignalR App Server** or other SignalR built-in roles depends on your scenario.

   | Role                                                                                              | Description                                                                                               | Use case                                                                                                                                     |
   | ------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
   | [SignalR App Server](/azure/role-based-access-control/built-in-roles.md#signalr-app-server)           | Access to the server connection creation and key generation APIs.                                                | Most commonly used for app server with Azure SignalR resource run in **Default** mode.                                                                                                             |
   | [SignalR Service Owner](/azure/role-based-access-control/built-in-roles.md#signalr-service-owner)     | Full access to all data-plane APIs, including REST APIs, the server connection creation, and key/token generation APIs. | For negotiation server with Azure SignalR resource run in **Serverless** mode, as it requires both REST API permissions and authentication API permissions. |
   | [SignalR REST API Owner](/azure/role-based-access-control/built-in-roles.md#signalr-rest-api-owner)   | Full access to data-plane REST APIs.                                                                      | For using [Azure SignalR Management SDK](/azure/azure-signalr/signalr-howto-use-management-sdk) to manage connections and groups, but does **NOT** make server connections or handle negotiation requests.                          |
   | [SignalR REST API Reader](/azure/role-based-access-control/built-in-roles.md#signalr-rest-api-reader) | Read-only access to data-plane REST APIs.                                                                 | Use it when write a monitoring tool that calls readonly REST APIs.                                      |


1. On the **Members** tab, select **User, group, or service principal**, and then choose **Select members**.

1. Search for and select the application to which you want to assign the role.

1. On the **Review + assign** tab, select **Review + assign** to assign the role.

> [!IMPORTANT]
> Azure role assignments might take up to 30 minutes to propagate.

To learn more about how to assign and manage Azure roles, see these articles:

- [Assign Azure roles using the Azure portal](/azure/role-based-access-control/role-assignments-portal.yml)
- [Assign Azure roles using the REST API](/azure/role-based-access-control/role-assignments-rest.md)
- [Assign Azure roles using Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell.md)
- [Assign Azure roles using the Azure CLI](/azure/role-based-access-control/role-assignments-cli.md)
- [Assign Azure roles using Azure Resource Manager templates](/azure/role-based-access-control/role-assignments-template.md)
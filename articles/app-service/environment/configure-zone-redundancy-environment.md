---
title: Configure App Service Environments and Isolated v2 App Service Plans for Zone Redundancy
description: Learn how to configure zone redundancy for App Service Environments and Isolated v2 App Service plans to boost reliabilty and minimize service disruption.
ms.topic: conceptual
ms.service: azure-app-service
ms.date: 07/16/2025
author: anaharris
ms.author: anaharris

---
# Configure App Service Environments and Isolated v2 App Service plans for zone redundancy

An [App Service Environment](./overview.md) is a single-tenant deployment of Azure App Service that integrates with an Azure virtual network. Each App Service Environment deployment requires a dedicated subnet that other resources can't use.

This article describes how to create and modify App Service Environment zone redundancy settings. It also describes how to set up and modify zone redundancy settings for your plan.

For more information about zone redundancy, see [Reliability in an App Service Environment](../../reliability/reliability-app-service-environment.md).

## Configure zone redundancy for an App Service Environment

- **To create a new App Service Environment that includes zone redundancy**, follow the steps to [create an App Service Environment](creation.md). Make sure to select **Enabled** for **Zone redundancy**.

- **To enable or disable zone redundancy** for an existing App Service Environment, use the Azure CLI or Bicep.
    
    # [Azure portal](#tab/portal)
    
    The Azure portal doesn't support this operation. Use the Azure CLI or Bicep instead.
    
    # [Azure CLI](#tab/azurecli)
    
    - To *enable zone redundancy*, set the `zoneRedundant` property to `true`.
    
        ```azurecli
        az resource update \
            --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Web/hostingEnvironments/{aseName} \
            --set properties.zoneRedundant=true
        ```
    
    - To *disable zone redundancy*, set the `zoneRedundant` property to `false`.
    
        ```azurecli
        az resource update \
            --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Web/hostingEnvironments/{aseName} \
            --set properties.zoneRedundant=false
        ```
    
    # [Bicep](#tab/bicep)
    
    - To *enable zone redundancy*, set the `zoneRedundant` property to `true`.
    
        ```bicep
        resource appServiceEnvironment 'Microsoft.Web/hostingEnvironment@2024-11-01' = {
            name: appServiceEnvironmentName
            location: location
            properties: {
                zoneRedundant: true
            }
        }
        ```
    
    - To *disable zone redundancy*, set the `zoneRedundant` property to `false`.
    
    ---
    
    > [!NOTE]
    > A zone redundancy status change in an App Service Environment takes 12 to 24 hours to complete. During the upgrade process, no downtime or performance problems occur.

## Configure Isolated v2 App Service plans with zone redundancy

All App Service plans created in an App Service Environment must use the Isolated v2 pricing tier.

If you enable your App Service Environment to be zone redundant, you must also set the Isolated v2 App Service plans as zone redundant. Each plan has its own independent zone redundancy setting, so you can manually enable or disable zone redundancy on specific plans in an App Service Environment, as long as the environment is configured to be zone redundant.

- **To create a new Isolated v2 App Service plan with zone redundancy**, use the Azure portal, the Azure CLI, or Bicep.
    
    # [Azure portal](#tab/portal)
    
    Follow the guidance to [create an App Service plan](../app-service-plan-manage.md#create-an-app-service-plan). Configure the following settings:
    
    - For **Region**, select your App Service Environment.
    - For **Pricing plan**, select **Isolated v2**.
    - For **Zone redundancy**, select **Enabled**.
    
    # [Azure CLI](#tab/azurecli)
    
    - Set the `--zone-redundant` argument.
    - Set the `--number-of-workers` argument, which is the number of instances, to a value of 2 or more.
    
    ```azurecli
    az appservice plan create \
        -n <app-service-plan-name> \
        -g <resource-group-name> \
        --app-service-environment MyAse \
        --zone-redundant \
        --number-of-workers 2 \
        --sku I1V2
    ```
    
    # [Bicep](#tab/bicep)
    
    - Set the `zoneRedundant` property to `true`.
    - Set the `sku.capacity` property to a value of 2 or more. If you don't define the `sku.capacity` property, the value defaults to 1.
    
    ```bicep
    resource appServicePlan 'Microsoft.Web/serverfarms@2024-11-01' = {
        name: appServicePlanName
        location: location
        hostingEnvironmentProfile: {
            id: '...'
        }
        sku: {
            name: sku
            capacity: 2
        }
        kind: 'linux'
        properties: {
            reserved: true
            zoneRedundant: true
        }
    }
    ```
    
    ---

- **To enable or disable zone redundancy** on an existing Isolated v2 App Service plan, use the Azure portal, the Azure CLI, or Bicep.
    
    # [Azure portal](#tab/portal)
    
    1. In the [Azure portal](https://portal.azure.com), go to your App Service plan.
    1. Select **Settings > Scale out (App Service plan)** in the left navigation pane.
    1. Select **Zone redundancy** to enable zone redundancy. Deselect it to disable it.
     
    :::image type="content" source="./media/configure-zone-redundancy/app-service-plan-zone-redundancy-portal-isolated.png" alt-text="Screenshot of zone redundancy property for an App Service plan in the Azure portal.":::
    
    >[!IMPORTANT]
    >If you have *Rules Based* scaling enabled, you can't use the Azure portal to enable zone redundancy for your App Service plan. You must use the Azure CLI or Bicep and Resource Manager instead.
    
    # [Azure CLI](#tab/azurecli)
    
    - To *enable zone redundancy*, set the `zoneRedundant` property to `true`.
    - Set the `sku.capacity` argument, which is the number of instances, to a value of 2 or more.
    
        ```azurecli
        az appservice plan update \
            -n <app-service-plan-name> \
            -g <resource-group-name> \
            --set zoneRedundant=true sku.capacity=2
        ```
    
    - To *disable zone redundancy*, set the `zoneRedundant` property to `false`.
    
        ```azurecli
        az appservice plan update \
            -n <app-service-plan-name> \
            -g <resource-group-name> \
            --set zoneRedundant=false
        ```
    
    # [Bicep](#tab/bicep)
    
    - To *enable zone redundancy*, set the `zoneRedundant` property to `true`.
    - Set the `sku.capacity` property to a value of 2 or more. If you don't define the `sku.capacity` property, the value defaults to 1.
    
        ```bicep
        resource appServicePlan 'Microsoft.Web/serverfarms@2024-11-01' = {
            name: appServicePlanName
            location: location
            hostingEnvironmentProfile: {
                id: '...'
            }
            sku: {
                name: sku
                capacity: 2
            }
            kind: 'linux'
            properties: {
                reserved: true
                zoneRedundant: true
            }
        }
        ```
    
    - To *disable zone redundancy*, set the `zoneRedundant` property to `false`.
    
    ---
    
    
## Related content

- [Configure App Service plans for zone redundancy](../configure-zone-redundancy.md)
- [Reliability in App Service Environments](../../reliability/reliability-app-service-environment.md)

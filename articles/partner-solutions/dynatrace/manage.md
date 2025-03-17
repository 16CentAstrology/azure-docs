---
title: Manage your Azure Native Dynatrace Service integration
description: This article describes how to manage Dynatrace on the Azure portal. 

ms.topic: how-to
ms.date: 3/13/2025

---

# Manage the Azure Native Dynatrace Service

This article describes how to manage the settings for Dynatrace for Azure.

## Resource overview

To see the details of your Dynatrace resource, select **Overview** in the left pane.

:::image type="content" source="media/manage/resource-overview.png" alt-text="A screenshot of a Dynatrace resource in the Azure portal with the overview displayed in the working pane." lightbox="media/manage/resource-overview.png":::

The details include:

- Resource group name
- Region
- Subscription
- Subscription ID
- Tags
- Dynatrace environment
- Status
- Pricing plan
- Billing term
- Dynatrace account

To manage your resource, select the links next to corresponding details.

Below the essentials, you can navigate to other details about your resource by selecting the links.

- **Get started** provides access to Dashboards, Logs, and Smartscape Topology.
- **Monitoring** provides a summary of resources sending logs to Dynatrace.

## Reconfigure rules for metrics and logs

To change the configuration rules for logs, select **Metrics and logs** in the Resource menu on the left.

For more information, see [Configure metrics and logs](dynatrace-create.md#configure-metrics-and-logs).

## View monitored resources

To view the list of resources emitting logs to Dynatrace, select **Dynatrace environment config > Monitored Resources** in the Resource menu.

> [!TIP]
> You can filter the list of resources by resource type, resource group name, region, and whether the resource is sending logs and/or metrics. 

The column **Logs to Dynatrace** indicates whether the resource is sending logs to Dynatrace. 
The column **Metrics to Dynatrace** indicates whether the resource is sending metrics to Dynatrace.

## Monitor resources using Dynatrace OneAgent

You can install Datadog OneAgents on virtual machines, App Service extensions, and Azure Arc Machines.

### Virtual Machines

To monitor resources for virtual machines, select **Dynatrace environment config > Virtual Machines** from the Resource pane.

> [!IMPORTANT]
>
> - Dynatrace OneAgent can only be installed on virtual machines that are running. If the virtual machine is stopped, installing the Dynatrace OneAgent is disabled.   
> - If a virtual machine shows that a OneAgent is installed, but the option Uninstall extension is disabled, then the agent was configured through a different Dynatrace resource in the same Azure subscription. To make any changes, please go to the other Dynatrace resource in the Azure subscription.

[!INCLUDE [agent](../includes/agent.md)]

### App Service

To monitor resources for App Service, select **Dynatrace environment config > App Service** from the Resource pane.

[!INCLUDE [agent](../includes/agent.md)]

### Azure Arc Machines
 
### Azure Arc Machines

To monitor resources for Azure Arc Machines, select **Datadog organization configurations > Azure Arc Machines** from the Resource pane.

[!INCLUDE [agent](../includes/agent.md)]

## Reconfigure single sign-on

To enable single sign-on through Microsoft Entra ID:

1. Select **Settings > Single sign-on** from the Resource pane.

1. Select the check box.

## Delete a Datadog resource

[!INCLUDE [delete-resource](../includes/delete-resource.md)]

## Related content

- For help with troubleshooting, see [Troubleshooting Dynatrace integration with Azure](dynatrace-troubleshoot.md).
- [Get started with infrastructure monitoring](https://www.dynatrace.com/support/help/how-to-use-dynatrace/hosts/basic-concepts/get-started-with-infrastructure-monitoring)


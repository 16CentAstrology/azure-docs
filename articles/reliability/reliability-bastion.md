---
title: Reliability in Azure Bastion
description: Find out about reliability in Azure Bastion, including availability zones and multi-region deployments.
author: anaharris-ms 
ms.author: anaharris
ms.topic: reliability-article
ms.custom: subject-reliability, references_regions
ms.service: azure-bastion
ms.date: 10/25/2024
---

# Reliability in Azure Bastion

This article describes reliability support in Azure Bastion and covers both intra-regional resiliency with [availability zones](#availability-zone-support) and information on [multi-region deployments](#multi-region-support).

Because resiliency is a shared responsibility between you and Microsoft, this article also covers ways for you to build a resilient solution that meets your needs.

> [!IMPORTANT]
> Zone redundancy features for Azure Bastion resources are currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

Azure Bastion is a fully managed PaaS service that you provision to securely connect to virtual machines via a private IP address. It provides secure and seamless RDP/SSH connectivity to your virtual machines directly over TLS from the Azure portal, or via the native SSH or RDP client already installed on your local computer. When you connect via Azure Bastion, your virtual machines don't need a public IP address, agent, or special client software.

## Production deployment recommendations

For production deployments, you should:

 - Use standard or premium Azure Bastion resources. While the basic SKU supports zone redundancy, we don't recommend it for production use.
 - [Enable zone redundancy](#availability-zone-support) (in preview), if your Azure Bastion resources are in a supported region.

## Transient faults

Transient faults are short, intermittent failures in components. They occur frequently in a distributed environment like the cloud, and they're a normal part of operations. They correct themselves after a short period of time. It's important that your applications handle transient faults, usually by retrying affected requests.

If transient faults affect your virtual machine or Azure Bastion host, clients using the secure sockets host (SSH) and remote desktop connection (RDP) protocols typically retry automatically.

## Availability zone support

You can configure Azure Bastion to be *zone-redundant*, so that your resources are spread across multiple [availability zones](../reliability/availability-zones-overview.md). When you spread resources across availability zones you can achieve resiliency and reliability for your production workloads.

You can specify which availability zone or zones an Azure Bastion resource should be deployed to. Azure Bastion spreads your instances across those zones. In the following diagram, Azure Bastion has three instances spread across three zones:

:::image type="content" source="media/reliability-bastion/bastion-instances-zones.png" alt-text="Diagram that shows Azure Bastion with three instances, each in a separate availability zone." border="false":::

If you specify more availability zones than you have instances, Azure Bastion spreads instances across as many zones as it can. If an availability zone is unavailable, the instance in the faulty zone will be replaced with another instance in a healthy zone.

> [!NOTE]
> Azure Bastion support for zone redundancy is currently in preview.

### Requirements

To configure Azure Bastion resources with zone redundancy you must deploy with the Basic, Standard, or Premium SKUs.

The Developer SKU is intended for non-production use. It doesn't support zone redundancy. However, in the event of a region failure, Azure Bastion attempts to route traffic through different infrastructure.

### Regions supported

Zone-redundant Azure Bastion resources can be deployed into the following regions:

| Americas | Europe | Middle East | Africa | Asia Pacific |
|---|---|---|---|---|
| Canada Central | North Europe | Qatar Central | South Africa North | Australia East |
| Central US | Sweden Central | | |
| East US | UK South
| East US 2 | West Europe | | |
| West US 2  | | | |

### Cost

There's no additional cost to use zone redundancy for Azure Bastion.

### Configure availability zone support

**New resources:** When you deploy a new Bastion resource in a [region that supports availability zones](#regions-supported), you select the specific zones you want to deploy to.  For zone redundancy, you must select multiple zones.

   >[!IMPORTANT]
   > You can't change the availability zone setting after you deploy your Bastion resource.

When you select which availability zones to use, you're actually selecting the *logical availability zone*. If you deploy other workload components in a different Azure subscription, they might use a different *logical availability zone* number to access the same physical availability zone. For more information, see [Physical and logical availability zones](./availability-zones-overview.md#physical-and-logical-availability-zones).

**Migration:** It's not possible to migrate from non-availability zone support to availability zone support. Instead, you need to create a Bastion resource in the new region and delete the old one.

### Traffic routing between zones

When you initiate an SSH or RDP session, it can be routed to an Azure Bastion instance in any of the availability zones you selected.

It's possible that a session might be sent to an Azure Bastion instance in an availability zone that's different from the virtual machine you're connecting to. In the following diagram, a request from the user is sent to an Azure Bastion instance in zone 2, while the virtual machine is in zone 1:

:::image type="content" source="./media/reliability-bastion/bastion-cross-zone.png" alt-text="Diagram that shows Azure Bastion with three instances. A user request goes to an Azure Bastion instance in zone 2 and is sent to a virtual machine in zone 1." border="false":::

In most scenarios, the small amount of cross-zone latency isn't significant. However, if you have unusually stringent latency requirements for your Azure Bastion workloads, you should deploy a dedicated single-zone Azure Bastion instance in the virtual machine's availability zone. However, this configuration doesn't provide zone redundancy, and we don't recommend it for most customers.

### Zone-down experience

**Detection and response:** Azure Bastion detects and responses to failures in an availability zone. You don't need to do anything to initiate an availability zone failover.

**Active requests:** When an availability zone is unavailable, any RDP or SSH connections in progress that use an Azure Bastion instance in the faulty availability zone are terminated and need to be retried.

If the virtual machine you're connecting to isn't in the affected availability zone, the virtual machine continues to be accessible. See [Reliability in virtual machines: Zone down experience](./reliability-virtual-machines.md#zone-down-experience) for more information on the VM zone down experience.

**Traffic rerouting:** New connections use Azure Bastion instances in the surviving availability zones. Overall, Azure Bastion continues to remain operational.

### Failback

When the availability zone recovers, Azure Bastion:

- Automatically restores instances in the availability zone.
- Removes any temporary instances created in the other availability zones
- Reroutes traffic between your instances as normal.

### Testing for zone failures

The Azure Bastion platform manages traffic routing, failover, and failback for zone-redundant Azure Bastion resources. Because this feature is fully managed, you don't need to initiate anything or validate availability zone failure processes.

## Multi-region support

Azure Bastion is deployed within virtual networks or peered virtual networks, and is associated with an Azure region. Azure Bastion is a single-region service. If the region becomes unavailable, your Bastion resource is also unavailable.

Azure Bastion supports reaching virtual machines in globally peered virtual networks, but if the region that hosts your Azure Bastion resource is unavailable, you won't be able to use your Bastion resource. For higher resiliency, if you deploy your overall solution into multiple regions with separate virtual networks in each region, you should deploy Azure Bastion into each region.

If you have a disaster recovery (DR) site in another Azure region, ensure you deploy Azure Bastion into the virtual network in that region.

## Service-level agreement (SLA)

The service-level agreement (SLA) for Azure Bastion describes the expected availability of the service, and the conditions that must be met to achieve that availability expectation. To understand those conditions, it's important that you review the [Service Level Agreements (SLA) for Online Services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

## Related content

- [Reliability in Azure](./overview.md)

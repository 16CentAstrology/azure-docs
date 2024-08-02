---
title: What are Azure Extended Zones (Preview)?
description: Learn about Azure Extended Zones.
author: halkazwini
ms.author: halkazwini
ms.service: azure-extended-zones
ms.topic: overview
ms.date: 08/02/2024
---

# What are Azure Extended Zones (Preview)?

Azure Extended Zones are small-footprint extensions of Azure placed in metros, industry centers, or a specific jurisdiction to serve low latency and data residency workloads. Azure Extended Zones supports virtual machines (VMs), containers, storage, and a selected set of Azure services and can run latency-sensitive and throughput-intensive applications close to end users and within approved data residency boundaries.
 
Azure Extended Zones are part of the Microsoft global network that provides secure, reliable, high-bandwidth connectivity between applications that run on an Azure Extended Zone close to the user. Extended Zones address low latency and data residency by bringing all the goodness of the Azure ecosystem (access, user experience, automation, security, etc.) closer to the customer or their jurisdiction. Azure customers can provision and manage their Azure Extended Zones resources, services, and workloads through the Azure portal and other essential Azure tools.

## Key scenarios

The key scenarios Azure Extended Zones enable are: 

- **Latency**: users want to run their resources, for example, media editing software, remotely with low latency.

- **Data residency**: users want their applications data to stay within a specific geography and might essentially want to host locally for various privacy, regulatory, and compliance reasons.

The following diagram shows some of the industries and use cases where Azure Extended Zones can provide benefits.

:::image type="content" source="./media/overview/azure-extended-zones-industries.png" alt-text="Diagram that shows industries and use cases where Azure Extended Zones can provide benefits." lightbox="./media/overview/azure-extended-zones-industries.png":::

## Service offerings for Azure Extended Zones

Azure Extended Zones enable some key Azure services for customers to deploy. The control plane for these services remains in the region and the data plane is deployed at the Extended Zone site, resulting in a smaller Azure footprint.

The following diagram shows how Azure services are deployed at the Azure Extended Zones location.

:::image type="content" source="./media/overview/azure-extended-zone-services.png" alt-text="Diagram that shows available Azure services at an Azure Extended Zone." lightbox="./media/overview/azure-extended-zone-services.png":::


The following table lists key services that are available in Azure Extended Zones:

| Service category | Available services |
| ------------------ | ------------------- |
| **Compute** | Azure virtual machines (general purpose: A, B, D, E, and F series and GPU NVadsA10 v5 series) <br> Virtual Machine Scale Sets <br> Azure Kubernetes Service |
| **Networking** | Azure Private Link <br> Standard public IP <br> Virtual networks <br> Virtual network peering <br> ExpressRoute <br> Azure Standard Load Balancer <br> DDoS (Standard protection) |
| **Storage** | Azure managed disks <br> Azure Premium Page Blobs <br> Azure Premium Block Blobs <br> Azure Premium Files <br>  Azure Data Lake Storage Gen2<br> Hierarchical Namespace <br>Azure Data Lake Storage Gen2 Flat Namespace <br> Change Feed <br> Blob Features <br> - SFTP <br> - NFS |
| **BCDR** | Azure Site Recovery <br> Azure Backup |

## Related content

- [Quickstart: Deploy a virtual machine in an Extended Zone](deploy-vm-portal.md).
- [Tutorial: Back up an Azure Extended Zone virtual machine](backup-virtual-machine.md).
- [Azure Extended Zones frequently asked questions (FAQ)](faq.md).

---
title: VMware Cloud Director with Azure VMware Solution Networking 
description: This article explains how to enable network for VMware Cloud director tenants on Azure VMware Solution 
ms.topic: how-to
author: rdutt
ms.service: azure-vmware
ms.date: 4/24/2025
---

# VMware Cloud Director on Azure VMware Solution network scenarios 

VMware Cloud Director on Azure VMware Solution offers a robust platform for managing multitenancy, enabling organizations to create secure, isolated virtual data centers. This article provides various network connectivity scenarios for VMware Cloud Director tenants, including connecting to the internet and accessing Azure services. By leveraging the flexibility of VMware Cloud Director and Azure VMware Solution, tenants can achieve seamless integration with external networks and Azure resources, ensuring efficient and scalable operations.


## Connect VMware Cloud Director tenants on Azure VMware Solution to internet

- To achieve internet connectivity, the provider can create organization virtual data center's with an organization edge gateway (Tier-1) router and assign Public IP for NAT configuration. 

-  Learn about how to [Turn on public IP addresses to an NSX Edge node for VMware NSX](enable-public-ip-nsx-edge.md).

- VMware Cloud Director Tenants can use the above NSX-T public IP address for SNAT configuration to enable Internet access for virtual machine in tenant's organization virtual data center.

:::image type="content" source="media/vmware-vcd/VCD_internet_diag.png" alt-text="Diagram showing how tenants in VMware Cloud Director connects to internet in Azure VMware Solution." border="false" lightbox="media/vmware-vcd/VCD_internet_diag.png":::

- Organization virtual data center Edge gateway has default DENY ALL firewall rule. Virtual datacenter organization administrators need to open appropriate ports to allow access through the firewall by adding a new firewall rule.

**Overlapping IP address**

> [!Note]
>  To manage overlapping IP address, use NAT to prevent conflicts in end-to-end routing scenarios.


## Connect VMware Cloud Director tenants workloads with Azure services

- To enable access to Azure services in Azure vNet, configure Azure VNet with an azure vPN gateway. 
- Follow this document to create an [Azure virtual network gateway](tutorial-configure-networking.md)
- A site-to-site vpn is established between tenant’s organization virtual data center and azure VNet. To achieve this connectivity, the tenant provides a public IP to the organization virtual datacenter. Both source and destination of the tunnel should have identical settings for IKE,SA, DPD etc.
- The organization virtual datacenter administrator can configure IPsec VPN connectivity using VMware Cloud Director.

> [!Note]
>  VMware Cloud Director supports a policy-based VPN. Azure VPN gateway configures route-based VPN by default and to configure policy-based VPN policy-based selector needs to be enabled.

- Organization virtual data center edge router firewall denies traffic by default. You need to apply specific rules to enable connectivity. Use the following steps to apply firewall rules.

:::image type="content" source="media/vmware-vcd/VCD_Azure_Services_diag.png" alt-text="Diagram showing how tenants in VMware Cloud Director connects to azure services in Azure VMware Solution." border="false" lightbox="media/vmware-vcd/VCD_Azure_Services_diag.png":::

## Related topics

Learn about [How to enable VMware Cloud Director on Azure VMware Solution](enable-vmware-vcd-with-azure.md)

Learn about [VMware Cloud Director](https://techdocs.broadcom.com/us/en/vmware-cis/cloud-director/vmware-cloud-director/10-6/overview.html)

Learn about [Architecture - Network interconnectivity - Azure VMware Solution](architecture-networking.md)

---
title: Reliability in Azure Kubernetes Service (AKS)
description: Find out about reliability in Azure Kubernetes Service (AKS), including availability zones and multi-region deployments.
author: schaffererin
ms.author: schaffererin
ms.topic: reliability-article
ms.custom: subject-reliability, references_regions #Required  - use references_regions if specific regions are mentioned.
ms.service: azure-kubernetes-service
ms.date: 03/14/2025
#Customer intent: As an engineer responsible for business continuity, I want to understand who need to understand the details of how AKS works from a reliability perspective and plan disaster recovery strategies in alignment with the exact processes that Azure services follow during different kinds of situations. 
---

# Reliability in Azure Kubernetes Service (AKS)

This article describes reliability support in [Azure Kubernetes Service (AKS)](../app-service/overview.md). It addresses zone resiliency, [availability zones](./availability-zones-overview.md), and multi-region deployments.

## Reliability architecture overview

When you create an AKS cluster, the Azure platform automatically creates and configures a [control plane](/azure/aks/core-aks-concepts#control-plane) with the API server, etcd, the scheduler, and other pods required to manage your workload. The control plane is managed by AKS and doesn't require any configuration or management by you. AKS also deploys a [system node pool](/azure/aks/use-system-pools) to your subscription that hosts your add-ons and additional pods running in the *kube-system* namespace. Once this initial setup is complete, you can [add or delete node pools](/azure/aks/create-node-pools) for your own user workloads.

:::image type="content" source="./media/reliability-aks/control-plane-and-nodes.png" alt-text="Screenshot of the Kubernetes control plane and node components.":::

**Resiliency is a shared responsibility between you and Microsoft**. While AKS can ensure the reliability of the managed components, it's important to take your workload requirements into consideration when deploying your applications and selecting your node pool configurations.

AKS only handles compute and the management of compute resources. AKS requires deep integration with other Azure components, like load balancers, virtual networks, and storage. When you deploy your workloads, you need to ensure that the other Azure components are also configured for resiliency by following the best practices for those services.

## Production deployment recommendations

For recommendations on how to deploy production workloads in AKS, see the following articles:

- [Deployment and cluster reliability best practices for Azure Kubernetes Service (AKS)](/azure/aks/best-practices-app-cluster-reliability)
- [High availability and disaster recovery overview for Azure Kubernetes Service (AKS)](/azure/aks/ha-dr-overview)
- [Zone resiliency considerations for Azure Kubernetes Service (AKS)](/azure/aks/aks-zone-resiliency)

## Transient faults

[!INCLUDE [Transient fault description](includes/reliability-transient-fault-description-include.md)]
	
When you use AKS, transient faults can occur due to a variety of reasons, including application crashes, pod scaling and balancing operations, node patching, and temporary infrastructure failures such as hardware or networking issues.
	
You can't eliminate all transient faults. It's important that clients accessing your applications hosted in AKS are prepared to retry failed requests and follow other transient fault handling recommendations. However, you can minimize the likelihood of transient faults, and you can avoid or mitigate the downtime caused by these events, by following Kubernetes and Azure best practices in your deployment, such as:
	
- **Set Pod Disruption Budgets (PDBs)**: You can set PDBs in your pod YAML to specify how many pods you need to have in a `Ready` state at any given point of time. When set, AKS ensures the availability of minimum replicas when running operations to cordon and drain the nodes. If a PDB can't be satisfied during processes like upgrades, the pod will continue to function and the operation might fail. For more information, see [Pod Disruption Budgets (PDBs)](azure/aks/best-practices-app-cluster-reliability#pod-disruption-budgets-pdbs).
- **Use `maxUnavailable`**: You can set this to define the maximum number of replicas that can become unavailable at any given point of time. As an example, if you're performing a rolling restart, AKS will ensure that no more than the `maxUnavailable` number of pods are being churned at a given point of time. For more information, see [maxUnavailable](/azure/aks/best-practices-app-cluster-reliability#maxunavailable).
- **Follow deployment best practices**: Pod replicas can also fail due to application issues. For more information about how to handle these issues, see the [Deployment level best practices for AKS cluster reliability](/azure/aks/best-practices-app-cluster-reliability#deployment-level-best-practices).
	
> [!NOTE]
> If you want AKS to validate your deployments for adherence to best practices and provide blocking or warning notifications as necessary, you can use Deployment Safeguards (Preview), a managed offering which helps enforce product best practices before your code gets deployed to the cluster. For more information, see [Use deployment safeguards to enforce best practices in Azure Kubernetes Service (AKS) (Preview)](/azure/aks/deployment-safeguards).

## Availability zone support

[!INCLUDE [AZ support description](includes/reliability-availability-zone-description-include.md)]

The AKS control plane is zone resilient by default. For any customer node pools that you deploy, you must enable availability zone support to ensure that your workloads are resilient to availability zone failures.

### Considerations

Configure AKS for zone redundancy to spread resources across multiple availability zones, enhancing resiliency and reliability for production workloads in a region. You can ensure this by implementing the following:

- **Deploy multiple replicas**: Kubernetes spreads your pods across nodes based on node labels. To spread your workload across zones, you need to make sure you deploy multiple replicas of your pod. For instance, if you configure the node pool with three zones, but only deploy a single replica of your pod, your deployment won't be zone resilient.
- **Enable automatic scaling**: Kubernetes node pools provide manual and automatic scaling options. With manual scaling, you can add or delete nodes as needed, and pending pods wait until you scale up a node pool. With AKS managed scaling (using the [cluster autoscaler](/azure/aks/cluster-autoscaler) or [node autoprovisioning (NAP)](/azure/aks/node-autoprovision)), AKS scales the node pool based on pod needs within your subscription's SKU quota and capacity. This ensures that your pods are scheduled on available nodes in the up zones.
- **Set pod topology constraints**: Use pod topology spread constraints to control how pods are spread across different nodes or zones. This helps you achieve high availability and resiliency and efficient resource utilization. If you prefer to strictly spread across zones, you can set them to force a pod into a pending state to maintain the balance of pods across zones. For more information, see [Pod topology spread constraints](/azure/aks/best-practices-app-cluster-reliability#pod-topology-spread-constraints).
- **Configure zone resilient networking**: If your pods serve external traffic, configure your cluster network architecture using services like [Azure Application Gateway v2](../application-gateway/overview-v2.md), [Standard Load Balancer](../load-balancer/load-balancer-overview.md), or [Azure Front Door](../frontdoor/front-door-overview.md).
- **Ensure dependencies are zone resilient**: Most AKS applications use other services for storage, security, or networking. Make sure you review the zone resiliency recommendations for those services as well.

### Zone-down experience

In the event of a zone down, the control plane automatically fails over. If your node pools use availability zones and follow [zone resiliency best practices](#considerations), you can expect AKS to bring up nodes and replicas in the zones that are up and running. This is done automatically when using managed solutions like cluster autoscaler or NAP. Without autoscaling, they will remain pending or wait for manual intervention to scale up the node pool. AKS will also attempt to rebalance the pods across the available zones. If you choose to manually scale your node pool, in a zone down scenario, your pods might be left pending if there are no nodes available in the up zones. Along with this, scaling up in the remaining zones is also subject to the availability of SKU quota and capacity.

AKS doesn't currently notify you when a zone is down. You can use your node or pod health metrics to monitor the health of your nodes and pods.

### Cost

There's no additional charge to enable availability zone support in AKS. You pay for the virtual machines (VMs) and other resources that you deploy in the availability zones.

### Configure availability zone support

- **Create a new AKS cluster with availability zone support**: To configure availability zone support, see [Create an Azure Kubernetes Service (AKS) cluster that uses availability zones](/azure/aks/availability-zones).
- **Migration**: You can't enable availability zone support after you create a cluster. Instead, you need to create a new cluster with availability zone support enabled and delete the old one.
- **Disable availability zone support**: You can't disable availability zone support after you create a cluster. Instead, you need to create a new cluster with availability zone support disabled and delete the old one.

### Traffic routing between zones

When you configure availability zone support in AKS, the Azure platform automatically configures the necessary networking components to route traffic between the zones. This includes configuring the load balancers and virtual networks to ensure that traffic is routed to the correct nodes in the correct zones.

In the event of a zone down, the load balancer should redirect any new traffic to healthy pods in the up zones. Existing traffic might experience disruptions, as some requests can fail and latency can increase.

### Data replication between zones

If you're running a stateless workload, you should use managed Azure services, such [Azure Databases](https://azure.microsoft.com/products/category/databases/), [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview), or [Azure Storage](https://azure.microsoft.com/products/category/storage/) to store the application data. Using these services ensures your traffic can be moved across nodes and zones without risking data loss or impacting the user experience. You can use Kubernetes [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), [Services](https://kubernetes.io/docs/concepts/services-networking/service/), and [Health Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) to manage stateless pods and ensure even distribution across zones.

For stateful workloads, you can rely on Azure zone redundant storage (ZRS) to ensure that your data is replicated across multiple availability zones within a region. For more information, see [Choose the right disk type based on application needs](/azure/aks/aks-zone-resiliency#make-your-storage-disk-decision).

### Testing for zone failures

You can test your resiliency to availability zone failures using the following methods:

- [Cordon and drain nodes in a single availability zone](/azure/aks/aks-zone-resiliency#method-1-cordon-and-drain-nodes-in-a-single-az)
- [Simulate an availability zone failure using Azure Chaos Studio](/azure/aks/aks-zone-resiliency#method-2-simulate-an-az-failure-using-azure-chaos-studio)

## Multi-region support

AKS clusters are single region resources by default. If you need to deploy your Kubernetes workload to multiple Azure regions, you have two categories of options to manage the orchestration of these clusters:

For a simpler and more managed experience, you can use [Azure Kubernetes Fleet Manager (Fleet)](/azure/kubernetes-fleet/overview). Fleet enables you to manage a set of AKS clusters as a single unit, and those clusters can be distributed across multiple Azure regions. With Fleet, you can automate certain aspects of cluster management, such as cluster and node image upgrades, and you can use its traffic distribution capabilities to spread traffic across the clusters and automatically fail over if a region is unavailable.

If your workload requires more nuanced control over the different components of inter-region failovers, you can orchestrate them yourself with the an active-active or active-passive deployment model. For more information, see [High availability and disaster recovery overview for Azure Kubernetes Service (AKS)](/azure/aks/ha-dr-overview).

## Backups

Azure Backup supports backing up AKS cluster resources and persistent volumes attached to the cluster using a backup extension. The Backup vault communicates with the AKS cluster through the extension to perform backup and restore operations.

For more information, see the following articles:

- [About AKS backup using Azure Backup (preview)](/azure/backup/azure-kubernetes-service-backup-overview)
- [Back up AKS using Azure Backup (preview)](/azure/backup/azure-kubernetes-service-cluster-backup)

For most solutions, you shouldn't rely exclusively on backups. Instead, use the other capabilities described in this guide to support your resiliency requirements. However, backups protect against some risks that other approaches don't. For more information, see [What are redundancy, replication, and backup?](concept-redundancy-replication-backup.md).

## Service-level agreement

The service-level agreement (SLA) for Azure Logic Apps describes the expected availability of the service. This agreement also describes the conditions to meet for achieving this expectation. To understand these conditions, make sure that you review the [Service Level Agreements (SLA) for Online Services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

AKS offers three pricing tiers for cluster management: **Free**, **Standard**, and **Premium**. For more information, see [Free, Standard, and Premium pricing tiers for Azure Kubernetes Service (AKS) cluster management](/azure/aks/free-standard-pricing-tiers). The Free tier enables you to use AKS to test your workloads. The Standard and Premium tiers are designed for production workloads. When you deploy an AKS cluster with availability zones enabled, the uptime percentage defined in the SLA increases. However, you're only covered by the SLA if you deploy a cluster in the Standard or Premium pricing tiers.

## Related content

For more information, see [Reliability in Azure](/azure/availability-zones/overview.md).

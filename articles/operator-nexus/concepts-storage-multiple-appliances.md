---
title: Azure Operator Nexus multiple storage appliances
description: Lean about Azure Operator Nexus support for multiple storage appliances.
author: pjw711
ms.author: speterwhiting
ms.service: azure-operator-nexus
ms.topic: conceptual
ms.date: 03/12/2025
ms.custom: template-concept
---

# Azure Operator Nexus multiple storage appliances

The storage appliance in Azure Operator Nexus provides highly available, persistent storage to containerized and virtualized workloads. Azure Operator Nexus hardware is organized into compute racks and an aggregator rack. The aggregator rack contains space for two storage appliances. Azure Operator Nexus instances always require one storage appliance; the second storage appliance is optional.

Customers can choose to deploy a second storage appliance when their workloads require more capacity than a single storage appliance can provide.

## Which storage appliance is which?

The spaces in the aggregator rack reserved for storage appliances are called storage appliance rack slots. The aggregator rack contains two rack slots reserved for storage appliances. The storage appliance in rack slot 1 is always the first storage appliance. If a second storage appliance is present, then it is in rack slot 2.

## Hardware prerequisites

Azure Operator Nexus only supports a second storage appliance for instances that meet the following conditions:

- The instance hardware matches the 2.0.x or later bills of material (BOMs).
- All Pure storage appliances have R4 controllers.

The Azure Operator Nexus SKUs that support a second storage appliance are documented in the [supported SKUs documentation](/reference-operator-nexus-skus.md). The storage appliances don't have to have the same capacity configurations. All supported capacity configurations are listed in the [supported storage appliances](/reference-near-edge-storage.md) documentation.

## Supported deployment models

Azure Operator Nexus only supports deploying a second storage appliance at initial Nexus instance install time. There's no support for adding a second storage appliance to an existing Nexus instance. Any existing instance that requires a second storage appliance must be reinstalled.

The deployment process for storage appliances has several prerequisites before you can deploy the Azure Operator Nexus software. The pre-requisities for the second storage appliance are the same as for the first storage appliance, with minor configuration differences. The prerequisites are fully documented for Nexus instances with one or two storage appliances in the [how-to documentation](./howto-azure-operator-nexus-prerequisites.md).

## Supported function

### Storage appliance management through Azure

Azure Operator Nexus automates provisioning the storage appliances when the Nexus cluster is installed. Nexus also manages all aspects of storage appliance configuration and ongoing lifecycle operations that are required for volume orchestration, volume management, secure communications, and observability. This functionality behaves identically for both storage appliances.

### Nexus-volume storage class

Azure Operator Nexus supports creating Persistent Volume Claims (PVCs) using the *nexus-volume* storage class. Nexus-volume PVCs are backed by a volume on the storage appliance which is created and managed by Azure Operator Nexus. You can select the storage appliance to provide the backing storage by using the `storageApplianceName` annotation.

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: testPvc
  namespace: default
  annotations:
    storageApplianceName: exampleStorageAppliance
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 107Mi
  storageClassName: nexus-volume
  volumeMode: Block
  volumeName: testVolume
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 107Mi
  phase: Bound
```

`storageApplianceName` must match the Azure resource name of the storage appliance resource managed by your Azure Operator Nexus cluster on which you want to create the volume backing your PVC. If there's no `storageApplianceName`, or if the `storageApplianceName` doesn't match a storage appliance resource managed by your Azure Operator Nexus cluster, Azure Operator Nexus places the volume on the first storage appliance.

#### Nexus-volume limitations

- Azure Operator Nexus doesn't support moving a PVC from one storage appliance to another. Attempts to change the `storageApplianceName` annotation fail.
- There's no support for placing volumes on a specific storage appliance when creating volumes through the Azure Resource Manager APIs. All volumes created directly through Azure Resource Manager will be placed on the storage appliance in rack slot 1.

### Nexus-shared storage class

Azure Operator Nexus provides a shared filesystem storage solution for containerized workloads: the *nexus-shared* storage class. This storage class provides a highly available shared storage solution by enabling multiple pods in the same Nexus Kubernetes cluster to concurrently access and share the same volume. The *nexus-shared* storage class is backed by a highly available storage service. This service is deployed and managed by the Cloud Service Network (CSN) resource and is in turn backed by volumes on a storage appliance. Individual PVCs consume storage from the CSN-managed storage service, rather than directly from the storage appliance.

You can create the shared storage service on either storage appliance when the CSN is created. All nexus-shared PVCs using that shared storage service consume storage from the storage appliance backing the shared service. You can't place a specific nexus-shared PVC on a specific storage appliance. If no storage appliance configuration is provided at CSN creation time, or if the configuration doesn't match a storage appliance, the shared storage service uses the first storage appliance.

See [How to create shared storage on a specific storage appliance](howto-storage-multi-appliance-nfs.md) for instructions on creating the shared storage service on a specific storage appliance.

#### Nexus-shared limitations

- Azure Operator Nexus doesn't support moving the shared storage service from one storage appliance to another. Attempts to change the storage appliance backing the shared storage service have no effect.

### Persistent storage for virtual machines

Nexus VMs support persistent OS disks and data disks backed by the storage appliance. There's no support for placing these disks on the second storage appliance. All VM disks are placed on the storage appliance in rack slot 1. For more information, see [Azure Operator Nexus storage for virtual machines](/concepts-storage-virtual-machine.md).

### Metrics, logs, and monitoring

The second storage appliance appears as an independent resource in Azure, of type NetworkCloud/storageAppliance. A Nexus instance with two storage appliances have two Storage Appliance Azure Resources. These resources are functionally identical: they share an API definition; and all supported metrics, documented in [List of Metrics Collected in Azure Operator Nexus](/list-of-metrics-collected.md), function identically on both storage appliances.

Nexus also makes [storage appliance logs available](/list-logs-available.md#storage-appliance). Audit and alert logs are streamed on a per-resource basis. System logs are delivered in a combined stream that includes logs from both storage appliances.

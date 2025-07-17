---
title: Publisher Resource Preview Management
description: Learn about the Publisher Resource Preview Management feature in Azure Network Function Manager.
author: sherrygonz
ms.author: sherryg
ms.date: 09/11/2023
ms.topic: concept-article
ms.service: azure-operator-service-manager
ms.custom:
---

# Publisher Resource Preview Management feature

This article introduces the Publisher Resource Preview Management feature in Azure Network Function Manager. The Azure Network Function Manager Publisher API offers partners a seamless Azure Marketplace experience for onboarding network functions and network service designs.

The Publisher API introduces features that enable Network Function (NF) Publishers and Service Designers to manage network function definitions (NFDs) and network service designs (NSDs) in various modes. These modes empower partners to exercise control over the usage of NFDs and NSDs. This control allows partners to target specific subscriptions, target all subscriptions, or deprecate a network function definition version (NFDV) or a network service design version (NSDV) if there are regressions. This article delves into the specifics of these modes.

The Publisher Resource Preview Management feature in Azure Network Function Manager empowers partners to seamlessly manage network function definitions and their versions. With the ability to control deployment states, access privileges, and version management, partners can provide a smooth experience for their customers while maintaining the quality and stability of their offerings.

## Considerations for tenants, subscriptions, and regions

- Publisher NSDV and NFDV resources must be in the same Azure tenant as site network service (SNS) resources.

- NSDV and NFDV states are key for cross-subscription:
  - **Preview**: The SNS is deployable in the same subscription as the NSDV or NFDV.
  - **Active**: The SNS is deployable in any subscription.
- Publisher resources can be in different Azure Core or Azure Operator Nexus regions from SNS resources.

- Publisher names must be unique within a region.

- SNS resources can reference configuration group values (CGVs) from any region, but they can reference site resources only from the same region.

- CGVs can reference a configuration group schema (CGS) in any region.

- Network functions:
  - Can reference an NFDV from any region.
  - Must reference Azure Stack Edge from the same region, if they're hosted on Azure Stack Edge.

- The Azure Resource Manager template within a virtualized network function (VNF) must deploy resources to the same region as the network function.

- Containerized network functions (CNFs) can reference a custom location from any region.

## NFDV and NSDV states

|State  |Description  |Users  |Is immutable  |
|---------|---------|---------|---------|
|**Preview**     |     Default state upon NFDV or NSDV creation; indicates pending testing.    |    Same subscription as the publisher.     |    No     |
|**Active**    |   Signifies readiness for customer usage. Artifacts must be immutable with `artifactManifestState` uploaded.    |    Access based on Remote Blob Store (RBS) for any subscription in the same tenant.     |      Yes   |
|**Deprecated**     |  Implies that a regression is found; prevents new deployments from this version.       |    Can't be deployed.     |     Yes    |

## Artifact manifest states

- **Uploading** means the state is mutable and the artifacts within the manifest can be altered.
- **Uploaded** means the state is immutable and the artifacts within the manifest can't be altered.

Immutable artifacts are tested artifacts that can't be modified or overwritten. Use of immutable artifacts with Azure Operator Service Manager helps ensure the consistency, reliability, and security of its artifacts across environments and platforms. NFDVs and NSDVs with a version state of **Active** are enforced to deploy immutable artifacts.  

### Update the artifact manifest state

To change the state of an artifact manifest resource, use the following Azure CLI command:

```azurecli
  az aosm publisher artifact-manifest update-state \
    --resource-group <myResourceGroupName> \
    --publisher-name <myPublisherName> \
    --artifact-store-name <myArtifactStoreName> \
    --name <myArtifactManifestName> \
    --state Uploaded
```

## NFDV and NSDV states

- **Preview** is the default state.
- **Deprecated** is a terminal state but can be reversed.

### Update the NFDV state

To change the state of an NFDV resource, use the following Azure CLI command:

```azurecli
  az aosm publisher network-function-definition version update-state \
    --resource-group <myResourceGroup> \
    --publisher-name <myPublisherName> \
    --group-name <myNetworkFunctionDefinitionGroupName> \
    --version-name <myNetworkFunctionDefinitionVersionName> \
    --version-state Active | Deprecated
```

### Update the NSDV state

To change the state of an NSDV resource, use the following Azure CLI command:

```azurecli
  az aosm publisher network-service-design version update-state \
    --resource-group <myResourceGroup> \
    --publisher-name <myPublisherName> \
    --group-name <myNetworkServiceDesignGroupName> \
    --version-name <myNetworkServiceDesignVersionName> \
    --version-state Active | Deprecated
```

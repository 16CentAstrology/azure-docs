---
title: "Azure Operator Nexus - Example of clusterManager.jsonc template file"
description: Example of clusterManager.jsonc template file to use with ARM template in creating a cluster manager.
author: lb4368
ms.author: lborgmeyer
ms.service: azure-operator-nexus
ms.topic: how-to
ms.date: 07/21/2025
ms.custom: template-how-to, devx-track-arm-template
---

# Example of clusterManager.jsonc template file.

```clusterManager.jsonc
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "environment": {
      "type": "string",
      "metadata": {
        "description": "Name of the environment"
      }
    },
    "name": {
      "type": "string",
      "metadata": {
        "description": "Specify the name for the resource."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "Specify the location for the resources."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": ""
    },
    "availabilityZones": {
      "type": "array",
      "defaultValue": ["1","2","3"]
    },
    "fabricControllerId": {
      "type": "string"
    },
    "resourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Specify the resource group for the resources."
      },
      "defaultValue": ""
    },
    "managedResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Specify a managed resource group for the resource."
      }
    },
    "clusterManagerTags": {
      "type": "object",
      "metadata": {
        "description": "Additional tags to pass to the cluster manager on creation"
      }
    },
    "assignedIdentities": {
      "type": "object",
      "metadata": {
        "description": "The assigned identities for the Cluster Manager"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "[concat(parameters('environment'), '-lab-cm-deployment')]",
          "resourceGroup": "[parameters('resourceGroupName')]",
      "tags": {},
      "properties": {
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        },
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "environment": {
              "type": "string"
            },
            "name": {
              "type": "string"
            },
            "location": {
              "type": "string"
            },
            "vmSize": {
              "type": "string"
            },
            "availabilityZones": {
              "type": "array"
            },
            "fabricControllerId": {
              "type": "string"
            },
            "resourceGroupName": {
              "type": "string"
            },
            "managedResourceGroupConfiguration": {
              "type": "object"
            },
            "clusterManagerTags": {
              "type": "object"
            },
            "assignedIdentities": {
              "type": "object"
            }
          },
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.NetworkCloud/clusterManagers",
              "apiVersion": "2025-02-01",
              "name": "[parameters('name')]",
              "location": "[parameters('location')]",
              "tags": "[parameters('clusterManagerTags')]",
              "identity": "[parameters('assignedIdentities')]",
              "properties": {
                "fabricControllerId": "[parameters('fabricControllerId')]",
                "vmSize": "[if(equals(parameters('vmSize'), ''), json('null'), parameters('vmSize'))]",
                "availabilityZones": "[parameters('availabilityZones')]",
                "managedResourceGroupConfiguration": "[parameters('managedResourceGroupConfiguration')]"
              }
            }
          ],
          "outputs": {}
        },
        "parameters": {
          "environment": {
            "value": "[parameters('environment')]"
          },
          "fabricControllerId": {
            "value": "[parameters('fabricControllerId')]"
          },
          "location": {
            "value": "[parameters('location')]"
          },
          "clusterManagerTags": {
            "value": "[parameters('clusterManagerTags')]"
          },
          "vmSize": {
            "value": "[parameters('vmSize')]"
          },
          "availabilityZones": {
            "value": "[parameters('availabilityZones')]"
          },
          "name": {
            "value": "[parameters('name')]"
          },
          "resourceGroupName": {
            "value": "[parameters('resourceGroupName')]"
          },
          "assignedIdentities": {
            "value": "[parameters('assignedIdentities')]"
          },
          "managedResourceGroupConfiguration": {
            "value": {
              "location": "[parameters('location')]",
              "name": "[parameters('managedResourceGroupName')]"
            }
          }
        }
      }
    }
  ]
}
```

---
title: Configure dataflow endpoints for Azure Data Explorer
description: Learn how to configure dataflow endpoints for Azure Data Explorer in Azure IoT Operations.
author: PatAltimore
ms.author: patricka
ms.service: azure-iot-operations
ms.subservice: azure-data-flows
ms.topic: how-to
ms.date: 10/16/2024
ai-usage: ai-assisted

#CustomerIntent: As an operator, I want to understand how to configure dataflow endpoints for Azure Data Explorer in Azure IoT Operations so that I can send data to Azure Data Explorer.
---

# Configure dataflow endpoints for Azure Data Explorer

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

To send data to Azure Data Explorer in Azure IoT Operations Preview, you can configure a dataflow endpoint. This configuration allows you to specify the destination endpoint, authentication method, table, and other settings.

## Prerequisites

- An instance of [Azure IoT Operations Preview](../deploy-iot-ops/howto-deploy-iot-operations.md)
- A [configured dataflow profile](howto-configure-dataflow-profile.md)
- An **Azure Data Explorer cluster**. Follow the **Full cluster** steps in the [Quickstart: Create an Azure Data Explorer cluster and database](/azure/data-explorer/create-cluster-and-database). The *free cluster* option doesn't work for this scenario.


## Create an Azure Data Explorer database

1. In the Azure portal, create a database in your Azure Data Explorer *full* cluster.

1. Create a table in your database for the data. You can use the Azure portal and create columns manually, or you can use [KQL](/azure/data-explorer/kusto/management/create-table-command) in the query tab. For example, to create a table for sample thermostat data, run the following command:

    ```kql
    .create table thermostat (
        externalAssetId: string,
        assetName: string,
        CurrentTemperature: real,
        Pressure: real,
        MqttTopic: string,
        Timestamp: datetime
    )
    ```

1. Enable streaming ingestion on your table and database. In the query tab, run the following command, substituting `<DATABASE_NAME>` with your database name:

    ```kql
    .alter database ['<DATABASE_NAME>'] policy streamingingestion enable
    ```

   Alternatively, enable streaming ingestion on the entire cluster. See [Enable streaming ingestion on an existing cluster](/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-an-existing-cluster).

1. In Azure portal, go to the Arc-connected Kubernetes cluster and select **Settings** > **Extensions**. In the extension list, find the name of your Azure IoT Operations extension. Copy the name of the extension.

1. In your Azure Data Explorer database (not cluster), under **Overview** select **Permissions** > **Add** > **Ingestor**. Search for the Azure IoT Operations extension name then add it.


## Create an Azure Data Explorer dataflow endpoint

Create the dataflow endpoint resource with your cluster and database information. We suggest using the managed identity of the Azure Arc-enabled Kubernetes cluster. This approach is secure and eliminates the need for secret management. Replace the placeholder values like `<ENDPOINT_NAME>` with your own.

# [Bicep](#tab/bicep)

Create a Bicep `.bicep` file with the following content.

```bicep
param aioInstanceName string = '<AIO_INSTANCE_NAME>'
param customLocationName string = '<CUSTOM_LOCATION_NAME>'
param endpointName string = '<ENDPOINT_NAME>'
param hostName string = 'https://<CLUSTER>.<region>.kusto.windows.net'
param databaseName string = '<DATABASE_NAME>'

resource aioInstance 'Microsoft.IoTOperations/instances@2024-08-15-preview' existing = {
  name: aioInstanceName
}
resource customLocation 'Microsoft.ExtendedLocation/customLocations@2021-08-31-preview' existing = {
  name: customLocationName
}
resource adxEndpoint 'Microsoft.IoTOperations/instances/dataflowEndpoints@2024-08-15-preview' = {
  parent: aioInstance
  name: endpointName
  extendedLocation: {
    name: customLocationName
    type: 'CustomLocation'
  }
  properties: {
    endpointType: 'DataExplorer'
    dataExplorerSettings: {
      host: hostName
      database: databaseName
      authentication: {
        method: 'SystemAssignedManagedIdentity'
        systemAssignedManagedIdentitySettings: {}
      }
    }
  }
}
```

Then, deploy via Azure CLI.

```azurecli
az stack group create --name <DEPLOYMENT_NAME> --resource-group <RESOURCE_GROUP> --template-file <FILE>.bicep
```

# [Kubernetes](#tab/kubernetes)

Create a Kubernetes manifest `.yaml` file with the following content.

```yaml
apiVersion: connectivity.iotoperations.azure.com/v1beta1
kind: DataflowEndpoint
metadata:
  name: <ENDPOINT_NAME>
  namespace: azure-iot-operations
spec:
  endpointType: DataExplorer
  dataExplorerSettings:
    host: 'https://<CLUSTER>.<region>.kusto.windows.net'
    database: <DATABASE_NAME>
    authentication:
      method: SystemAssignedManagedIdentity
      systemAssignedManagedIdentitySettings: {}
```

Then apply the manifest file to the Kubernetes cluster.

```bash
kubectl apply -f <FILE>.yaml
```

---

### Available authentication methods

The following authentication methods are available for Azure Data Explorer endpoints. For more information about enabling secure settings by configuring an Azure Key Vault and enabling workload identities, see [Enable secure settings in Azure IoT Operations Preview deployment](../deploy-iot-ops/howto-enable-secure-settings.md).

#### System-assigned managed identity

Using the system-assigned managed identity is the recommended authentication method for Azure IoT Operations. Azure IoT Operations creates the managed identity automatically and assigns it to the Azure Arc-enabled Kubernetes cluster. It eliminates the need for secret management and allows for seamless authentication with Azure Data Explorer.

Before you create the dataflow endpoint, assign a role to the managed identity that grants permission to write to the Azure Data Explorer database. For more information on adding permissions, see [Manage Azure Data Explorer cluster permissions](/azure/data-explorer/manage-cluster-permissions).

In the *DataflowEndpoint* resource, specify the managed identity authentication method. In most cases, you don't need to specify other settings. This configuration creates a managed identity with the default audience `https://api.kusto.windows.net`.

# [Bicep](#tab/bicep)

```bicep
dataExplorerSettings: {
  authentication: {
    method: 'SystemAssignedManagedIdentity'
    systemAssignedManagedIdentitySettings: {}
  }
}
```

# [Kubernetes](#tab/kubernetes)

```yaml
dataExplorerSettings:
  authentication:
    method: SystemAssignedManagedIdentity
    systemAssignedManagedIdentitySettings: {}
```

---

If you need to override the system-assigned managed identity audience, you can specify the `audience` setting.

# [Bicep](#tab/bicep)

```bicep
dataExplorerSettings: {
  authentication: {
    method: 'SystemAssignedManagedIdentity'
    systemAssignedManagedIdentitySettings: {
      audience: 'https://<AUDIENCE_URL>'    
    }
  }
}
```

# [Kubernetes](#tab/kubernetes)

```yaml
dataExplorerSettings:
  authentication:
    method: SystemAssignedManagedIdentity
    systemAssignedManagedIdentitySettings:
      audience: https://<AUDIENCE_URL>
```

---

#### User-assigned managed identity

To use a user-assigned managed identity, specify the `UserAssignedManagedIdentity` authentication method and provide the `clientId` and `tenantId` of the managed identity.

# [Bicep](#tab/bicep)

```bicep
dataExplorerSettings: {
  authentication: {
    method: 'UserAssignedManagedIdentity'
    userAssignedManagedIdentitySettings: {
      clientId: '<ID>'
      tenantId: '<ID>'
    }
  }
}
```

# [Kubernetes](#tab/kubernetes)

```yaml
dataExplorerSettings:
  authentication:
    method: UserAssignedManagedIdentity
    userAssignedManagedIdentitySettings:
      clientId: <ID>
      tenantId: <ID>
```

---

## Advanced settings

You can set advanced settings for the Azure Data Explorer endpoint, such as the batching latency and message count. 

Use the `batching` settings to configure the maximum number of messages and the maximum latency before the messages are sent to the destination. This setting is useful when you want to optimize for network bandwidth and reduce the number of requests to the destination.

| Field | Description | Required |
| ----- | ----------- | -------- |
| `latencySeconds` | The maximum number of seconds to wait before sending the messages to the destination. The default value is 60 seconds. | No |
| `maxMessages` | The maximum number of messages to send to the destination. The default value is 100000 messages. | No |

For example, to configure the maximum number of messages to 1000 and the maximum latency to 100 seconds, use the following settings:

# [Bicep](#tab/bicep)

```bicep
dataExplorerSettings: {
  batching: {
    latencySeconds: 100
    maxMessages: 1000
  }
}
```

# [Kubernetes](#tab/kubernetes)

```yaml
dataExplorerSettings:
  batching:
    latencySeconds: 100
    maxMessages: 1000
```

---

## Next steps

- [Create a dataflow](howto-create-dataflow.md)
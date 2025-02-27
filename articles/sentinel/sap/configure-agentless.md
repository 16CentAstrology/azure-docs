---
title: Customize a Microsoft Sentinel SAP agentless data connector
description: Learn how to customize settings for your SAP agentless data connector for Microsoft Sentinel using the SAP Integration Suite value mapping.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 02/27/2025

#Customer intent: As a security engineer, I want to customize settings for my SAP agentless connector for Microsoft Sentinel to meet my organization's needs.

---

# Customize your SAP agentless data connector for Microsoft Sentinel

If you have an SAP agentless data connector for Microsoft Sentinel, you can use the SAP Integration Suite to customize how the agentless data connector ingests data from your SAP system into Microsoft Sentinel.

This procedure is only relevant when you want to customize the SAP agentless data connector behavior. Skip the procedures in this article if you're satisfied with the default functionality.

## Prerequisites

- You must have an SAP agentless data connector for Microsoft Sentinel set up and running. For more information, see [Microsoft Sentinel solution for SAP applications: Deployment overview](deployment-overview.md?tabs=agentless#data-connector).
- You must have access to the [SAP Integration Suite](https://help.sap.com/docs/cloud-integration/sap-cloud-integration/sap-cloud-integration), with permissions to [edit value mappings](https://help.sap.com/docs/cloud-integration/sap-cloud-integration/working-with-mapping).
- An SAP integration package, either existing or new, to upload the default value mapping file.

## Download the configuration file and customize settings

1. Download the default **value_mapping.xml** file. This file provides settings that define default behavior and is a good starting point to start customizing. The **value_mapping.xml** file is stored in the [example-parameters.zip](https://raw.githubusercontent.com/Azure/Azure-Sentinel/sap-agentless/Solutions/SAP/Agentless/example-paramaters.zip) file in the Microsoft Sentinel GitHub repository. Save the file to a location accessible to your SAP Integration environment.

1. Use the regular SAP procedure to create value mappings to customize your data connector settings.

    1. Upload the **value_mapping.xml** file to the SAP Integration Suite.
    1. Use one of the following methods to customize your settings:

        - **To customize settings across all SAP systems**, add value mappings for the **global** bi-directional mapping agency.
        - **To customize settings for specific SAP systems**, add new bi-directional mapping agencies for each SAP system, and then add value mappings for each one.

        For more information, see:

        - [SAP documentation on creating value mapping](https://help.sap.com/docs/cloud-integration/sap-cloud-integration/creating-value-mapping)
        - [Reference of customizable parameters](#reference-of-customizable-parameters)

    Make sure to deploy the artifact when you're done customizing to activate the updated settings.

## Reference of customizable parameters

This section provides a reference of customizable parameters for the SAP agentless connector for Microsoft Sentinel.

### changedocs-object-classes

List of object classes that are ingested from Change Docs logs.

- **Allowed values:** Comma separated list of object classes
- **Default value:** BANK,CLEARING,IBAN,IDENTITY,KERBEROS,OA2_CLIENT,PCA_BLOCK,PCA_MASTER,PFCG,SECM,SU_USOBT_C,SECURITY_POLICY,STATUS,SU22_USOBT,SU22_USOBX,SUSR_PROF,SU_USOBX_C,USER_CUA

### collect-audit-logs

Determines whether Audit Log data is ingested or not.

- **Allowed values:**
  - **true**: Ingested
  - **false**: Not ingested
- **Default value:** **true**

### collect-changedocs-logs

Determines whether Change Docs logs are ingested or not.

- **Allowed values:**
  - **true**: Ingested
  - **false**: Not ingested
- **Default value:** **true**

### collect-user-master-data

Determines whether User Master data is ingested or not.

- **Allowed values:**
  - **true**: Ingested
  - **false**: Not ingested
- **Default value:** **true**

### force-audit-log-to-read-from-all-clients

Determines whether the Audit Log is read from all clients.

- **Allowed values:**
  - **true**: Read from all clients
  - **false**: Not read from all clients
- **Default value:** **false**

### ingestion-cycle-days

Time, in days, given to ingest the full User Master data, including all roles and users. This parameter doesn't affect the ingestion of changes to User Master data.

- **Allowed values:** Integer, between **1**-**14**
- **Default value:** **1**

### offset-in-seconds

Determines the offset, in seconds, for both the start and end times of a data collection window. Use this parameter to delay data collection by the configured number of seconds.

- **Allowed values:** Integer, between **1**-**600**
- **Default value:** **60**


## Related content

- [Install a Microsoft Sentinel solution for SAP applications](/azure/sentinel/sap/deploy-sap-security-content?pivots=connection-agentless)
- [Prepare your SAP environment](/azure/sentinel/sap/preparing-sap?pivots=connection-agentless)
- [Connect your SAP system](/azure/sentinel/sap/deploy-data-connector-agent-container?tabs=managed-identity&pivots=connection-agentless)

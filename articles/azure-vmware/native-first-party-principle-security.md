---
title: Enable first-party application service principal for Azure VMware Solution in an Azure Virtual Network
description: Learn about enabling first-party application service principal for Azure VMware Solution in an Azure Virtual Network.
ms.topic: how-to
ms.service: azure-vmware
ms.date: 4/2/2025
#cusom intent: As a cloud administrator, I want to enable first-party application service principal for Azure VMware Solution in an Azure Virtual Network so that I can manage the Azure VMware Solution experiences.
---

# Enable first-party application service principal for Azure VMware Solution in an Azure Virtual Network

In this article, you learn how to re-enable the Azure VMware Solution service principal. This service principal is required to be enabled to deploy the Azure VMware Solution in an Azure Virtual Network. If you are already familiar with how to enable service principal, re-enable the service principal for application ID “1a5e141d-70dd-4594-8442-9fc46fa48686” with name “Avs Fleet Rp”.

## Prerequisite
 
You must have the permissions to edit applications in your Microsoft Entra ID tenant, such as:  
- Cloud Application Administrator  
- Application Administrator  
- Global Administrator  

## Enable first-party application service principal for Azure VMware Solution in an Azure Virtual Network

There are two options to enable the service principal for Azure VMware Solution. You can use either the **Microsoft Entra ID** portal or Azure PowerShell. The following sections describe both options.

### Option 1: From the Portal  

1. Select **Microsoft Entra ID**.  

2. Search **Microsoft Entra ID** for the application ID `1a5e141d-70dd-4594-8442-9fc46fa48686`. Select **Avs Fleet Rp**.  

3. Enable the **Avs Fleet Rp** Enterprise application for user sign-in by toggling the **Enabled for users to sign-in** toggle to **Yes**.   

4. Ensure you select **Save**.  


### Option 2: From Azure PowerShell  

1. Run the following command:  
    ```powershell  
    Get-AzADServicePrincipal -ApplicationId 1a5e141d-70dd-4594-8442-9fc46fa48686  
    ```  

2. Copy the value from the `Id` column to use in the next command. The `Id` field is a full GUID (blurred in the screenshot for privacy).  

3. Run the following command to enable the service principal using the value you copied from the `Id` column:  
    ```powershell  
    Set-AzureADServicePrincipal -ObjectId 0a9fa53e-1930 -AccountEnabled $True  
    ```  

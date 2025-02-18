---
title: Develop for Azure Files with .NET
titleSuffix: Azure Storage
description: Learn how to develop .NET applications and services that use Azure Files to store data.
author: pauljewellmsft
ms.service: azure-file-storage
ms.topic: conceptual
ms.date: 02/14/2025
ms.author: pauljewell
ms.devlang: csharp
ms.custom: devx-track-csharp, devx-track-dotnet
---

# Develop for Azure Files with .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Learn how to develop .NET applications that use Azure Files to store data. Azure Files is a managed file share service in the cloud. It provides fully managed file shares that are accessible via the industry standard Server Message Block (SMB) and Network File System (NFS) protocols. Azure Files also provides a REST API for programmatic access to file shares.

In this article, you learn about the different approaches to developing with Azure Files in .NET, and how to choose the approach that best fits the needs of your app. You also learn how to create a basic console app that interacts with Azure Files resources.

## Applies to

| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## About .NET app development with Azure Files

Azure Files offers several ways for .NET developers to access data and manage resources in Azure Files. The following table lists the approaches, summarizes how they work, and provides guidance on when to use each approach:

| Approach | How it works | When to use |
| --- | --- | --- |
| Standard file I/O libraries | Uses OS-level API calls through Azure file shares mounted using SMB or NFS. When you mount a file share using SMB/NFS, you can use file I/O libraries for a programming language or framework, such as `System.IO` for .NET. | You have line-of-business apps with existing code that uses standard file I/O, and you don't want to rewrite code for the app to work with an Azure file share. |
| FileREST API | Directly calls HTTPS endpoints to interact with data stored in Azure Files. Provides programmatic control over file share resources. The Azure SDK provides the File Shares client library (`Azure.Storage.Files.Shares`) that builds on the FileREST API, allowing you interact with FileREST API operations through familiar .NET programming language paradigms. | You're building value-added cloud services and apps for customers and you want to use advanced features not available through `System.IO`. |
| Storage resource provider REST API | Uses Azure Resource Manager (ARM) to manage storage accounts and file shares. Calls REST API endpoints for various resource management operations. | Your app or service needs to perform resource management tasks, such as creating, deleting, or updating storage accounts or file shares. |

For general information about these approaches, see [Overview of application development with Azure Files](storage-files-developer-overview.md).

This article focuses on working with Azure Files resources using the following approaches:

- [Work with Azure Files using System.IO](#work-with-azure-files-using-systemio): Mount a file share using SMB or NFS and use the `System.IO` namespace to work with files and directories in the share.
- [Work with Azure Files using the File Shares client library for .NET](#work-with-azure-files-using-the-file-shares-client-library-for-net): Use the Azure Storage File Shares client library for .NET to work with files and directories in a file share. This client library builds on the FileREST API.

To learn about using the Storage resource provider REST API and management libraries, see [Libraries for resource management](storage-files-developer-overview.md#libraries-for-resource-management).

## Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)
- Azure storage account - [create a storage account](../common/storage-account-create.md)
- Latest [.NET SDK](https://dotnet.microsoft.com/download/dotnet) for your operating system (get the SDK and not the runtime)

## Set up your environment

This section walks you through steps to prepare a .NET console app to work with Azure Files.

### Create the project

If you don't already have a .NET app, create one using Visual Studio or the .NET CLI. In this article, we create a console app for simplicity.

### [Visual Studio 2022](#tab/visual-studio)

1. Start Visual Studio and select **Create a new project**. Or if you're in Visual Studio, navigate to **File** > **New** > **Project**.
1. In the dialog window, choose **Console App** for C# and  select **Next**.
1. Enter a name for the project, leave the defaults, and select **Next**.
1. For **Framework**, select the latest installed version of .NET. Leave the other defaults, and select **Create**.

### [.NET CLI](#tab/dotnet-cli)

1. In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app. This command creates a simple "Hello World" C# project with a single source file: *Program.cs*.

   ```dotnetcli
   dotnet new console -n FilesConsoleApp
   ```

1. Switch to the newly created *FilesConsoleApp* directory.

   ```console
   cd FilesConsoleApp
   ```

1. Open the project in a code editor:
    * To open in Visual Studio, locate and double-click the `FilesConsoleApp.csproj` file.
    * To open in Visual Studio Code, run the following command:

    ```bash
    code .
    ```

---

### Install the package

If you plan to interact with Azure Files using the `System.IO` namespace, you don't need to install any additional packages. The `System.IO` namespace is included with the .NET SDK. If you plan to use the File Shares client library for .NET, install the package using NuGet.

### [Visual Studio](#tab/visual-studio)

1. In **Solution Explorer**, right-click your project and choose **Manage NuGet Packages**.
1. In **NuGet Package Manager**, select **Browse**. Then search for and choose **Azure.Storage.Files.Shares**. Select **Install**.

   This step installs the package and its dependencies.

### [.NET CLI](#tab/dotnet-cli)

1. In a console window, run the following command to install the `Azure.Storage.Files.Shares` package.

   ```dotnetcli
   dotnet add package Azure.Storage.Files.Shares
   ```

---

### Add using directives

If you plan to use the `System.IO` namespace, add the following using directive to the top of your *Program.cs* file:

```csharp
using System.IO;
```

If you plan to use the File Shares client library for .NET, add the following using directive to the top of your *Program.cs* file:

```csharp
using Azure.Storage.Files.Shares;
```

## Work with Azure Files using System.IO

Standard file I/O libraries are the most common way to access and work with Azure Files resources. When you mount a file share using SMB or NFS, your operating system redirects API requests for the local file system. This approach allows you to use standard file I/O libraries, such as `System.IO`, to interact with files and directories in the share.

Consider using `System.IO` when your app requires:

- **App compatibility:** Ideal for line-of-business apps with existing code that already uses `System.IO`. You don't need to rewrite code for the app to work with an Azure file share.
- **Ease of use:** `System.IO` is well known by developers and easy to use. A key value proposition of Azure Files is that it exposes native file system APIs through SMB and NFS.

In this section, you learn how to use `System.IO` to work with Azure Files resources.

For more information and examples, see the following resources:

- [File and Stream I/O](/dotnet/standard/io/) overview
- [Common I/O tasks](/dotnet/standard/io/common-i-o-tasks)

### Mount a file share

To use `System.IO`, you must first mount a file share. See the following resources for guidance on how to mount a file share using SMB or NFS:

- [Mount an SMB file share on Windows](storage-how-to-use-files-windows.md)
- [Mount an SMB file share on Linux](storage-how-to-use-files-linux.md)
- [Mount an NFS file share on Linux](storage-files-how-to-mount-nfs-shares.md)

In this article, we use the following path to refer to a mounted SMB file share on Windows:

```csharp
string fileSharePath = @"Z:\file-share";
```

### Example: Connect to a file share and enumerate directories using System.IO

The following code example shows how to connect to a file share and list the directories in the share:

```csharp
using System.IO;

string fileSharePath = @"Z:\file-share";

EnumerateDirectories(@"Z:\file-share");

static void EnumerateDirectories(string path)
{
    try
    {
        List<string> dirs = new List<string>(Directory.EnumerateDirectories(path));

        foreach (var dir in dirs)
        {
            Console.WriteLine($"{dir.Substring(dir.LastIndexOf(Path.DirectorySeparatorChar) + 1)}");
        }
        Console.WriteLine($"{dirs.Count} directories found.");
    }
    catch (UnauthorizedAccessException ex)
    {
        Console.WriteLine(ex.Message);
    }
    catch (PathTooLongException ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

### Example: Write to a file in a file share using System.IO

The following code example shows how to write and append text with the `File` class:

```csharp
using System.IO;

string fileSharePath = @"Z:\file-share";

WriteToFile(fileSharePath, "test.txt");

static void WriteToFile(string fileSharePath, string fileName)
{
    string textToWrite = "First line" + Environment.NewLine;
    string filePath = Path.Combine(fileSharePath, fileName);
    
    File.WriteAllText(filePath, textToWrite);

    string[] textToAppend = { "Second line", "Third line" };
    File.AppendAllLines(filePath, textToAppend);
}
```

### Example: Lock a file in a file share using System.IO

The following code example shows how to lock a file in a file share:

```csharp
using System.IO;

string fileSharePath = @"Z:\file-share";

LockFile(Path.Combine(fileSharePath, "test.txt"));

static void LockFile(string filePath)
{
    try
    {
        using (FileStream fs = new FileStream(filePath, FileMode.Open, FileAccess.ReadWrite, FileShare.None))
        {
            Console.WriteLine("File locked.");

            // Do something with file, press Enter to close the stream and release the lock
            Console.ReadLine();

            fs.Close();
            Console.WriteLine("File closed.");
        }
    }
    catch (IOException ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

### Example: Enumerate file ACLs using System.IO

The following code example shows how to enumerate ACLs for a file:

```csharp
using System.IO;
using System.Security.AccessControl;

string fileSharePath = @"Z:\file-share";
string fileName = "test.txt";
string filePath = Path.Combine(fileSharePath, fileName);

EnumerateFileACLs(filePath);

static void EnumerateFileACLs(string filePath)
{
    FileInfo fileInfo = new FileInfo(filePath);

    // For directories, use DirectorySecurity instead of FileSecurity
    FileSecurity fSecurity = FileSystemAclExtensions.GetAccessControl(fileInfo);

    // List all access rules for the file
    foreach (FileSystemAccessRule rule in fSecurity.GetAccessRules(true, true, typeof(System.Security.Principal.NTAccount)))
    {
        Console.WriteLine($"Identity: {rule.IdentityReference.Value}");
        Console.WriteLine($"Access Control Type: {rule.AccessControlType}");
        Console.WriteLine($"File System Rights: {rule.FileSystemRights}");
        Console.WriteLine();
    }

}
```

## Work with Azure Files using the File Shares client library for .NET

The FileREST API provides programmatic access to Azure Files. It allows you to call HTTPS endpoints to perform operations on file shares, directories, and files. The FileREST API is designed for high scalability and advanced features that might not be available through native protocols. The Azure SDK provides client libraries, such as the File Shares client library for .NET, that build on the FileREST API.

Consider using the FileREST API and the File Share client library if your application requires:

- **Advanced features:** Access operations and features that aren't available through native protocols.
- **Custom cloud integrations:** Build custom value-added services, such as backup, antivirus, or data management, that interact directly with Azure Files.
- **Performance optimization:** Benefit from performance advantages in high-scale scenarios using data plane operations.

The FileREST API models Azure Files as a hierarchy of resources, and is recommended for operations that are performed at the *directory* or *file* level. You should prefer the Storage resource provider REST API for operations that are performed at the *file service* or *file share* level.

In this section, you learn how to use the File Shares client library to work with Azure Files resources.

For more information and examples, see the following resources:

- [Azure Storage File Shares client library for .NET](/dotnet/api/overview/azure/storage.files.shares-readme)
- [Azure Storage File Shares client library for .NET samples](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/storage/Azure.Storage.Files.Shares/samples)

### Authorize access and create a client

To connect an app to Azure Files, create a `ShareClient` object. This object is your starting point for working with Azure Files resources. The following code examples show how to create a `ShareClient` object using different authorization mechanisms.

## [Microsoft Entra ID (recommended)](#tab/azure-ad)

To authorize with Microsoft Entra ID, you need to use a security principal. The type of security principal you need depends on where your app runs. Use this table as a guide.

| Where the app runs | Security principal | Guidance |
| --- | --- | --- |
| Local machine (developing and testing) | Service principal | To learn how to register the app, set up a Microsoft Entra group, assign roles, and configure environment variables, see [Authorize access using developer service principals](/dotnet/azure/sdk/authentication-local-development-service-principal?toc=/azure/storage/blobs/toc.json&bc=/azure/storage/blobs/breadcrumb/toc.json) | 
| Local machine (developing and testing) | User identity | To learn how to set up a Microsoft Entra group, assign roles, and sign in to Azure, see [Authorize access using developer credentials](/dotnet/azure/sdk/authentication-local-development-dev-accounts?toc=/azure/storage/blobs/toc.json&bc=/azure/storage/blobs/breadcrumb/toc.json) |
| Hosted in Azure | Managed identity | To learn how to enable managed identity and assign roles, see [Authorize access from Azure-hosted apps using a managed identity](/dotnet/azure/sdk/authentication-azure-hosted-apps?toc=/azure/storage/blobs/toc.json&bc=/azure/storage/blobs/breadcrumb/toc.json) |
| Hosted outside of Azure (for example, on-premises apps) | Service principal | To learn how to register the app, assign roles, and configure environment variables, see [Authorize access from on-premises apps using an application service principal](/dotnet/azure/sdk/authentication-on-premises-apps?toc=/azure/storage/blobs/toc.json&bc=/azure/storage/blobs/breadcrumb/toc.json) |

To work with the code examples in this article, assign the Azure RBAC built-in role **Storage File Data Privileged Contributor** to the security principal. This role provides full read, write, modify ACLs, and delete access on all the data in the shares for all the configured storage accounts regardless of the file/directory level NTFS permissions that are set. For more information, see [Access Azure file shares using Microsoft Entra ID with Azure Files OAuth over REST](authorize-oauth-rest.md).

#### Authorize access using DefaultAzureCredential

An easy and secure way to authorize access and connect to Blob Storage is to obtain an OAuth token by creating a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) instance. You can then use that credential to create a `ShareClient` object.

The following example creates a `ShareClient` object authorized using `DefaultAzureCredential`, then creates a `ShareDirectoryClient` object to work with a directory in the share:

```csharp
string accountName = "<account-name>";
string shareName = "<share-name>";

ShareClientOptions options = new ShareClientOptions()
{
    AllowSourceTrailingDot = true,
    AllowTrailingDot = true,
    ShareTokenIntent = ShareTokenIntent.Backup,
};
ShareClient shareClient = new(
   new Uri($"https://{accountName}.file.core.windows.net/{shareName}"),
   new DefaultAzureCredential(),
   options);

ShareDirectoryClient directoryClient = shareClient.GetDirectoryClient("sample-directory");
```

If you know exactly which credential type you use to authenticate users, you can obtain an OAuth token by using other classes in the [Azure Identity client library for .NET](/dotnet/api/overview/azure/identity-readme). These classes derive from the [TokenCredential](/dotnet/api/azure.core.tokencredential) class.

## [Account key](#tab/account-key)

Create a [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) by using the storage account name and account key. Then use that object to initialize a `ShareClient`.

```csharp
string accountName = "<account-name>";
string accountKey = "<account-key>";
string shareName = "<share-name>";

StorageSharedKeyCredential sharedKeyCredential = 
    new StorageSharedKeyCredential(accountName, accountKey);

ShareClient shareClient = new ShareClient(
    new Uri($"https://{accountName}.file.core.windows.net/{shareName}"),
    sharedKeyCredential);
```

You can also create a `ShareClient` by using a connection string. 

```csharp
string connectionString = "<connection-string>";
string shareName = "<share-name>";

ShareClient shareClient = new ShareClient(connectionString, shareName);
```

For information about how to obtain account keys and best practice guidelines for properly managing and safeguarding your keys, see [Manage storage account access keys](../common/storage-account-keys-manage.md).

> [!IMPORTANT]
> The account access key should be used with caution. If your account access key is lost or accidentally placed in an insecure location, your service may become vulnerable. Anyone who has the access key is able to authorize requests against the storage account, and effectively has access to all the data. `DefaultAzureCredential` provides enhanced security features and benefits and is the recommended approach for managing authorization to Azure services.

---

To learn more about each of these authorization mechanisms, see [Choose how to authorize access to file data](authorize-data-operations-portal.md).

### Example: TODO:Add examples here

## Related content

For more information about Azure Files, see the following resources:

- [Get started with AzCopy](../common/storage-use-azcopy-v10.md?toc=/azure/storage/files/toc.json)
- [Troubleshoot Azure Files](/troubleshoot/azure/azure-storage/files-troubleshoot?toc=/azure/storage/files/toc.json)
- [Azure Storage APIs for .NET](/dotnet/api/overview/azure/storage)
- [File Service REST API](/rest/api/storageservices/File-Service-REST-API)

---
author: cephalin
ms.service: azure-app-service
ms.devlang: java
ms.topic: include
ms.date: 06/10/2025
ms.author: cephalin
---

[Azure App Service](/azure/app-service/) provides a highly scalable, self-patching web app hosting service. At the top of the page, choose how you want to deploy your Java app: **Java SE**, **Tomcat**, or **JBoss EAP**, and then follow the corresponding instructions.

In this quickstart, you use the [Maven Plugin for Azure App Service Web Apps](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) to deploy a Java web application to a Linux JBoss EAP server in App Service.

If Maven isn't your preferred development tool, check out our similar tutorials for Java developers:
+ [Gradle](../../configure-language-java-deploy-run.md?pivots=platform-linux#gradle)
+ [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)
+ [Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)
+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-webapp)

## Prerequisites

- [!INCLUDE [quickstarts-free-trial-note](~/reusable-content/ce-skilling/azure/includes/quickstarts-free-trial-note.md)]

- Run the Azure CLI and Maven commands in this tutorial by using Azure Cloud Shell, an interactive shell that you can use through your browser to work with Azure services.

  To use Cloud Shell, select **Open Cloud Shell** at upper right in a code block and sign in to Azure if necessary. Then select **Copy** in the code block, paste the code into Cloud Shell, and run it. Make sure you're using the **Bash** environment of Cloud Shell.

## Create a Java app

1. Clone the Pet Store demo application.

   ```azurecli-interactive
   git clone https://github.com/Azure-Samples/app-service-java-quickstart
   ```

1. Change directory to the completed `petstore-ee7` project and build it.

   ```azurecli-interactive
   cd app-service-java-quickstart
   git checkout 20230308
   cd petstore-ee7
   mvn clean install
   ```

   > [!TIP]
   > The `petstore-ee7` sample requires Java 11 or newer. The `booty-duke-app-service` sample project requires Java 17. If your installed version of Java is less than 17, run the build from within the *petstore-ee7* directory, rather than at the top level.

   If you see a message about being in detached HEAD state, you can ignore it. You won't make any Git commit in this quickstart, so detached HEAD state is appropriate.

## Configure the Maven plugin

The App Service deployment process uses your Azure credentials from Azure Cloud Shell automatically. The Maven plugin authenticates with OAuth or device sign-in. For more information, see [Authentication](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Run the following Maven command to configure the deployment by setting the App Service operating system, Java version, and Jbosseap version.

```azurecli-interactive
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.14.1:config
```

1. For **Create new run configuration**, type **Y** and then press **Enter**.
1. For **Define value for OS**, type **2** for Linux, and then press **Enter**.
1. For **Define value for javaVersion**, type **2** for Java 17, and then press **Enter**. If you select Java 21, you don't see **Jbosseap** as an option later.
1. For **Define value for webContainer**, type **4** for Jbosseap 7, and then press **Enter**.
1. For **Define value for pricingTier**, type **1** for P1v3, and then press **Enter**.
1. For **Confirm**, type **Y** and then press **Enter**.

The output should look similar to the following code:

```bash
Please confirm webapp properties
AppName : petstoreee7-1745409173307
ResourceGroup : petstoreee7-1745409173307-rg
Region : centralus
PricingTier : P1v3
OS : Linux
Java Version: Java 17
Web server stack: Jbosseap 4
Deploy to slot : false
Confirm (Y/N) [Y]: 
[INFO] Saving configuration to pom.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:36 min
[INFO] Finished at: 2025-04-23T11:54:22Z
[INFO] ------------------------------------------------------------------------
```

After you confirm your choices, the plugin adds the plugin element and required settings to your project's *pom.xml* file, which configures your web app to run in App Service.

The relevant portion of the *pom.xml* file should look similar to the following example.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>>azure-webapp-maven-plugin</artifactId>
            <version>x.xx.x</version>
            <configuration>
                <schemaVersion>v2</schemaVersion>
                <resourceGroup>your-resource-group-name</resourceGroup>
                <appName>your-app-name</appName>
            ...
            </configuration>
        </plugin>
    </plugins>
</build>
```

The values for `<appName>` and `<resourceGroup>`, `petstoreee7-1745409173307` and `petstoreee7-1745409173307-rg` in the demo code, are used later.

You can modify the configurations for App Service directly in your *pom.xml* file.

- For the complete list of configurations, see [Common Configurations](https://github.com/microsoft/azure-maven-plugins/wiki/Common-Configuration).
- For configurations specific to App Service, see [Azure Web App: Configuration Details](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details).

## Deploy the app

With all the configuration ready in your *pom.xml* file, you can deploy your Java app to Azure with the following single command.

```azurecli-interactive
# Disable testing, as it requires Wildfly to be installed locally.
mvn package azure-webapp:deploy -DskipTests
```

Once you select from a list of available subscriptions, Maven deploys to Azure App Service. When deployment completes, your application is ready. For this demo, the URL is `http://petstoreee7-1745409173307.azurewebsites.net`. When you open the URL with your local web browser, you should see the following app:

![Screenshot of Maven Hello World web app running in Azure App Service.](../../media/quickstart-java/jboss-sample-in-app-service.png)

Congratulations! You deployed a Java app to App Service.

## Clean up resources

You created the resources for this tutorial in an Azure resource group. If you no longer need them, you can delete the resource group and all its resources by running the following Azure CLI command in the Cloud Shell.

```azurecli-interactive
az group delete --name <your resource group name>  --yes
```

For example, to delete the demo resources, run `az group delete --name petstoreee7-1745409173307-rg --yes`. This command might take awhile to run.

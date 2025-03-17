---
title: 'Tutorial: Deploy your first container app'
description: Deploy a NVIDIA NIM to Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: azure-container-apps
ms.topic: tutorial
ms.date: 03/16/2025
ms.author: cachai
ms.devlang: azurecli
---

# Tutorial: Deploy an NVIDIA LLAMA3 NIM to Azure Container Apps

NVIDIA Inference Microservices (NIMs) are optimized, containerized AI inference microservices designed to simplify and accelerate the deployment of AI models. When you use Azure Container Apps with serverless GPUs, you can run these NIMs efficiently without having to manage the underlying infrastructure.​

In this tutorial, you learn to deploy a Llama3 NVIDIA NIM to Azure Container Apps using serverless GPUs.

This tutorial uses a premium instance of Azure Container Registry to improve cold start performance when working with serverless GPUs. If you don't want to use a premium Azure Container Registry, make sure to modify the `az acr create` command in this tutorial to set `--sku` to `basic`.

## Prerequisites

| Resource | Description |
|---|---|
| Azure account | An Azure account with an active subscription.<br><br>If you don't have one, you [can create one for free](https://azure.microsoft.com/free/). |
| Azure CLI | Install the [Azure CLI](/cli/azure/install-azure-cli). |
| NVIDIA NGC API key | You can get an API key from the [NVIDIA GPU Cloud (NGC) website](https://catalog.ngc.nvidia.com). |

[!INCLUDE [container-apps-create-cli-steps.md](../../includes/container-apps-create-cli-steps.md)]

1. Set up environment variables by naming the resource group and setting the location.

    ```bash
    RESOURCE_GROUP="my-resource-group"
    LOCATION="swedencentral"
    ```

    Next, generate a unique container registry name.

    ```bash
    SUFFIX=$(head /dev/urandom | tr -dc 'A-Za-z0-9' | head -c 6)
    ACR_NAME="mygpututorialacr${SUFFIX}"
    ```

    Finally, set variables to name the environment and identify the environment, workload profile type, container app name, and container.

    ```bash
    CONTAINERAPPS_ENVIRONMENT="my-environment-name"
    GPU_TYPE="Consumption-GPU-NC24-A100"
    CONTAINER_APP_NAME="llama3-nim"
    CONTAINER_AND_TAG="llama3-8b-instruct:1.0.0"
    ```

[!INCLUDE [container-apps-create-resource-group.md](../../includes/container-apps-create-resource-group.md)]

[!INCLUDE [container-apps-create-environment.md](../../includes/container-apps-create-environment.md)]

1. Create an Azure Container Registry (ACR)

    > [!NOTE]
    > This tutorial uses a premium Azure Container Registry to improve cold start performance when working with serverless GPUs. If you don't want to use a premium Azure Container Registry, modify the following command and set `--sku` to `basic`.

    ```azurecli
    az acr create \
      --resource-group $RESOURCE_GROUP \
      --name $ACR_NAME \
      --location $LOCATION \
      --sku premium
    ```

## Pull, tag, and push your image

Next, pull the image from NVIDIA GPU Cloud and push to Azure Container Registry.

> [!NOTE]
> NVIDIA NICs each has their own hardware requirements. Make sure the GPU type you select supports the [NIM](link) of your choice. The Llama3 NIM used in this tutorial can run on NVIDIA A100 GPUs.

1. Authenticate to the NVIDIA container registry.

    ```bash
    docker login nvcr.io
    ```

    After you run this command, the sign in process prompts you to enter a username. Enter **$oauthtoken** for your user name value.

    Then you're prompted for a password. Enter your NVIDIA NGC API key here. Once authenticated to the NVIDIA registry, you can authenticate to the Azure registry.

1. Authenticate to Azure Container Registry.

    ```bash
    az acr login --name $ACR_NAME
    ```

1. Pull the Llama3 NIM image.

    ```azurecli
    docker pull nvcr.io/nim/meta/$CONTAINER_AND_TAG
    ```

1. Tag the image.

    ```azurecli
    docker tag nvcr.io/nim/meta/$CONTAINER_AND_TAG $ACR_NAME.azurecr.io/$CONTAINER_AND_TAG
    ```

1. Push the image to Azure Container Registry.

    ```azurecli
    docker push $ACR_NAME.azurecr.io/$CONTAINER_AND_TAG
    ```

## Enable artifact streaming (recommended but optional)

Many of the NIM images are large, and your container app can take a long time to start if you don't enable artifact streaming. Use the following steps to enable artifact streaming.

> [!NOTE]
> The following commands can take a few minutes to complete.

1. Enable artifact streaming on your container registry.

    ```azurecli
    az acr artifact-streaming update \
        --name $ACR_NAME \
        --repository llama31_8b_ins \
        --enable-streaming True
    ```

1. Enable artifact streaming on the container image.

    ```azurecli
    az acr artifact-streaming create \
      --name $ACR_NAME \
      --image llama31_8b_ins:latest
    ```

## Create your container app

Next you create a container app with the NVIDIA GPU Cloud API key.

1. Create the container app.

    ```azurecli
    az containerapp env create \
      --name $CONTAINERAPPS_ENVIRONMENT \
      --resource-group $RESOURCE_GROUP \
      --location $LOCATION \
      --enable-workload-profiles
    ```

1. Add the GPU workload profile to your environment.

    ```azurecli
    az containerapp env workload-profile add \
        --resource-group $RESOURCE_GROUP \
        --name $CONTAINERAPPS_ENVIRONMENT \
        --workload-profile-type $GPU_TYPE \
        --workload-profile-name <WORKLOAD_PROFILE_NAME>
    ```

1. Create the container app.

    ```azurecli
    az containerapp create \
      --name $CONTAINER_APP_NAME \
      --resource-group $RESOURCE_GROUP \
      --environment $CONTAINERAPPS_ENVIRONMENT \
      --image $ACR_NAME.azurecr.io/$CONTAINER_AND_TAG \
      --cpu 24 \
      --memory 220 \
      --gpu "NVIDIAA100" \
      --secrets ngc-api-key=<PASTE_NGC_API_KEY_HERE> \
      --env-vars NGC_API_KEY=secretref:ngc-api-key \
      --registry-server $ACR_NAME.azurecr.io \
      --registry-username <ACR_USERNAME> \
      --registry-password <ACR_PASSWORD> \
      --query properties.configuration.ingress.fqdn
    ```

    This command returns the URL of your container app. Set this value aside in a text editor for use in a following command.

## Verify the application works

You can verify a successful deployment by sending a request `POST` request to your application.

Before you run this command, make  sure you replace the `<YOUR_CONTAINER_APP_URL>` URL with your container app URL returned from the previous command.

```bash
curl -X POST \
  'http://<YOUR_CONTAINER_APP_URL>/v1/completions' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "meta/llama3-8b-instruct",
    "prompt": "Once upon a time",
    "max_tokens": 64
  }'
```

## (Optional) Improving performance with volume mounts

For even faster cold start times, many of the NIMs provide a volume mount path to mount a cache directory. You can use this cache directory to store the model weights and other files that the NIM needs to run. To set up a volume mount for the Llama3 NIM, see this article.

## Clean up resources

If you're not going to continue to use this application, run the following command to delete the resource group along with all the resources created in this tutorial.

>[!CAUTION]
> The following command deletes the specified resource group and all resources contained within it. This command also deletes any resources outside the scope of this tutorial that exist in this resource group.

```azurecli
az group delete --name $RESOURCE_GROUP
```

> [!TIP]
> Having issues? Let us know on GitHub by opening an issue in the [Azure Container Apps repo](https://github.com/microsoft/azure-container-apps).

## Next steps

TODO

> [!div class="nextstepaction"]
> [Communication between microservices](communicate-between-microservices.md)

---
title: Skapa, Visa och hantera Azure Event Grid system ämnen med CLI
description: Den här artikeln visar hur du använder Azure CLI för att skapa, Visa och ta bort system ämnen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633230"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Skapa, Visa och hantera Event Grid system ämnen med hjälp av Azure CLI
Den här artikeln visar hur du skapar och hanterar system ämnen med hjälp av Azure CLI. En översikt över system ämnen finns i [system avsnitt](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Installera tillägg för Azure CLI
För Azure CLI behöver du Event Grid- [tillägget](/cli/azure/azure-cli-extensions-list).

I Cloud Shell:

- Om du har installerat tillägget tidigare uppdaterar du det: `az extension update -n eventgrid`
- Om du inte har installerat tillägget tidigare installerar du det:  `az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI](/cli/azure/install-azure-cli). Kontrol lera att du har den senaste versionen genom att kontrol lera med `az --version` .
2. Avinstallera tidigare versioner av tillägget: `az extension remove -n eventgrid`
3. Installera eventgrid-tillägget med `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Avsnittet Skapa ett system

- Om du vill skapa ett system ämne i en Azure-källa först och sedan skapa en händelse prenumeration för ämnet, se följande referens avsnitt:
    - [AZ eventgrid system-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Om du vill ha en lista över `topic-type` värden som du kan använda för att skapa ett system avsnitt kör du följande kommando. Dessa ämnes typs värden representerar de händelse källor som har stöd för att skapa system ämnen. Ignorera och ta bort `Microsoft.EventGrid.Topics` `Microsoft.EventGrid.Domains` den från listan. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [AZ eventgrid system-topic Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Om du vill skapa ett system ämne (implicit) när du skapar en händelse prenumeration för en Azure-källa använder du metoden [AZ eventgrid Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) . Här är ett exempel:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    En själv studie kurs med stegvisa instruktioner finns i [Prenumerera på lagrings konto](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Visa alla system ämnen
Om du vill visa alla system ämnen och information om ett valt system, använder du följande kommandon:

- [AZ eventgrid system-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid system-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Ta bort ett system ämne
Om du vill ta bort ett system avsnitt använder du följande kommando: 

- [AZ eventgrid system-topic Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Nästa steg
Mer information om system ämnen och ämnes typer som stöds av Azure Event Grid finns i avsnittet [system ämnen i Azure Event Grid](system-topics.md) . 
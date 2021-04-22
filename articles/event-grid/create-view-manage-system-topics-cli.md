---
title: Skapa, visa och hantera Azure Event Grid systemämnen med CLI
description: Den här artikeln visar hur du använder Azure CLI för att skapa, visa och ta bort systemämnen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874305"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Skapa, visa och hantera Event Grid systemämnen med Hjälp av Azure CLI
Den här artikeln visar hur du skapar och hanterar systemämnen med hjälp av Azure CLI. En översikt över systemämnen finns i [Systemämnen](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Installera tillägg för Azure CLI
För Azure CLI behöver du Event Grid [tillägget](/cli/azure/azure-cli-extensions-list).

I Cloud Shell:

- Om du har installerat tillägget tidigare uppdaterar du det: `az extension update -n eventgrid`
- Om du inte har installerat tillägget tidigare installerar du det:  `az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI.](/cli/azure/install-azure-cli) Kontrollera att du har den senaste versionen genom att kontrollera med `az --version` .
2. Avinstallera tidigare versioner av tillägget: `az extension remove -n eventgrid`
3. Installera eventgrid-tillägget med `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Skapa ett systemämne

- Om du vill skapa ett systemämne på en Azure-källa först och sedan skapa en händelseprenumeration för det ämnet kan du läsa följande referensavsnitt:
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

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

        Kör följande kommando `topic-type` för en lista med värden som du kan använda för att skapa ett systemämne. Dessa värden för ämnestyp representerar de händelsekällor som stöder skapandet av systemämnen. Ignorera `Microsoft.EventGrid.Topics` och `Microsoft.EventGrid.Domains` från listan. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Om du vill skapa ett systemämne (implicit) när du skapar en händelseprenumeration för en Azure-källa använder du metoden [az eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) Här är ett exempel:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    En självstudiekurs med stegvisa instruktioner finns i [Prenumerera på lagringskonto.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Visa alla systemämnen
Om du vill visa alla systemämnen och information om ett valt systemämne använder du följande kommandon:

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Ta bort ett systemämne
Om du vill ta bort ett systemämne använder du följande kommando: 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Nästa steg
I avsnittet [Systemavsnitt i Azure Event Grid](system-topics.md) du mer information om systemämnen och ämnestyper som stöds av Azure Event Grid. 
---
title: 'Snabb start: skapa en Synapse-arbetsyta med Azure CLI'
description: Skapa en Azure dataSynapses-arbetsyta med Azure CLI genom att följa stegen i den här hand boken.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 8a56b325dd5e1180b1229465965167241fab76a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676464"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Snabb start: skapa en Azure datasynapses-arbetsyta med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows och köra det från kommandoraden.

I den här snabb starten lär du dig att skapa en Synapse-arbetsyta med hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Hämta och installera [JQ](https://stedolan.github.io/jq/download/), en lätt och flexibel kommando RADS-JSON-processor
- [Azure Data Lake Storage Gen2 lagrings konto](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure Synapse-arbetsytan måste kunna läsa och skriva till det valda ADLS Gen2 kontot. För alla lagrings konton som du länkar som primärt lagrings konto måste du dessutom ha aktiverat **hierarkiskt namn område**  när lagrings kontot skapas, enligt beskrivningen på sidan [skapa en lagrings kontot](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) . 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Skapa en Azure dataSynapses-arbetsyta med hjälp av Azure CLI

1. Definiera nödvändiga miljövariabler för att skapa resurser för Azure dataSynapses-arbetsytan.

    | Miljövariabelns namn | Beskrivning |
    |---|---|---|
    |StorageAccountName| Namn på ditt befintliga ADLS Gen2 lagrings konto.|
    |StorageAccountResourceGroup| Namnet på din befintliga ADLS Gen2 lagrings konto resurs grupp. |
    |FileShareName| Namnet på ditt befintliga lagrings fil system.|
    |SynapseResourceGroup| Välj ett nytt namn för din resurs grupp för Azure-Synapse. |
    |Region| Välj en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Välj ett unikt namn för din nya Azure Synapse-arbetsyta. |
    |SqlUser| Välj ett värde för ett nytt användar namn.|
    |SqlPassword| Välj ett säkert lösen ord.|
    |||

1. Skapa en resurs grupp som en behållare för din Azure Synapse-arbetsyta:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```

1. Skapa en Azure dataSynapses-arbetsyta:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

1. Hämta webb-och dev-URL för Azure dataSynapses-arbetsyta:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

1. Skapa en brand Väggs regel som ger åtkomst till Azure Synapse-arbetsytan från din dator:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

1. Öppna webb adressen för webb adressen för Azure Synapse-arbetsytan som lagras i miljövariabeln `WorkspaceWeb` för att få åtkomst till arbets ytan

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Azure Synapse- ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) arbetsyta](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Azure dataSynapses-arbetsytan.
> [!WARNING]
> Om du tar bort en Azure Synapse-arbetsyta tas analys motorerna och de data som lagras i databasen med SQL-pooler och metadata för arbets ytan bort. Det går inte längre att ansluta till SQL-eller Apache Spark-slutpunkter. Alla kod artefakter tas bort (frågor, antecknings böcker, jobb definitioner och pipeliner).
>
> Att ta bort arbets ytan påverkar **inte** data i den data Lake Store Gen2 som är länkad till arbets ytan.

Om du vill ta bort arbets ytan Azure Synapse slutför du följande kommando:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Sedan kan du [skapa SQL-pooler](quickstart-create-sql-pool-studio.md) eller [skapa Apache Spark pooler](quickstart-create-apache-spark-pool-studio.md) för att börja analysera och utforska dina data.
---
title: Skapa arbets ytor med Azure CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure CLI-tillägget för Machine Learning för att skapa en ny Azure Machine Learning-arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: b6b23e792aaef4d70e9ffc9be3667f0abef49e81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489556"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Skapa en arbets yta för Azure Machine Learning med Azure CLI


I den här artikeln får du lära dig hur du skapar en Azure Machine Learning-arbetsyta med hjälp av Azure CLI. Azure CLI innehåller kommandon för att hantera Azure-resurser. Machine Learning-tillägget till CLI innehåller kommandon för att arbeta med Azure Machine Learning resurser.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö** behöver du [Azure CLI](/cli/azure/install-azure-cli).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Cloud Shell autentiserar automatiskt dig med det konto som du loggar in i din Azure-prenumeration.

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Det enklaste är att interaktivt autentisera med hjälp av en webbläsare. För att autentisera interaktivt, öppna en kommando rad eller Terminal och Använd följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

För andra metoder för autentisering, se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-the-machine-learning-extension"></a>Installera Machine Learning-tillägget

Om du vill installera Machine Learning-tillägget använder du följande kommando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Azure Machine Learning-arbetsytan är beroende av följande Azure-tjänster eller-entiteter:

> [!IMPORTANT]
> Om du inte anger en befintlig Azure-tjänst skapas en automatiskt när arbets ytan skapas. Du måste alltid ange en resurs grupp. När du kopplar ditt eget lagrings konto ser du till att det uppfyller följande kriterier:
>
> * Lagrings kontot är _inte_ ett Premium-konto (Premium_LRS och Premium_GRS)
> * Både Azure blob-och Azure-filfunktioner är aktiverade
> * Hierarkiskt namn område (ADLS gen 2) är inaktiverat
>
> Dessa krav gäller endast för det _standard_ lagrings konto som används av arbets ytan.

| Tjänst | Parameter för att ange en befintlig instans |
| ---- | ---- |
| **Azure-resurs grupp** | `-g <resource-group-name>`
| **Azure Storage konto** | `--storage-account <service-id>` |
| **Azure Application insikter** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) stöder för närvarande inte Unicode-tecken i resurs grupps namn. Du kan åtgärda det här problemet genom att använda en resurs grupp som inte innehåller dessa tecken.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Arbets ytan Azure Machine Learning måste skapas i en resurs grupp. Du kan välja en befintlig resursgrupp eller skapa en ny. Använd följande kommando för att __skapa en ny resurs grupp__. Ersätt `<resource-group-name>` med det namn som ska användas för den här resurs gruppen. Ersätt `<location>` med den Azure-region som ska användas för den här resurs gruppen:

> [!TIP]
> Välj en region där Azure Machine Learning är tillgängligt. Mer information finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Svaret från det här kommandot liknar följande JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Mer information om hur du arbetar med resurs grupper finns i [AZ Group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Skapa nödvändiga resurser automatiskt

Om du vill skapa en ny arbets yta där __tjänsterna skapas automatiskt__, använder du följande kommando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Namnet på arbets ytan är Skift läges okänsligt.

Utdata från det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Virtuellt nätverk och privat slut punkt

> [!IMPORTANT]
> Användning av en Azure Machine Learning arbets yta med privat länk är inte tillgänglig i Azure Government regionerna.

Om du vill begränsa åtkomsten till din arbets yta till ett virtuellt nätverk kan du använda följande parametrar:

* `--pe-name`: Namnet på den privata slut punkt som skapas.
* `--pe-auto-approval`: Om de privata slut punkts anslutningarna till arbets ytan automatiskt ska godkännas.
* `--pe-resource-group`: Resurs gruppen som den privata slut punkten ska skapas i. Måste vara samma grupp som innehåller det virtuella nätverket.
* `--pe-vnet-name`: Det befintliga virtuella nätverket som den privata slut punkten ska skapas i.
* `--pe-subnet-name`: Namnet på det undernät som den privata slut punkten ska skapas i. Standardvärdet är `default`.

Mer information om hur du använder en privat slut punkt och ett virtuellt nätverk med din arbets yta finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Kundhanterad nyckel och arbets yta med hög arbets belastning

Som standard lagras metadata för arbets ytan i en Azure Cosmos DB-instans som Microsoft underhåller. Dessa data är krypterade med Microsoft-hanterade nycklar.

> [!NOTE]
> Azure Cosmos DB används __inte__ för att lagra information som modell prestanda, information som loggats av experiment eller information som loggats från modell distributioner. Mer information om hur du övervakar dessa objekt finns i avsnittet [övervakning och loggning](concept-azure-machine-learning-architecture.md) i artikeln arkitektur och begrepp.

I stället för att använda den Microsoft-hanterade nyckeln kan du använda ange din egen nyckel. Om du gör det skapas Azure Cosmos DB-instansen som lagrar metadata i din Azure-prenumeration. Använd `--cmk-keyvault` parametern för att ange Azure Key Vault som innehåller nyckeln och `--resource-cmk-uri` för att ange URL: en för nyckeln i valvet.

Innan du använder `--cmk-keyvault` `--resource-cmk-uri` parametrarna och måste du först utföra följande åtgärder:

1. Auktorisera __Machine Learning-appen__ (i identitets-och åtkomst hantering) med deltagar behörigheter för din prenumeration.
1. Följ stegen i [Konfigurera Kundhanterade nycklar](../cosmos-db/how-to-setup-cmk.md) för att:
    * Registrera Azure Cosmos DB-providern
    * Skapa och konfigurera en Azure Key Vault
    * Generera en nyckel

Du behöver inte skapa Azure Cosmos DB-instansen manuellt, en skapas automatiskt när du skapar arbets ytan. Den här Azure Cosmos DB-instansen skapas i en separat resurs grupp med hjälp av ett namn baserat på det här mönstret: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Om du vill begränsa de data som Microsoft samlar in på din arbets yta använder du `--hbi-workspace` parametern. 

> [!IMPORTANT]
> Du kan bara välja hög påverkan på verksamheten när du skapar en arbets yta. Du kan inte ändra den här inställningen när du har skapat arbets ytan.

Mer information om Kundhanterade nycklar och arbets ytan för arbets ytor med hög arbets belastning finns i [företags säkerhet för Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Använd befintliga resurser

Om du vill skapa en arbets yta som använder befintliga resurser måste du ange ID: t för resurserna. Använd följande kommandon för att hämta ID: t för tjänsterna:

> [!IMPORTANT]
> Du behöver inte ange alla befintliga resurser. Du kan ange en eller flera. Du kan till exempel ange ett befintligt lagrings konto så skapas de andra resurserna av arbets ytan.

+ **Azure Storage konto**: `az storage account show --name <storage-account-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID: t för ditt lagrings konto:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Om du vill använda ett befintligt Azure Storage-konto kan det inte vara ett Premium-konto (Premium_LRS och Premium_GRS). Det får inte heller ha ett hierarkiskt namn område (används med Azure Data Lake Storage Gen2). Varken Premium Storage eller hierarkiskt namn område stöds med arbets ytans _standard_ lagrings konto. Du kan använda Premium Storage eller hierarkiskt namnrymd med lagrings konton som _inte är standard_ .

+ **Azure Application insikter**:

    1. Installera Application Insights-tillägget:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Hämta ID för din Application Insight Service:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Svaret från det här kommandot liknar följande text och är ID: t för Application Insights-tjänsten:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Svaret från det här kommandot liknar följande text och är ID: t för nyckel valvet:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID: t för behållar registret:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Kontot för behållar registret måste ha [Administratörs kontot](../container-registry/container-registry-authentication.md#admin-account) aktiverat innan det kan användas med en Azure Machine Learning-arbetsyta.

När du har ID: n för de resurser som du vill använda med arbets ytan använder du `az workspace create -w <workspace-name> -g <resource-group-name>` kommandot Base och lägger till parametrarna och ID: na för de befintliga resurserna. Följande kommando skapar till exempel en arbets yta som använder ett befintligt behållar register:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Lista arbets ytor

Om du vill visa en lista över alla arbets ytor för din Azure-prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace list
```

Utdata från det här kommandot liknar följande JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Mer information finns i dokumentationen om [AZ ml-arbetsytans lista](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Hämta information om arbets ytan

Om du vill hämta information om en arbets yta använder du följande kommando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Mer information finns i dokumentationen om [AZ ml-arbetsytan](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Uppdatera en arbets yta

Om du vill uppdatera en arbets yta använder du följande kommando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Mer information finns i dokumentationen om [uppdatering av AZ ml-arbetsytan](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Dela en arbets yta med en annan användare

Om du vill dela en arbets yta med en annan användare i din prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Mer information om rollbaserad åtkomst kontroll i Azure (Azure RBAC) med Azure Machine Learning finns i [Hantera användare och roller](how-to-assign-roles.md).

Mer information finns i [AZ ml-arbetsytan resurs](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share) dokumentation.

## <a name="sync-keys-for-dependent-resources"></a>Synkronisera nycklar för beroende resurser

Om du ändrar åtkomst nycklar för en av resurserna som används av din arbets yta tar det ungefär en timme innan arbets ytan synkroniseras med den nya nyckeln. Om du vill tvinga arbets ytan att synkronisera de nya nycklarna direkt använder du följande kommando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Mer information om hur du ändrar nycklar finns i [Återskapa lagrings åtkomst nycklar](how-to-change-storage-access-key.md).

Mer information finns i dokumentationen för [Sync-Keys för AZ ml-arbetsytan](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd följande kommando om du vill ta bort en arbets yta efter att den inte längre behövs:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Om du tar bort en arbets yta tas inte program insikter, lagrings konto, nyckel valv eller behållar registret som används av arbets ytan bort.

Du kan också ta bort resurs gruppen, som tar bort arbets ytan och alla andra Azure-resurser i resurs gruppen. Om du vill ta bort resurs gruppen använder du följande kommando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Mer information finns i [AZ ml-arbetsytan ta bort](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete) dokumentation.

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-tillägget för Machine Learning finns i [AZ ml](/cli/azure/ext/azure-cli-ml/ml) -dokumentationen.
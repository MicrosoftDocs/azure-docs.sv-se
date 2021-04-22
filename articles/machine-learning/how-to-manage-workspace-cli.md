---
title: Skapa arbetsytor med Azure CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure CLI-tillägget för maskininlärning för att skapa en Azure Machine Learning arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876411"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Skapa en arbetsyta för Azure Machine Learning med Azure CLI


I den här artikeln får du lära dig hur du skapar Azure Machine Learning en arbetsyta med hjälp av Azure CLI. Azure CLI innehåller kommandon för att hantera Azure-resurser. Maskininlärningstillägget till CLI innehåller kommandon för att arbeta med Azure Machine Learning resurser.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har någon kan du prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda CLI-kommandona i det här dokumentet **från din lokala** miljö behöver du Azure [CLI](/cli/azure/install-azure-cli).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), nås CLI via webbläsaren och finns i molnet.

## <a name="limitations"></a>Begränsningar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Cloud Shell autentiserar dig automatiskt med det konto som du loggar in på din Azure-prenumeration.

Det finns flera sätt att autentisera till din Azure-prenumeration från CLI. Det enklaste är att autentisera interaktivt med hjälp av en webbläsare. Om du vill autentisera interaktivt öppnar du en kommandorad eller terminal och använder följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommandoraden. Anvisningarna handlar om att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Andra metoder för autentisering finns i [Logga in med Azure CLI.](/cli/azure/authenticate-azure-cli)

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Arbetsytan Azure Machine Learning förlitar sig på följande Azure-tjänster eller entiteter:

> [!IMPORTANT]
> Om du inte anger en befintlig Azure-tjänst skapas en automatiskt när arbetsytan skapas. Du måste alltid ange en resursgrupp. När du bifogar ett eget lagringskonto kontrollerar du att det uppfyller följande kriterier:
>
> * Lagringskontot är _inte ett_ Premium-konto (Premium_LRS och Premium_GRS)
> * Både Azure Blob- och Azure File-funktioner är aktiverade
> * Hierarkisk namnrymd (ADLS Gen 2) är inaktiverat
>
> Dessa krav gäller endast för det _standardlagringskonto_ som används av arbetsytan.

| Tjänst | Parameter för att ange en befintlig instans |
| ---- | ---- |
| **Azure-resursgrupp** | `-g <resource-group-name>`
| **Azure Storage konto** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) stöder för närvarande inte Unicode-tecken i resursgruppnamn. Du kan åtgärda det här problemet genom att använda en resursgrupp som inte innehåller dessa tecken.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Arbetsytan Azure Machine Learning måste skapas i en resursgrupp. Du kan välja en befintlig resursgrupp eller skapa en ny. Använd __följande kommando för att skapa__ en ny resursgrupp. Ersätt `<resource-group-name>` med namnet som ska användas för den här resursgruppen. Ersätt `<location>` med Azure-regionen som ska användas för den här resursgruppen:

> [!TIP]
> Du bör välja en region där Azure Machine Learning är tillgänglig. Mer information finns i [Produktinformation per region.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

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

Mer information om hur du arbetar med resursgrupper finns i [az group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Skapa automatiskt nödvändiga resurser

Om du vill skapa en ny __arbetsyta där tjänsterna skapas automatiskt__ använder du följande kommando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Arbetsytans namn är inte case-okänsligt.

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

### <a name="virtual-network-and-private-endpoint"></a>Virtuellt nätverk och privat slutpunkt

> [!IMPORTANT]
> Användning av Azure Machine Learning arbetsyta med privat länk är inte tillgängligt i Azure Government regioner.

Om du vill begränsa åtkomsten till din arbetsyta till ett virtuellt nätverk kan du använda följande parametrar:

* `--pe-name`: Namnet på den privata slutpunkt som skapas.
* `--pe-auto-approval`: Om privata slutpunktsanslutningar till arbetsytan ska godkännas automatiskt.
* `--pe-resource-group`: Resursgruppen som den privata slutpunkten ska skapas i. Måste vara samma grupp som innehåller det virtuella nätverket.
* `--pe-vnet-name`: Det befintliga virtuella nätverk som den privata slutpunkten ska skapas i.
* `--pe-subnet-name`: Namnet på undernätet som den privata slutpunkten ska skapas i. Standardvärdet är `default`.

Mer information om hur du använder en privat slutpunkt och ett virtuellt nätverk med din arbetsyta finns i Översikt [över isolering och sekretess för virtuella nätverk.](how-to-network-security-overview.md)

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Kundhanteringsnyckel och arbetsyta med stor inverkan på verksamheten

Som standard lagras metadata för arbetsytan i en Azure Cosmos DB som Microsoft underhåller. Dessa data krypteras med Microsoft-hanterade nycklar.

> [!NOTE]
> Azure Cosmos DB __används inte__ för att lagra information som modellprestanda, information som loggas av experiment eller information som loggas från dina modelldistributioner. Mer information om övervakning av dessa objekt finns i [avsnittet Övervakning och loggning](concept-azure-machine-learning-architecture.md) i artikeln om arkitektur och begrepp.

I stället för att använda den Microsoft-hanterade nyckeln kan du använda ange en egen nyckel. När du gör det skapas Azure Cosmos DB-instansen som lagrar metadata i din Azure-prenumeration. Använd `--cmk-keyvault` parametern för att Azure Key Vault som innehåller nyckeln och för `--resource-cmk-uri` att ange url:en för nyckeln i valvet.

Innan du använder `--cmk-keyvault` `--resource-cmk-uri` parametrarna och måste du först utföra följande åtgärder:

1. __Auktorisera Machine Learning -appen__ (i Identitets- och åtkomsthantering) med deltagarbehörighet för din prenumeration.
1. Följ stegen i Konfigurera [kund hanterade nycklar för](../cosmos-db/how-to-setup-cmk.md) att:
    * Registrera Azure Cosmos DB providern
    * Skapa och konfigurera en Azure Key Vault
    * Generera en nyckel

Du behöver inte skapa Azure Cosmos DB instansen manuellt. En skapas åt dig när arbetsytan skapas. Den Azure Cosmos DB instansen skapas i en separat resursgrupp med ett namn baserat på det här mönstret: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Om du vill begränsa de data som Microsoft samlar in på din arbetsyta använder du `--hbi-workspace` parametern . 

> [!IMPORTANT]
> Du kan bara välja stor inverkan på verksamheten när du skapar en arbetsyta. Du kan inte ändra den här inställningen när arbetsytan har skapats.

Mer information om kund hanterade nycklar och arbetsyta med stor inverkan på verksamheten finns i [Enterprise Security for Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Använda befintliga resurser

Om du vill skapa en arbetsyta som använder befintliga resurser måste du ange ID:t för resurserna. Använd följande kommandon för att hämta ID:t för tjänsterna:

> [!IMPORTANT]
> Du behöver inte ange alla befintliga resurser. Du kan ange en eller flera. Du kan till exempel ange ett befintligt lagringskonto så skapar arbetsytan de andra resurserna.

+ **Azure Storage konto**: `az storage account show --name <storage-account-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID:t för ditt lagringskonto:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Om du vill använda ett befintligt Azure Storage konto kan det inte vara ett Premium-konto (Premium_LRS och Premium_GRS). Den kan inte heller ha ett hierarkiskt namnområde (används med Azure Data Lake Storage Gen2). Varken Premium Storage eller hierarkisk namnrymd stöds med _arbetsytans_ standardlagringskonto. Du kan använda premiumlagring eller hierarkisk namnrymd med _lagringskonton som inte_ är standard.

+ **Azure Application Insights:**

    1. Installera Application Insights-tillägget:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Hämta ID:t för din application insight-tjänst:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Svaret från det här kommandot liknar följande text och är ID:t för application insights-tjänsten:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Svaret från det här kommandot liknar följande text och är ID:t för nyckelvalvet:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Svaret från det här kommandot liknar följande text och är ID:t för containerregistret:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Containerregistret måste ha [administratörskontot aktiverat innan](../container-registry/container-registry-authentication.md#admin-account) det kan användas med en Azure Machine Learning arbetsyta.

När du har ID:n för de resurser som du vill använda med arbetsytan använder du baskommandot och lägger till parametrarna och ID:na för de `az workspace create -w <workspace-name> -g <resource-group-name>` befintliga resurserna. Följande kommando skapar till exempel en arbetsyta som använder ett befintligt containerregister:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Kommandots utdata liknar följande JSON:

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

## <a name="list-workspaces"></a>Visa en lista över arbetsytor

Om du vill visa en lista över alla arbetsytor för din Azure-prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace list
```

Kommandots utdata liknar följande JSON:

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

Mer information finns i dokumentationen [för az ml workspace list.](/cli/azure/ml/workspace#az_ml_workspace_list)

## <a name="get-workspace-information"></a>Hämta arbetsyteinformation

Om du vill ha information om en arbetsyta använder du följande kommando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Kommandots utdata liknar följande JSON:

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

Mer information finns i dokumentationen [az ml workspace show.](/cli/azure/ml/workspace#az_ml_workspace_show)

## <a name="update-a-workspace"></a>Uppdatera en arbetsyta

Om du vill uppdatera en arbetsyta använder du följande kommando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Kommandots utdata liknar följande JSON:

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

Mer information finns i dokumentationen [för az ml workspace update.](/cli/azure/ml/workspace#az_ml_workspace_update)

## <a name="share-a-workspace-with-another-user"></a>Dela en arbetsyta med en annan användare

Om du vill dela en arbetsyta med en annan användare i din prenumeration använder du följande kommando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Mer information om rollbaserad åtkomstkontroll i Azure (Azure RBAC) med Azure Machine Learning finns i [Hantera användare och roller.](how-to-assign-roles.md)

Mer information finns i dokumentationen [om az ml workspace share.](/cli/azure/ml/workspace#az_ml_workspace_share)

## <a name="sync-keys-for-dependent-resources"></a>Synkronisera nycklar för beroende resurser

Om du ändrar åtkomstnycklarna för en av de resurser som används av arbetsytan tar det ungefär en timme för arbetsytan att synkroniseras med den nya nyckeln. Om du vill tvinga arbetsytan att synkronisera de nya nycklarna direkt använder du följande kommando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Mer information om hur du ändrar nycklar finns i [Återskapa åtkomstnycklar för lagring.](how-to-change-storage-access-key.md)

Mer information finns i dokumentationen [az ml workspace sync-keys.](/cli/azure/ml/workspace#az_ml_workspace_sync-keys)

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Om du vill ta bort en arbetsyta när den inte längre behövs använder du följande kommando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> När du tar bort en arbetsyta tas inte programinsikter, lagringskonto, nyckelvalv eller containerregister bort som används av arbetsytan.

Du kan också ta bort resursgruppen, vilket tar bort arbetsytan och alla andra Azure-resurser i resursgruppen. Om du vill ta bort resursgruppen använder du följande kommando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Mer information finns i dokumentationen [för az ml workspace delete.](/cli/azure/ml/workspace#az_ml_workspace_delete)

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resursproviderfel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbetsytan

> [!WARNING]
> Det finns inte Azure Machine Learning att flytta din arbetsyta till en annan prenumeration eller att flytta den egna prenumerationen till en ny klientorganisation. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Ta bort Azure Container Registry

Arbetsytan Azure Machine Learning använder Azure Container Registry (ACR) för vissa åtgärder. Den skapar automatiskt en ACR-instans när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI-tillägget för maskininlärning finns i [az ml-dokumentationen.](/cli/azure/ml)
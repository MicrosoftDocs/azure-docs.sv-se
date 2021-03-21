---
title: Skript exempel för Azure CLI – skapa en Azure App konfigurations lager
titleSuffix: Azure App Configuration
description: Skapa en Azure App konfigurations lagring med ett exempel skript för Azure CLI. Se referens artikel länkar till kommandon som används i skriptet.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4554ca64dccaa69e3a6fb63e5c28a6bcbb1a32e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930848"
---
# <a name="create-an-azure-app-configuration-store"></a>Skapa ett Azure App Configuration-arkiv

Det här exempel skriptet skapar en ny instans av Azure App konfiguration i en ny resurs grupp.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en ny resurs grupp och ett konfigurations Arkiv för appar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az appconfig create](/cli/azure/appconfig#az-appconfig-create) | Skapar en resurs för konfigurations Arkiv för appar. |
| [lista med AZ appconfig-autentiseringsuppgifter](/cli/azure/appconfig/credential#az-appconfig-credential-list) | Visa en lista med åtkomst nycklar för ett konfigurations Arkiv för appar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare skript exempel för programkonfigurations-CLI finns i [Azure App konfigurations-CLI-exempel](../cli-samples.md).

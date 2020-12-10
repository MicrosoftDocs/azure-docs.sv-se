---
title: 'CLI: ansluta en app till Cosmos DB'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service-app. Det här exemplet visar hur du ansluter en app till MongoDB (Cosmos DB).
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: fa971bbb8526f1b0bc081e61ed5e7a7f2cf76d1f
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006369"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>Ansluta en App Service-app till Cosmos DB med hjälp av CLI

Det här exempelskriptet skapar ett Azure Cosmos DB-konto med hjälp av API:et för Azure Cosmos DB för MongoDB och en App Service-app. Det länkar sedan en MongoDB-anslutningssträng till webbappen med hjälp av appinställningar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, App Service-appen, Cosmos DB och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Skapar en App Service-app. |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Cosmos DB-konto. |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Anger anslutningssträngar för det angivna Cosmos DB-kontot. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Skapar eller uppdaterar en appinställning för en App Service-app. Appinställningarna visas som miljövariabler för din app. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).

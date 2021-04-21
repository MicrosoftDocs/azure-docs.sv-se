---
title: Skapa resurslås för en databas och en samling för MongoDB API för Azure Cosmos DB
description: Skapa resurslås för en databas och en samling för MongoDB API för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: ee298b33736ae25b365cc54ee1bb5ec9f38bfb62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763621"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Skapa ett resurslås för Azure Cosmos DB API för MongoDB med Hjälp av Azure CLI
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

> [!IMPORTANT]
> Resurslås fungerar inte för ändringar som görs av användare som ansluter med mongoDB SDK, Mongoshell, verktyg eller Azure Portal om inte Cosmos DB-kontot först låses med `disableKeyBasedMetadataWriteAccess` egenskapen aktiverad. Mer information om hur du aktiverar den här egenskapen finns i [Förhindra ändringar från SDK:er.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | Skapar ett lås. |
| [az lock list](/cli/azure/lock#az_lock_list) | Lista låsinformation. |
| [az lock show](/cli/azure/lock#az_lock_show) | Visa egenskaper för ett lås. |
| [az lock delete](/cli/azure/lock#az_lock_delete) | Tar bort ett lås. |

## <a name="next-steps"></a>Nästa steg

- [Låsa resurser för att förhindra oväntade ändringar](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

- [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

---
title: Skapa resurslås för en Azure Cosmos DB Tabell-API tabell
description: Skapa resurslås för en Azure Cosmos DB Tabell-API tabell
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 04f8d541fc534a60550ba77d9775b340571a504f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788980"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Skapa resurslås för en Azure Cosmos DB Tabell-API tabell med Azure CLI
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

> [!IMPORTANT]
> Resurslås fungerar inte för ändringar som görs av användare som ansluter Cosmos DB Table SDK, Azure Storage Table SDK, verktyg som ansluter via kontonycklar eller Azure Portal om inte Cosmos DB-kontot först låses med egenskapen `disableKeyBasedMetadataWriteAccess` aktiverad. Mer information om hur du aktiverar den här egenskapen finns i [Förhindra ändringar från SDK:er.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

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

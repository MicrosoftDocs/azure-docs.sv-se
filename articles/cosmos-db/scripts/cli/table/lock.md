---
title: Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell
description: Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6df5f3842ba08b04dd82910d3762472f4ff4febd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566817"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Skapa resurs lås för en Azure Cosmos DB Tabell-API tabell med Azure CLI
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

> [!IMPORTANT]
> Resurs lås fungerar inte för ändringar som gjorts av användare som ansluter Cosmos DB Table SDK, Azure Storage Table SDK, alla verktyg som ansluter via konto nycklar eller Azure Portal om inte Cosmos DB-kontot först är låst med `disableKeyBasedMetadataWriteAccess` egenskapen aktive rad. Mer information om hur du aktiverar den här egenskapen finns i [förhindra ändringar från SDK](../../../role-based-access-control.md#prevent-sdk-changes): er.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [AZ lås skapa](/cli/azure/lock#az-lock-create) | Skapar ett lås. |
| [AZ lås lista](/cli/azure/lock#az-lock-list) | Visa information om lås. |
| [AZ lås show](/cli/azure/lock#az-lock-show) | Visa lås egenskaper. |
| [AZ lås borttagning](/cli/azure/lock#az-lock-delete) | Tar bort ett lås. |

## <a name="next-steps"></a>Nästa steg

- [Låsa resurser för att förhindra oväntade ändringar](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

- [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

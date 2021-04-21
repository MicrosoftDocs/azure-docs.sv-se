---
title: Skapa ett Azure Cosmos-konto med tjänstslutpunkter för virtuellt nätverk
description: Skapa ett Azure Cosmos-konto med tjänstslutpunkter för virtuellt nätverk
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6aa8da221818f807c29310f0b124b58ae70b853e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770827"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Skapa ett Azure Cosmos-konto med tjänstslutpunkter för virtuellt nätverk med hjälp av Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar ett nytt virtuellt nätverk med ett klient- och serverundernät och aktiverar tjänstslutpunkter för `Microsoft.AzureCosmosDB` . Den hämtar sedan resurs-ID:t för det här undernätet och tillämpar det på Azure Cosmos-kontot och aktiverar tjänstslutpunkter för kontot.

> [!NOTE]
> Det här exemplet visar hur du använder ett CORE-API-konto (SQL). Om du vill använda det här exemplet för andra API:er tillämpar du parametrarna och i `enable-virtual-network` skriptet nedan på ditt `virtual-network-rules` API-specifika skript.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar ett undernät för ett virtuellt Azure-nätverk. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Returnerar ett undernät för ett virtuellt Azure-nätverk. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Skapar ett Azure Cosmos DB-konto. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om cli Azure Cosmos DB finns i [cli Azure Cosmos DB dokumentationen](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i Azure Cosmos DB [CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

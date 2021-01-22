---
title: PowerShell-skript för att ändra prioritet för redundans för ett Azure Cosmos-konto med en enda Skriv region
description: Azure PowerShell skript exempel – ändra prioritet för redundans eller utlös redundans för ett Azure Cosmos-konto med en enda Skriv region
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: ef9b16ae56c560d033cb18de6a3ccf3b87fbc25a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684361"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>Ändra växlings prioritet eller utlös redundans för ett Azure Cosmos-konto med en enda Skriv region med hjälp av PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell AZ 5.4.0 eller senare. Kör `Get-Module -ListAvailable Az` för att se vilka versioner som är installerade.
Om du behöver installera, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Kör [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på Azure.

## <a name="sample-script"></a>Exempelskript

> [!NOTE]
> Alla ändringar i en region med `failoverPriority=0` utlöser en manuell redundans och kan bara göras till ett konto som kon figurer ATS för manuell redundansväxling. Ändringar i alla andra regioner ändrar bara växlings prioriteten för ett Cosmos-konto.
> [!NOTE]
> Det här exemplet visar hur du använder ett SQL (Core) API-konto. Om du vill använda det här exemplet för andra API: er kopierar du de relaterade egenskaperna och tillämpar på ditt API-speciella skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Listar Cosmos DB konton eller hämtar ett angivet Cosmos DB-konto. |
| [Uppdatera – AzCosmosDBAccountFailoverPriority](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Uppdatera prioritets ordningen för redundans för ett Cosmos DB kontos regioner. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).
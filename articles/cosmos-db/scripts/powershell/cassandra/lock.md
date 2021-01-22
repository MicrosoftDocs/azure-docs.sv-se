---
title: PowerShell-skript för att skapa resurs lås för Azure Cosmos API för Cassandra-tecken utrymme och tabell
description: Skapa resurs lås för Azure Cosmos API för Cassandra tecken utrymme och tabell
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: f8e1e2172137c1c6a569fb169c59191ab11cedfe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684389"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Skapa ett resurs lås för Azure Cosmos API för Cassandra tecken utrymme och tabell med Azure PowerShell
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell AZ 5.4.0 eller senare. Kör `Get-Module -ListAvailable Az` för att se vilka versioner som är installerade.
Om du behöver installera, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Kör [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på Azure.

> [!IMPORTANT]
> Resurs lås fungerar inte för ändringar som gjorts av användare som ansluter med hjälp av Cassandra SDK, CQL Shell eller Azure Portal om inte Cosmos DB-kontot först är låst med `disableKeyBasedMetadataWriteAccess` egenskapen aktive rad. Mer information om hur du aktiverar den här egenskapen finns i [förhindra ändringar från SDK](../../../role-based-access-control.md#prevent-sdk-changes): er.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure-resurs**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Skapar ett resurs lås. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Hämtar ett resurs lås eller visar en lista över resurs lås. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Tar bort ett resurs lås. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/).
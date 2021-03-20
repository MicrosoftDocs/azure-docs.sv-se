---
title: 'PowerShell: lägga till en elastisk pool i en grupp för automatisk redundans'
description: Azure PowerShell exempel skript för att skapa en Azure SQL Database elastisk pool, lägga till den i en grupp för automatisk redundans och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: a8326249b48e98eccecb5f0c4953add26e167560
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594121"
---
# <a name="use-powershell-to-add-an-elastic-pool-to-a-failover-group"></a>Använd PowerShell för att lägga till en elastisk pool i en failover-grupp
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här Azure PowerShell skript exemplet skapar en databas i Azure SQL Database, lägger till den i en elastisk pool, skapar en failover-grupp och testar redundans.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel på server nivå för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny databas. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en Elastic Database-pool.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny grupp för redundans. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera databaser. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera databaser i en failover-grupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller visar databasens failover-grupper. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundansväxling av en databas grupp för växling vid fel. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../powershell-script-content-guide.md).

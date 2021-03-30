---
title: 'PowerShell: skapa en enskild databas'
description: Använd ett Azure PowerShell exempel skript för att skapa en enda databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3666c1c2c6721d803266395f6b9875a6797a92f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040487"
---
# <a name="use-powershell-to-create-a-single-database-and-configure-a-server-level-firewall-rule"></a>Använd PowerShell för att skapa en enskild databas och konfigurera en brand Väggs regel på server nivå

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här Azure PowerShell skript exemplet skapar en enda databas i Azure SQL Database och konfigurerar en brand Väggs regel på server nivå. När skriptet har körts kan databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/create-and-configure-database/create-and-configure-database.ps1?highlight=15-16 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel på server nivå för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas på en server. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../powershell-script-content-guide.md).

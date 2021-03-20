---
title: 'PowerShell: lägga till en hanterad instans i en grupp för automatisk redundans'
titleSuffix: Azure SQL Managed Instance
description: Azure PowerShell exempel skript för att skapa en hanterad instans, lägga till den i en grupp för automatisk redundans och testa redundans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: af1d7110cccfd8b0617d6c79eb5373cc7c087159
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594019"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>Använda PowerShell för att lägga till en hanterad instans i en failover-grupp 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Det här PowerShell-skriptet skapar två hanterade instanser, lägger till dem i en grupp för växling vid fel och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den. Du måste ta bort resurs gruppen två gånger. Om du tar bort resurs gruppen första gången tas den hanterade instansen och virtuella kluster bort, men fel meddelandet kommer att Miss sen `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Kör kommandot Remove-AzResourceGroup en andra gång för att ta bort eventuella kvarvarande resurser samt resurs gruppen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | skapa en nätverkssäkerhetsgrupp | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en routningstabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en under näts konfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverks säkerhets grupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverks säkerhets regel konfiguration i en nätverks säkerhets grupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverks säkerhets grupp.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en väg i en routningstabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en routningstabell.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en hanterad instans.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Returnerar information om den hanterade Azure SQL-instansen. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en Virtual Network Gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en Virtual Network Gateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella Nätverksgatewayen.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny failover-grupp för Azure SQL-hanterad instans.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar redundans grupper för SQL-hanterade instanser.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling av en failover-grupp med SQL-hanterad instans. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/azure/).

Ytterligare PowerShell-skript exempel för SQL-hanterad instans finns i [PowerShell-skript för Azure SQL-hanterad](../../database/powershell-script-content-guide.md)instans.

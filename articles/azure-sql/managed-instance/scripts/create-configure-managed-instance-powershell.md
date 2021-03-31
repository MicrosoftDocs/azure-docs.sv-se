---
title: 'PowerShell: skapa en hanterad instans'
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln innehåller ett Azure PowerShell exempel skript för att skapa en hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323880"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Använd PowerShell för att skapa en hanterad instans

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Det här PowerShell-skript exemplet skapar en hanterad instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en routningstabell och en nätverks säkerhets grupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera virtuell Azure-dator för att ansluta till Azure SQL Database Hanterad instans](../connect-vm-instance-configure.md) och [Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans från den lokala](../point-to-site-p2s-configure.md)datorn.

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](../resource-limits.md#supported-regions) och [prenumerations typer som stöds](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder några av följande kommandon. Om du vill ha mer information om hur du använder och andra kommandon i tabellen nedan, klickar du på länkarna till den aktuella dokumentationen.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Anger mål tillstånd för ett virtuellt nätverk. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hämtar ett undernät i ett virtuellt nätverk. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfigurerar mål tillstånd för en under näts konfiguration i ett virtuellt nätverk. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Skapar en routningstabell. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hämtar routningstabeller. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Anger mål status för en routningstabell. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Skapar en hanterad instans. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/azure/).

Ytterligare PowerShell-skript exempel för Azure SQL-hanterad instans finns i [PowerShell-skript för Azure SQL-hanterad instans](../../database/powershell-script-content-guide.md).

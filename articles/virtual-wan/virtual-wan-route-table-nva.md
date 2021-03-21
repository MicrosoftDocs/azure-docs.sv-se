---
title: 'Virtuellt WAN: Skapa virtuell hubb väg tabell till NVA: Azure PowerShell'
description: Väg tabell för virtuell WAN-hubb som styr trafik till en virtuell nätverks installation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267133"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Skapa en väg tabell för virtuell hubb för att styra trafik till en virtuell nätverks installation

Den här artikeln visar hur du styr trafik från en virtuell hubb till en virtuell nätverks installation. 

![Virtual WAN-diagram](./media/virtual-wan-route-table-nva/vwanroute.png)

I den här artikeln lär du dig hur du:

* Skapa WAN (Wide Area Network)
* Skapa en hubb
* Skapa virtuella hubb nätverk anslutningar
* Skapa en hubb väg
* Skapa en routningstabell
* Använda routningstabellen

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kontrol lera att du har uppfyllt följande kriterier:

* Du har en virtuell nätverks installation (NVA). Det här är ett tredjepartsprogram som du väljer som vanligt vis tillhandahålls från Azure Marketplace i ett virtuellt nätverk.
* Du har en privat IP tilldelad till NVA-nätverks gränssnittet. 
* Det går inte att distribuera NVA i den virtuella hubben. Den måste distribueras i ett separat VNet. I den här artikeln kallas NVA VNet som "DMZ VNet".
* DMZ VNet kan ha ett eller flera virtuella nätverk som är anslutna till det. I den här artikeln kallas det här virtuella nätverket som ' indirekt eker VNet '. Dessa virtuella nätverk kan anslutas till DMZ VNet med VNet-peering.
* Kontrol lera att du har 2 virtuella nätverk redan skapat. Dessa kommer att användas som eker-virtuella nätverk. I den här artikeln är adress utrymmen för VNet-ekrar 10.0.2.0/24 och 10.0.3.0/24. Om du behöver information om hur du skapar ett VNet, se [skapa ett virtuellt nätverk med hjälp av PowerShell](../virtual-network/quick-create-powershell.md).
* Se till att det inte finns några virtuella nätverksgateway i någon virtuella nätverk.

## <a name="1-sign-in"></a><a name="signin"></a>1. Logga in

Se till att du installerar den senaste versionen av PowerShell-cmdletarna för Resource Manager. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). Detta är viktigt eftersom tidigare versioner av cmdletarna inte innehåller de aktuella värden som du behöver för den här övningen.

1. Öppna PowerShell-konsolen med utökade privilegier och logga in på ditt Azure-konto. Med den här cmdleten uppmanas du att ange inloggnings uppgifterna. När du har loggat in hämtas dina konto inställningar så att de är tillgängliga för Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Hämta en lista över dina Azure-prenumerationer.

   ```powershell
   Get-AzSubscription
   ```
3. Ange den prenumeration som du vill använda.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Skapa resurser

1. Skapa en resursgrupp.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Skapa ett virtuellt WAN-nätverk.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Skapa en virtuell hubb.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. skapa anslutningar

Skapa virtuella hubbar för virtuella nätverk från det indirekta eker VNet och DMZ VNet till den virtuella hubben.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. skapa en virtuell Hubbs väg

I den här artikeln är de indirekta, VNet-adress utrymmena för virtuella 10.0.2.0/24 och 10.0.3.0/24, och DMZ NVA nätverks gränssnittets privata IP-adress är 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. skapa en väg tabell för virtuell hubb

Skapa en väg tabell för virtuell hubb och tillämpa sedan den skapade vägen till den.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. genomför ändringarna

Genomför ändringarna i den virtuella hubben.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).

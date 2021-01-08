---
title: Diagnostisera ett problem med Routning av VM-nätverk – Azure PowerShell
titleSuffix: Azure Network Watcher
description: I den här artikeln får du lära dig hur du diagnostiserar ett problem med nätverks routning i en virtuell dator med hjälp av nästa hopp funktion i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020221"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostisera ett problem med nätverks dirigering för virtuella datorer – Azure PowerShell

I den här artikeln distribuerar du en virtuell dator (VM) och kontrollerar sedan kommunikationen med en IP-adress och URL. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell- `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.



## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Skapa den virtuella datorn med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats och PowerShell returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikation med Network Watcher måste du först aktivera en nätverks bevakare i den region som den virtuella datorn som du vill testa är i och sedan använda Network Watchers nästa hopp-funktion för att testa kommunikationen.

## <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har en nätverks Övervakare som är aktive rad i regionen USA, östra, använder du [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) för att hämta nätverks bevakaren. I följande exempel hämtas en befintlig nätverksbevakare med namnet *NetworkWatcher_eastus* som finns i resursgruppen *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Om du inte redan har en nätverks Övervakare som är aktive rad i regionen USA, östra, använder du [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) för att skapa en nätverks övervakare i regionen USA, östra:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Om du vill testa routning från en virtuell dator använder du kommandot [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) för att fastställa nästa dirigering när trafiken är avsedd för en speciell adress.

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Efter några sekunder informerar utdata om att **NextHopType** är **Internet** och att **RouteTableId** är **system väg**. Det innebär att du vet att det finns en giltig väg till målet.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Utdata som returneras informerar dig om att **ingen** är **NextHopType** och att **RouteTableId** också är **system väg**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Granska de effektiva vägarna för nätverks gränssnittet med kommandot [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) för att analysera routning ytterligare:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Utdata som innehåller följande text returneras:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Som du kan se i föregående utdata dirigerar vägen med **AddressPrefix** av **0.0.0.0/0** all trafik som inte är avsedd för adresser inom andra vägars adressprefix med ett nästa hopp på **Internet**. Som du kan också se i utdata, men det finns en standard väg till prefixet 172.16.0.0/12, som innehåller 172.31.0.100-adressen, är **NextHopType** **ingen**. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel har lagt till adress intervallet 172.16.0.0/12 i det virtuella nätverkets adress utrymme ändrar Azure **nextHopType** till det **virtuella nätverket** för vägen. En kontroll visar sedan det **virtuella nätverket** som **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och en diagnostiserad nätverks dirigering från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan du också bestämma svars tiden och tillåten och nekad nätverks trafik mellan den virtuella datorn och en slut punkt med hjälp av Network Watcher [anslutnings fel söknings](network-watcher-connectivity-powershell.md) funktion. Du kan övervaka kommunikationen mellan en virtuell dator och en slut punkt, till exempel en IP-adress eller URL, med tiden med hjälp av funktionen för Network Watcher anslutnings övervakaren. Mer information finns i [övervaka en nätverks anslutning](connection-monitor.md).

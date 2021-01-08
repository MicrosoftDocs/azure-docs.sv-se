---
title: 'Snabb start: diagnostisera ett problem med trafik filter för virtuella dator nätverk – Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Lär dig hur du använder Azure PowerShell för att diagnostisera ett problem med nätverks trafik filter från en virtuell dator med hjälp av IP-flödet verifiera Azure-Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: de46d71127f992ea573d1f2d778afcb7f46ed3e6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013319"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Snabbstart: Diagnostisera problem med filtreringen av nätverkstrafik på virtuella datorer – Azure PowerShell

I den här snabbstarten ska du distribuera en virtuell dator (VM) och kontrollera kommunikationen till en IP-adress och URL och från en IP-adress. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här snabb starten Azure PowerShell- `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.



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

För att testa nätverkskommunikation med Network Watcher måste du först aktivera en nätverksbevakare i den region där den virtuella dator du vill testa finns i, och sedan använda Kontrollera IP-flöde i Network Watcher för att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

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

### <a name="use-ip-flow-verify"></a>Använda Kontrollera IP-flöde

När du skapar en virtuell dator tillåter och nekar Azure nätverkstrafik till och från den virtuella datorn som standard. Om du vill kan du åsidosätta standardinställningarna i Azure och tillåta eller neka andra typer av trafik. Om du vill testa om trafik tillåts eller nekas till olika mål och från en käll-IP-adress använder du kommandot [test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) .

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Resultatet visas efter flera sekunder och anger att åtkomsten tillåts av en säkerhetsregel med namnet **AllowInternetOutbound**.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Resultatet som returneras anger att åtkomsten nekas av en säkerhetsregel med namnet **DefaultOutboundDenyAll**.

Testa inkommande kommunikation till den virtuella datorn från 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Resultatet som returneras anger att åtkomsten nekas på grund av en säkerhetsregel med namnet **DefaultInboundDenyAll**. Nu när du vet vilka säkerhetsregler som tillåter eller nekar trafik till eller från en virtuell dator kan du lättare avgöra hur du ska lösa problemet.

## <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

För att avgöra varför reglerna i [testa nätverkskommunikation](#test-network-communication) tillåter eller förhindrar kommunikation, granska de effektiva säkerhets reglerna för nätverks gränssnittet med [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

Returnerade utdata innehåller följande text för regeln **AllowInternetOutbound** som tillåter utgående åtkomst till www.bing.com i [Använda Kontrollera IP-flöde](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Du kan se i utdata att **DestinationAddressPrefix** är **Internet**. Det är dock oklart hur 13.107.21.200, adressen som du testade i [Använda Kontrollera IP-flöde](#use-ip-flow-verify), relaterar till **Internet**. Du ser flera adressprefix listade under **ExpandedDestinationAddressPrefix**. Ett av prefixen i listan är **12.0.0.0/6**, vilken omfattar IP-adressintervallet 12.0.0.1–15.255.255.254. Eftersom 13.107.21.200 ligger inom det adressintervallet tillåter regeln **AllowInternetOutBound** den utgående trafiken. Dessutom finns det ingen högre regler för **prioritet** (lägre nummer) listade i utdata som returneras av `Get-AzEffectiveNetworkSecurityGroup`, som åsidosätter den här regeln. Om du vill neka utgående kommunikation till 13.107.21.200 kan du lägga till en säkerhetsregel med högre prioritet, som nekar utgående trafik på port 80 till IP-adressen.

När du körde kommandot `Test-AzNetworkWatcherIPFlow` för att testa utgående kommunikation till 172.131.0.100 i [Använda Kontrollera IP-flöde](#use-ip-flow-verify) angavs i utdata att regeln **DefaultOutboundDenyAll** nekad kommunikationen. Regeln **DefaultOutboundDenyAll** är lika med regeln **DenyAllOutBound** som visas i följande utdata från kommandot `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

Regeln listar **0.0.0.0/0** som **DestinationAddressPrefix**. Regeln nekar utgående kommunikation till 172.131.0.100, eftersom adressen inte är inom **DestinationAddressPrefix** för någon av de andra reglerna för utgående trafik i utdata från kommandot `Get-AzEffectiveNetworkSecurityGroup`. Om du vill tillåta den utgående kommunikationen kan du lägga till en säkerhetsregel med högre prioritet, som tillåter utgående trafik på port 80 på 172.131.0.100.

När du körde kommandot `Test-AzNetworkWatcherIPFlow` för att testa inkommande kommunikation från 172.131.0.100 i [Använda Kontrollera IP-flöde](#use-ip-flow-verify) angavs i utdata att regeln **DefaultOutboundDenyAll** nekad kommunikationen. Regeln **DefaultInboundDenyAll** är lika med regeln **DenyAllInBound** som visas i följande utdata från kommandot `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

Regeln **DenyAllInBound** används eftersom, som du ser i utdata, det inte finns någon regeln med högre prioritet i utdata från kommandot `Get-AzEffectiveNetworkSecurityGroup` som tillåter port 80-inkommande till den virtuella datorn från 172.131.0.100. Om du vill tillåta den inkommande kommunikationen kan du lägga till en säkerhetsregel med högre prioritet, som tillåter inkommande trafik på port 80 från 172.131.0.100.

Kontrollerna i den här snabbstarten testade Azure-konfigurationen. Om kontrollerna returnerar förväntat resultat och du fortfarande har problem med nätverket kontrollerar du att det inte finns en brandvägg mellan den virtuella datorn och den slutpunkt som du kommunicerar med, samt att operativsystemet på den virtuella datorn inte har en brandvägg som tillåter eller nekar kommunikationen.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator och diagnostiserade filter för inkommande och utgående nätverkstrafik. Du lärde dig att regler för nätverkssäkerhetsgrupper tillåter eller nekar trafik till och från en virtuell dator. Lär dig mer om [säkerhetsregler](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar säkerhetsregler](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Kommunikationen till en virtuell dator kan misslyckas även om rätt trafikfilter används på grund av routningskonfigurationen. Mer information om hur du diagnostiserar problem med VM-routning finns i [Diagnostisera problem med VM-routning](diagnose-vm-network-routing-problem-powershell.md). Information om hur du diagnostiserar problem med utgående routning, svarstider och trafikfiltrering med ett verktyg finns i avsnittet om hur du [felsöker anslutningar](network-watcher-connectivity-powershell.md).
---
title: Självstudie – belastningsutjämna virtuella Windows-datorer i Azure
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att skapa en lastbalanserare för ett säkert program med hög tillgänglighet på tre virtuella Windows-datorer
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9d33944e046e1e5e2324f73ae26c78cf29d8f97d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734721"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Självstudier: Belastningsutjämna virtuella Windows-datorer i Azure för att skapa ett program med hög tillgänglighet med Azure PowerShell
Med belastningsutjämning får du högre tillgänglighet genom att inkommande begäranden sprids över flera virtuella datorer. I den här kursen får du lära dig mer om de olika komponenterna i Azure Load Balancer som distribuerar trafik och ger hög tillgänglighet. Lär dig att:

> [!div class="checklist"]
> * skapa en Azure Load Balancer
> * skapa en hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * skapa en grundläggande IIS-webbplats med tillägget för anpassat skript
> * skapa virtuella datorer och anslut dem till en lastbalanserare
> * visa en lastbalanserare i praktiken
> * lägga till och ta bort virtuella datorer från en lastbalanserare.

## <a name="azure-load-balancer-overview"></a>Översikt över Azure Load Balancer
En Azure-lastbalanserare är en Layer-4-lastbalanserare (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik till felfria virtuella datorer. Lastbalanseraren har en hälsoavsökningsfunktion som övervakar en given port på varje virtuell dator och ser till att trafik endast distribueras till virtuella datorer som fungerar.

Du definierar en IP-konfiguration på klientdelen som innehåller en eller flera offentliga IP-adresser. IP-konfigurationen på klientdelen gör att det går att komma åt lastbalanseraren och program via Internet. 

Virtuella datorer ansluter till en lastbalanserare med ett virtuellt nätverkskort. För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren.

För att styra trafikflödet definierar du regler för lastbalanseraren för specifika portar och protokoll som mappar till dina virtuella datorer.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer
I det här avsnittet beskrivs hur du skapar och konfigurerar varje komponent i lastbalanseraren. Innan du kan skapa lastbalanseraren skapar du en resursgrupp med hjälp av [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupLoadBalancer* på platsen *EastUS* (Östra USA):

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för lastbalanseraren. Skapa en offentlig IP-adress med hjälp av [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i resursgruppen *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Skapa en lastbalanserare
Skapa en IP-adresspool på klientdelen med hjälp av [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). I följande exempel skapas en IP-adresspool på klientdelen med namnet *myFrontEndPool* och adressen *myPublicIP* kopplas: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Skapa en adresspool på serverdelen med hjälp av [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuella datorer ansluter till den här adresspoolen i de resterande stegen. I följande exempel skapas en backend-adresspool med namnet *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Skapa nu lastbalanseraren med hjälp av [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). I följande exempel skapas en lastbalanserare med namnet *myLoadBalancer* med de IP-pooler för klient- och serverdelen som skapades i föregående steg:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Som standard tas en virtuell dator bort från lastbalanserarens distribution efter två fel i följd inom ett intervall på 15 sekunder. Du skapar en hälsoavsökning baserat på ett protokoll eller en specifik hälsokontrollsida för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar om du vill ha mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökning måste du skapa en hälsokontrollsida, till exempel *healthcheck.aspx*. Avsökningen måste returnera svaret **HTTP 200 OK** för att lastbalanseraren ska behålla värden i rotation.

Du skapar en TCP-hälsoavsökning med hjälp av [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe* som övervakar alla virtuella datorer på *TCP*-port *80*:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

För att använda hälsoavsökningen uppdaterar du lastbalanseraren med hjälp av [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen för den inkommande trafiken och IP-poolen i serverdelen som ska ta emot trafiken, samt nödvändiga käll- och målportar. För att säkerställa att de virtuella datorerna endast tar emot felfri trafik definierar du också vilken hälsoavsökning som ska användas.

Skapa en lastbalanseringsregel med hjälp av [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). I följande exempel skapas en lastbalanseringsregel med namnet *myLoadBalancerRule* och trafiken utjämnas på *TCP*-port *80*:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Uppdatera lastbalanseraren med hjälp av [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du kan distribuera virtuella datorer och testa din belastningsutjämnare skapar du virtuella nätverksresurser. Mer information om virtuella nätverk finns i självstudiekursen [Hantera virtuella Azure-nätverk](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Skapa nätverksresurser
Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuella nätverkskort skapas med hjälp av [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). I följande exempel skapas tre virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i lastbalanseraren:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Skapa virtuella datorer
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med hjälp av [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa de virtuella datorerna med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas tre virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Job`. Det tar några minuter att skapa och konfigurera de tre virtuella datorerna.


### <a name="install-iis-with-custom-script-extension"></a>Installera IIS med tillägget för anpassat skript
I en tidigare självstudiekurs om [hur du anpassar en virtuell Windows-dator](tutorial-automate-vm-deployment.md) lärde du dig hur du automatiserar VM-anpassning med tillägget för anpassat skript för Windows. Du kan använda samma tillvägagångssätt för att installera och konfigurera IIS på dina virtuella datorer.

Använd [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) för att installera Anpassat skripttillägg. Tillägget kör `powershell Add-WindowsFeature Web-Server` för att installera IIS-webbservern och uppdaterar sedan sidan *Default.htm* till att visa värddatornamnet för den virtuella datorn:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testa lastbalanseraren
Hämta den offentliga IP-adressen för lastbalanseraren med hjälp av [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra IIS-webbplatsen](./media/tutorial-load-balancer/running-iis-website.png)

Om du vill se hur lastbalanseraren distribuerar trafik över alla tre virtuella datorer som kör din app, kan du framtvinga uppdatering av webbläsaren.


## <a name="add-and-remove-vms"></a>Lägga till och ta bort virtuella datorer
Du kan behöva utföra underhåll på de virtuella datorerna som kör appen, till exempel installera uppdateringar av operativsystemet. För att klara ökad trafik till din app kan du behöva lägga till fler virtuella datorer. I det här avsnittet visas hur du tar bort eller lägger till en virtuell dator från lastbalanseraren.

### <a name="remove-a-vm-from-the-load-balancer"></a>Ta bort en virtuell dator från lastbalanseraren
Hämta nätverkskortet med hjälp av [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) och ange sedan egenskapen *LoadBalancerBackendAddressPools* för det virtuella nätverkskortet till *$null*. Uppdatera slutligen det virtuella nätverkskortet:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Om du vill se hur lastbalanseraren distribuerar trafik över de två återstående virtuella datorerna som kör din app, kan du framtvinga uppdatering av webbläsaren. Nu kan du utföra underhåll på den virtuella datorn, till exempel installera uppdateringar av operativsystemet eller göra en omstart av den virtuella datorn.

### <a name="add-a-vm-to-the-load-balancer"></a>Lägga till en virtuell dator i lastbalanseraren
Om du behöver utöka kapaciteten efter underhåll av den virtuella datorn anger du egenskapen *LoadBalancerBackendAddressPools* för det virtuella nätverkskortet till *BackendAddressPool* från [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

Hämta lastbalanseraren:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du skapat en lastbalanserare och kopplat virtuella datorer till den. Du har lärt dig att:

> [!div class="checklist"]
> * skapa en Azure Load Balancer
> * skapa en hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * skapa en grundläggande IIS-webbplats med tillägget för anpassat skript
> * skapa virtuella datorer och anslut dem till en lastbalanserare
> * visa en lastbalanserare i praktiken
> * lägga till och ta bort virtuella datorer från en lastbalanserare.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar VM-nätverk.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer och virtuella nätverk](./tutorial-virtual-network.md)

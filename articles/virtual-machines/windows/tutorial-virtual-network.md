---
title: Självstudie – Skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer
description: I den här självstudien får du lära dig hur du använder Azure PowerShell för att skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d61f4be63a2f3b2f75228848e2e4fe18fb27f598
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702066"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Självstudie: Skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer med Azure PowerShell

Azures virtuella datorer använder Azure-nätverk för intern och extern nätverkskommunikation. Den här självstudien visar hur du distribuerar två virtuella datorer och konfigurerar Azure-nätverk för dem. Exemplen i den här självstudien förutsätter att de virtuella datorerna är värd för ett webbprogram med en databasserverdel, men det distribueras inte något program i självstudien. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen


## <a name="vm-networking-overview"></a>Nätverksöversikt för VM

Virtuella Azure-nätverk möjliggör säkra nätverks anslutningar mellan virtuella datorer, Internet och andra Azure-tjänster, till exempel Azure SQL Database. Virtuella nätverk är uppdelade i logiska segment som kallas undernät. Undernät används för att kontrollera nätverksflödet och som en säkerhetsgräns. När du distribuerar en virtuell dator ingår vanligtvis ett virtuellt nätverksgränssnitt som är anslutet till ett undernät.

När du slutför den här självstudien kommer du att se att följande resurser skapas:

![Virtuellt nätverk med två undernät](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Det virtuella nätverk som de virtuella datorerna använder för att kommunicera med varandra och Internet.
- *myFrontendSubnet* – Undernät i *myVNet* som används av klientdelsresurserna.
- *myPublicIPAddress* – Offentlig IP-adress som används för att få åtkomst till *myFrontendVM* från Internet.
- *myFrontendNic* – Nätverksgränssnitt som används av *myFrontendVM* till att kommunicera med *myBackendVM*.
- *myFrontendVM* – Den virtuella dator som används för att kommunicera mellan Internet och *myBackendVM*.
- *myBackendNSG* – Nätverkssäkerhetsgruppen som styr kommunikationen mellan *myFrontendVM* och *myBackendVM*.
- *myBackendSubnet* – Det undernät som är associerat med *myBackendNSG* och används av serverdelsresurserna.
- *myBackendNic* – Nätverksgränssnittet som används av *myBackendVM* till att kommunicera med *myFrontendVM*.
- *myBackendVM* – Den virtuella dator som använder port 1433 för att kommunicera med *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="create-subnet"></a>Skapa undernät 

I den här självstudien skapas ett enda virtuellt nätverk med två undernät. Ett klientdelsundernät som är värd för ett webbprogram och ett serverdelsundernät som är värd för en databasserver.

Innan du kan skapa ett virtuellt nätverk skapar du en resursgrupp med hjälp av [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Följande exempel skapar en resursgrupp med namnet *myRGNetwork* på platsen *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Skapa en undernätskonfiguration med namnet *myFrontendSubnet* med hjälp av [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Och skapa en undernätskonfiguration med namnet *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med namnet *myVNet* med hjälp av *myFrontendSubnet* och *myBackendSubnet* med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Ett nätverk har nu skapats och delats upp i två undernät, ett för klientdelstjänster och ett för serverdelstjänster. I nästa avsnitt skapar vi virtuella datorer och ansluter dem till dessa undernät.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Med en offentlig IP-adress blir Azure-resurser tillgängliga på Internet. Allokeringsmetoden för den offentliga IP-adressen kan konfigureras som dynamisk eller statisk. Som standard blir en offentlig IP-adress dynamiskt allokerad. Dynamiska IP-adresser släpps när de virtuella datorerna frigörs. Detta medför IP-adressen ändras vid åtgärder som innebär att en virtuell dator frigörs.

Allokeringsmetoden kan anges som statisk, vilket ser till att IP-adressen förblir tilldelad till en virtuell dator, även när den frigjorts. Om du använder en statisk IP-adress går det inte att ange själva IP-adressen. I stället allokeras den från en pool med tillgängliga adresser.

Skapar en offentlig IP-adress med namnet *myPublicIPAddress* med hjälp av [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Du kan ändra parametern -AllocationMethod för `Static` för att tilldela en statisk offentlig IP-adress.

## <a name="create-a-front-end-vm"></a>Skapa en virtuell dator för klientdelen

För att en virtuell dator ska kommunicera i ett virtuellt nätverk, behöver den ett virtuellt nätverksgränssnitt (NIC). Skapa ett nätverkskort med hjälp av [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1&preserve-view=true). Du använder autentiseringsuppgifterna för att ansluta till den virtuella datorn i ytterligare steg:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa de virtuella datorerna med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Skydda nätverkstrafiken

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). NSG:er kan vara associerade med undernät eller separata nätverksgränssnitt. En NSG som är associerad med ett nätverksgränssnitt tillämpas bara på den associerade virtuella datorn. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet.

### <a name="network-security-group-rules"></a>Regler för nätverkssäkerhetsgrupp

NSG-regler definierar nätverksportar där trafik tillåts eller nekas. Reglerna kan innehålla käll- och målintervall för IP-adresser så att trafiken styrs mellan specifika system eller undernät. NSG-regler kan även innehålla en prioritet (mellan 1 och 4 096). Reglerna utvärderas i prioritetsordning. En regel med en prioritet på 100 utvärderas före en regel med prioritet 200.

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet kan de åsidosättas av de regler som du skapar.

- **Virtuellt nätverk** – Trafik som kommer från eller som går till ett virtuellt nätverk tillåts både i inkommande och utgående riktning.
- **Internet** – Utgående trafik tillåts, men inkommande trafik blockeras.
- **Lastbalanserare** – Tillåter att Azures lastbalanserare avsöker hälsotillståndet för dina virtuella datorer och rollinstanser. Du kan åsidosätta den här regeln om du inte använder någon belastningsutjämnad uppsättning.

### <a name="create-network-security-groups"></a>Skapa nätverkssäkerhetsgrupper

Skapa en inkommande regel med namnet *myFrontendNSGRule* för att tillåta inkommande webbtrafik på *myFrontendVM* med hjälp av [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Du kan begränsa intern trafik till *myBackendVM* från endast *myFrontendVM* genom att skapa en NSG för serverdelsundernätet. Följande exempel skapar en NSG-regel med namnet *myLoadBalancerNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Lägg till en nätverkssäkerhetsgrupp med namnet *myFrontendNSG* med hjälp av [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Lägg nu till en nätverkssäkerhetsgrupp med namnet *myFrontendNSG* med hjälp av New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Lägg till nätverkssäkerhetsgrupper till undernäten:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Skapa en virtuell dator för serverdelen

Det enklaste sättet att skapa den virtuella serverdelsdatorn för den här självstudien är med en SQL Server-avbildning. Den här självstudien skapar bara den virtuella datorn med databasservern, men ger inte information om åtkomst till databasen.

Skapa *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Avbildningen i det här exemplet har SQL Server installerat men används inte i den här självstudien. Den ingår för att visa hur du kan konfigurera en virtuell dator att hantera webbtrafik och en virtuell dator att hantera databashantering.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat och skyddat Azure-nätverk som är relaterade till virtuella datorer. 

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen

Information om hur du skyddar dina virtuella dator diskar finns i [säkerhets kopiering och haveri beredskap för diskar](backup-and-disaster-recovery-for-azure-iaas-disks.md).

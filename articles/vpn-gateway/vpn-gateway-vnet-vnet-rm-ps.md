---
title: 'Ansluta ett VNet till ett annat VNet med en Azure VPN Gateway VNet-till-VNet-anslutning: PowerShell'
description: Anslut virtuella nätverk tillsammans med en VNet-till-VNet-anslutning och PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655382"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Konfigurera en VPN-gatewayanslutning mellan virtuella nätverk med hjälp av PowerShell

Den här artikeln hjälper dig ansluta virtuella nätverk via VNet-till-VNet-anslutningstypen. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. När du ansluter virtuella nätverk från olika prenumerationer, behöver inte prenumerationerna vara associerade med samma Active Directory-klient.

Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen och användning av PowerShell. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>Om att ansluta virtuella nätverk

Det finns flera sätt att ansluta till virtuella nätverk. I avsnitten nedan beskrivs olika sätt att ansluta till virtuella nätverk.

### <a name="vnet-to-vnet"></a>VNet-till-VNet

Att konfigurera en anslutning mellan virtuella nätverk är ett bra sätt att enkelt ansluta virtuella nätverk. Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk med VNet-till-VNet-anslutningstypen (VNet2VNet) på nästan samma sätt som du skapar en plats-till-plats-IPsec-anslutning till en lokal plats.  Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE, och båda fungerar på samma sätt vid kommunikation. Skillnaden mellan anslutningstyperna är hur den lokala nätverksgatewayen har konfigurerats. När du skapar en anslutning mellan virtuella nätverk ser du inte adressutrymmet för den lokala nätverksgatewayen. Den skapas och fylls i automatiskt. Om du uppdaterar adressutrymmet för ett virtuellt nätverk dirigerar det andra virtuella nätverket automatiskt till det uppdaterade adressutrymmet. Att skapa en anslutning mellan virtuella nätverk är normalt snabbare och enklare än att skapa en plats-till-plats-anslutning mellan virtuella nätverk.

### <a name="site-to-site-ipsec"></a>Plats-till-plats (IPsec)

Om du arbetar med komplicerade nätverkskonfigurationer kan du överväga att ansluta dina virtuella nätverk med hjälp av anvisningarna för [Plats-till-plats](vpn-gateway-create-site-to-site-rm-powershell.md) i stället för Vnet-till-Vnet. När du använder stegen för plats-till-plats skapar och konfigurerar du de lokala nätverksgatewayerna manuellt. Den lokala nätverksgatewayen för varje virtuellt nätverk behandlar det andra virtuella nätverket som en lokal plats. På så sätt kan du ange ytterligare adressutrymme för den lokala nätverksgatewayen för att dirigera trafik. Om adressutrymmet för ett virtuellt nätverk ändras måste du uppdatera den motsvarande lokala nätverksgatewayen för att avspegla ändringen. Den uppdateras inte automatiskt.

### <a name="vnet-peering"></a>VNet-peering

Det kan vara bättre att ansluta dina virtuella nätverk med hjälp av VNet-peering. VNET-peering använder ingen VPN-gateway och har även andra restriktioner. Dessutom beräknas [prissättningen för VNet-peering](https://azure.microsoft.com/pricing/details/virtual-network) på ett annat sätt jämfört med [prissättningen för VPN Gateway mellan virtuella nätverk](https://azure.microsoft.com/pricing/details/vpn-gateway). Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Varför ska jag skapa en anslutning mellan virtuella nätverk?

Du kan vilja ansluta virtuella nätverk med en anslutning mellan virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Traffic Manager och Load Balancer kan du konfigurera arbetsbelastning med hög tillgänglighet och geografisk redundans över flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regionala flernivåprogram med isolering eller administrativa gränser**

  * Inom samma region kan du konfigurera flernivåprogram med flera virtuella nätverk som är anslutna till varandra på grund av isolering eller administrativa krav.

VNet-till-VNet-kommunikation kan kombineras med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Vilka steg för VNet-till-VNet ska jag använda?

I den här artikeln beskrivs två uppsättningar med steg. En uppsättning steg för [virtuella nätverk som finns i samma prenumeration](#samesub) och en annan för [virtuella nätverk som finns i olika prenumerationer](#difsub).
Den största skillnaden mellan uppsättningarna är att du måste använda separata PowerShell-sessioner när du konfigurerar anslutningar för virtuella nätverk som finns i olika prenumerationer. 

För den här övningen kan du kombinera konfigurationer eller bara välja den du vill arbeta med. Alla konfigurationer använder anslutningstypen VNet-till-VNet. Nätverkstrafik flödar mellan virtuella nätverk som är direkt anslutna till varandra. I den här övningen dirigeras inte trafik från TestVNet4 till TestVNet5.

* [Virtuella nätverk som finns i samma prenumeration](#samesub): i stegen för den här konfigurationen används TestVNet1 och TestVNet4.

  ![Diagram som visar V net-till-V net-steg för V-nät som finns i samma prenumeration.](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Virtuella nätverk som finns i olika prenumerationer](#difsub): i stegen för den här konfigurationen används TestVNet1 och TestVNet5.

  ![v2v-diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Så här ansluter du VNets som finns i samma prenumeration

### <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Eftersom det tar upp till 45 minuter att skapa en gateway, kan Azure Cloud Shell tids gräns under den här övningen. Du kan starta om Cloud Shell genom att klicka längst upp till vänster i terminalen. Se till att omdeklarera eventuella variabler när du startar om terminalen.

* Om du hellre vill installera den senaste versionen av modulen Azure PowerShell lokalt, se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>Steg 1 – Planera dina IP-adressintervall

I följande steg ska vi skapa två virtuella nätverk och deras respektive gateway-undernät och konfigurationer. Sedan ska du skapa en VPN-anslutning mellan de två virtuella nätverken. Det är viktigt att planera IP-adressintervallen för nätverkskonfigurationen. Tänk på att inga av dina VNet-intervall eller lokala nätverksintervall får överlappa varandra på något sätt. I de här exemplen tar vi inte med någon DNS-server. Information om hur du använder namnmatchning för dina virtuella nätverk finns i [Namnmatchning](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Vi använder följande värden i exemplen:

**Värden för TestVNet1:**

* VNet-namn: TestVNet1
* Resursgrupp: TestRG1
* Plats: USA, östra
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* Offentlig IP: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (för VNet i olika prenumerationer)
* ConnectionType: VNet2VNet

**Värden för TestVNet4:**

* VNet-namn: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Resursgrupp: TestRG4
* Plats: USA, västra
* GatewayName: VNet4GW
* Offentlig IP: VNet4GWIP
* VPNType: RouteBased
* Anslutning: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>Steg 2 – Skapa och konfigurera TestVNet1

1. Verifiera dina prenumerations inställningar.

   Anslut till ditt konto om du kör PowerShell lokalt på datorn. Om du använder Azure Cloud Shell är du ansluten automatiskt.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Kontrollera prenumerationerna för kontot.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Deklarera dina variabler. I det här exemplet deklarerar vi variablerna med värdena för den här övningen. I de flesta fall bör du ersätta värdena med dina egna. Du kan dock använda dessa variabler om du bara vill följa anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna om det behövs och kopiera och klistra in dem i PowerShell-konsolen.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Skapa undernätskonfigurationerna för TestVNet1. I det här exemplet skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät – GatewaySubnet, FrontEnd och BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen. Därför är den inte tilldelad via variabeln nedan.

   I följande exempel används variablerna som du angav tidigare. I det här exemplet använder gateway-undernätet en /27. Även om det är möjligt att skapa ett gatewayundernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Skapa TestVNet1.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för det virtuella nätverket. Observera att AllocationMethod är Dynamic. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till gatewayen. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Skapa gateway-konfigurationen. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd exemplet för att skapa gateway-konfigurationen.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Skapa gatewayen för TestVNet1. I det här steget ska du skapa VNet-gatewayen för TestVNet1. VNet-till-VNet-konfigurationer kräver VpnType RouteBased. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

När du har slutfört kommandona tar det upp till 45 minuter att skapa den här gatewayen. Om du använder Azure Cloud Shell kan du starta om Cloud Shell-sessionen genom att klicka längst upp till vänster i Cloud Shell terminalen och sedan konfigurera TestVNet4. Du behöver inte vänta tills TestVNet1-gatewayen har slutförts.

### <a name="step-3---create-and-configure-testvnet4"></a>Steg 3 – Skapa och konfigurera TestVNet4

När du har konfigurerat TestVNet1 skapar du TestVNet4. Följ stegen nedan och ersätt värdena med dina egna vid behov.

1. Anslut och deklarera dina variabler. Ersätt värdena med de som du vill använda för din konfiguration.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Skapa undernätskonfigurationerna för TestVNet4.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Skapa TestVNet4.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Begär en offentlig IP-adress.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Skapa gateway-konfigurationen.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. Skapa TestVNet4-gatewayen. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>Steg 4 – Skapa anslutningarna

Vänta tills båda gatewayerna har slutförts. Starta om Azure Cloud Shell-sessionen och kopiera och klistra in variablerna från början av steg 2 och steg 3 i-konsolen för att deklarera om värden.

1. Hämta båda virtuella nätverksgatewayerna.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Skapa TestVNet1-till-TestVNet4-anslutningen. I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet4. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Skapa TestVNet4-till-TestVNet1-anslutningen. Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet4 till TestVNet1. Kontrollera att de delade nycklarna matchar. Anslutningen upprättas efter några minuter.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Verifiera anslutningen. Mer information finns i avsnittet [Verifiera anslutningen](#verify).

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Så här ansluter du VNets som finns i olika prenumerationer

I det här scenariot ansluter vi TestVNet1 och TestVNet5. TestVNet1 och TestVNet5 finns i olika prenumerationer. Prenumerationerna behöver inte vara associerade med samma Active Directory-klient.

Skillnaden mellan de här stegen och den tidigare uppsättningen är att några av konfigurationsstegen måste utföras i en separat PowerShell-session för den andra prenumerationen. Detta gäller särskilt om två prenumerationer tillhör olika organisationer.

På grund av en ändring av prenumerations kontexten i den här övningen kan det vara lättare att använda PowerShell lokalt på datorn i stället för att använda Azure Cloud Shell när du går till steg 8.

### <a name="step-5---create-and-configure-testvnet1"></a>Steg 5 – Skapa och konfigurera TestVNet1

Du måste slutföra [Steg 1](#Step1) och [Steg 2](#Step2) från det tidigare avsnittet för att kunna skapa och konfigurera TestVNet1 och VPN Gateway för TestVNet1. I den här konfigurationen krävs det inte att du skapar TestVNet4 i enlighet med föregående avsnitt, men om du gör det går det ändå att använda de här instruktionerna. När du har slutfört steg 1 och steg 2 fortsätter du med steg 6 för att skapa TestVNet5.

### <a name="step-6---verify-the-ip-address-ranges"></a>Steg 6 – Kontrollera IP-adressintervaller

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet5, inte överlappar några av dina VNet-intervall eller lokala intervall för nätverksgatewayen. I det här exemplet kan de virtuella nätverken tillhöra olika organisationer. Använd följande VNet-värden för TestVNet5 i den här övningen:

**Värden för TestVNet5:**

* VNet-namn: TestVNet5
* Resursgrupp: TestRG5
* Plats: Japan, östra
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* Offentlig IP: VNet5GWIP
* VPNType: RouteBased
* Anslutning: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Steg 7 – Skapa och konfigurera TestVNet5

Det här steget måste utföras i den nya prenumerationen. Den här delen kan utföras av administratören i en annan organisation som äger prenumerationen.

1. Deklarera dina variabler. Ersätt värdena med de som du vill använda för din konfiguration.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Anslut till Prenumeration 5. Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Kontrollera prenumerationerna för kontot.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Ange den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Skapa en ny resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Skapa undernätskonfigurationerna för TestVNet5.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Skapa TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Begär en offentlig IP-adress.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Skapa gateway-konfigurationen.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Skapa TestVNet5-gatewayen.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>Steg 8 – Skapa anslutningarna

I det här exemplet där gatewayerna finns i olika prenumerationer, har vi delat upp steget i två PowerShell-sessioner som kallas för [Prenumeration 1] och [Prenumeration 5].

1. **[Prenumeration 1]** Hämta den virtuella Nätverksgatewayen för prenumeration 1. Logga in och Anslut till prenumeration 1 innan du kör följande exempel:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Kopiera utdatan från följande element och skicka dem till administratören för Prenumeration 5 via e-post eller någon annan metod.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Dessa två element har värden som liknar följande exempelutdata:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Prenumeration 5]** Hämta den virtuella nätverksgatewayen för Prenumeration 5. Logga in och Anslut till prenumeration 5 innan du kör följande exempel:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Kopiera utdatan från följande element och skicka dem till administratören för Prenumeration 1 via e-post eller någon annan metod.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Dessa två element har värden som liknar följande exempelutdata:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Prenumeration 1]** Skapa TestVNet1-till-TestVNet5-anslutningen. I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet5. Skillnaden här är att $vnet5gw inte kan hämtas direkt, eftersom den finns i en annan prenumeration. Du måste skapa ett nytt PowerShell-objekt med värdena från Prenumeration 1 i stegen ovan. Använd exemplet nedan. Ersätt namn, ID och delad nyckel med dina egna värden. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

   Anslut till Prenumeration 1 innan du kör följande exempel:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Prenumeration 5]** Skapa TestVNet5-till-TestVNet1-anslutningen. Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet5 till TestVNet1. Samma process att skapa ett PowerShell-objekt som baseras på de värden som erhållits från Prenumeration 1 gäller även här. Var noga med att de delade nycklarna matchar i det här steget.

   Anslut till Prenumeration 5 innan du kör följande exempel:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>Så här verifierar du en anslutning

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [dokumentationen för Virtual Machines](../index.yml).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
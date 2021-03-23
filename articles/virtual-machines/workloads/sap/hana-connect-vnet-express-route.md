---
title: Anslutning som konfigureras från ett virtuellt nätverk till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Anslutning som konfigureras från ett virtuellt nätverk för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775313"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Anslut ett virtuellt nätverk till HANA-stora instanser

När du har skapat ett virtuellt Azure-nätverk kan du ansluta nätverket till SAP HANA på stora Azure-instanser. Skapa en Azure ExpressRoute-gateway på det virtuella nätverket. Med den här gatewayen kan du länka det virtuella nätverket till ExpressRoute-kretsen som ansluts till kund klienten på den stora instans stämpeln i HANA.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra. Den nya gatewayen skapas i den angivna Azure-prenumerationen och ansluts sedan till det angivna virtuella Azure-nätverket.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Om det redan finns en gateway kontrollerar du om det är en ExpressRoute-gateway eller inte. Om det inte är en ExpressRoute-Gateway tar du bort gatewayen och skapar den igen som en ExpressRoute-Gateway. Om en ExpressRoute-Gateway redan har upprättats, se följande avsnitt i den här artikeln, "länka virtuella nätverk". 

- Använd antingen [Azure Portal](https://portal.azure.com/) eller PowerShell för att skapa en ExpressRoute VPN-gateway som är ansluten till det virtuella nätverket.
  - Om du använder Azure Portal lägger du till en ny **Virtual Network Gateway** och väljer sedan **ExpressRoute** som typ av gateway.
  - Om du använder PowerShell måste du först hämta och använda den senaste [Azure PowerShell SDK: n](https://azure.microsoft.com/downloads/). 
 
Följande kommandon skapar en ExpressRoute-Gateway. De texter som föregås av en _$_ är användardefinierade variabler som ska uppdateras med din speciella information.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

De enda Gateway-SKU: er som stöds för SAP HANA på Azure (stora instanser) är **UltraPerformance**.

## <a name="link-virtual-networks"></a>Länka virtuella nätverk

Det virtuella Azure-nätverket har nu en ExpressRoute-Gateway. Använd auktoriseringsinformationen från Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA – stora instanser ExpressRoute-kretsen. Du kan ansluta med hjälp av Azure Portal eller PowerShell. PowerShell-instruktionerna är följande. 

Kör följande kommandon för varje ExpressRoute-Gateway genom att använda en annan AuthGUID för varje anslutning. De första två posterna som visas i följande skript kommer från den information som tillhandahålls av Microsoft. Dessutom är AuthGUID särskilt för varje virtuellt nätverk och dess Gateway. Om du vill lägga till ytterligare ett virtuellt Azure-nätverk måste du skaffa en annan AuthID för din ExpressRoute-krets som ansluter HANA-stora instanser till Azure från Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Den sista parametern i kommandot New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** är en ny parameter som aktiverar ExpressRoute snabb sökväg. En funktion som minskar nätverks fördröjningen mellan dina HANA stora instans enheter och virtuella Azure-datorer. Funktionen lades till i maj 2019. Mer information hittar du i artikeln [SAP HANA (stora instanser) nätverks arkitektur](./hana-network-architecture.md). Kontrol lera att du kör den senaste versionen av PowerShell-cmdletar innan du kör kommandona.

Om du vill ansluta gatewayen till mer än en ExpressRoute-krets som är associerad med din prenumeration kan du behöva köra det här steget mer än en gång. Till exempel kommer du förmodligen att ansluta samma virtuella nätverksgateway till ExpressRoute-kretsen som ansluter det virtuella nätverket till ditt lokala nätverk.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Använda ExpressRoute snabb sökväg till befintliga HANA-ExpressRoute-kretsar i stora instanser
Dokumentationen så att du kan ansluta en ny ExpressRoute-krets som har skapats med en stor distribution av HANA-stor instans till en Azure ExpressRoute-Gateway för en av dina virtuella Azure-nätverk. Men många kunder har redan sina ExpressRoute-kretsar och har redan anslutit sina virtuella nätverk till HANA stora instanser. Eftersom den nya ExpressRoute-snabba sökvägen minskar nätverks fördröjningen, rekommenderar vi att du tillämpar ändringen för att använda den här funktionen. Kommandona för att ansluta en ny ExpreesRoute-krets och för att ändra en befintlig ExpressRoute-krets är desamma. Därför måste du köra den här sekvensen av PowerShell-kommandon för att ändra en befintlig krets som ska användas 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Det är viktigt att du lägger till den sista parametern som visas ovan för att aktivera ExpressRoute-funktionen för snabb sökväg


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Som du vill aktivera Global Reach för ett eller båda av de två scenarierna:

 - HANA-systemreplikering utan ytterligare proxyservrar eller brand väggar
 - Kopiera säkerhets kopior mellan HANA-stora instans enheter i två olika regioner för att utföra system kopior eller systemuppdateringar

Du måste tänka på följande:

- Du måste ange ett adress utrymmes intervall för ett/29-adressutrymme. Det adress intervallet får inte överlappa något av de andra adress utrymmes intervall som du använde så att du kopplar HANA-stora instanser till Azure och inte överlappar några av dina IP-adressintervall som du använde någon annan stans i Azure eller lokalt.
- Det finns en begränsning i ASN: er (autonomt system nummer) som kan användas för att annonsera dina lokala vägar till HANA-stora instanser. Ditt lokala nätverk får inte annonsera några vägar med privata ASN: er i intervallet 65000 – 65020 eller 65515. 
- För scenariot med att ansluta lokal direkt åtkomst till HANA-stora instanser måste du beräkna en avgift för kretsen som ansluter dig till Azure. För priser kontrollerar du priserna för [Global Reach-tillägg](https://azure.microsoft.com/pricing/details/expressroute/).

Om du vill hämta ett eller båda scenarier som tillämpas på din distribution öppnar du ett support meddelande med Azure enligt beskrivningen i [öppna en supportbegäran för många Hana-instanser](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Data som behövs och nyckelord som du behöver använda för att Microsoft ska kunna dirigera och köra på din begäran, ser ut så här:

- Tjänst: SAP HANA stor instans
- Problem typ: konfiguration och konfiguration
- Problem under typ: mitt problem visas inte ovan
- Subject ' ändra mitt nätverk – Lägg till Global Reach '
- Information: ' Lägg till Global Reach till HANA-stor instans till HANA-stor instans-klient eller Lägg till Global Reach till en stor instans av en stor instans på plats till HANA.
- Mer information för den stora data instansen HANA till HANA stor instans-väska: du måste definiera de **två Azure-regioner** där de två klienterna ska ansluta **och** du måste skicka in **/29 IP-adressintervall**
- Ytterligare information för den lokala instansen av den lokala instansen av klient väska: du måste definiera den **Azure-region** där den virtuella Hana-instansen är distribuerad som du vill ansluta till direkt. Du måste också ange GUID för **autentisering** och **krets-ID** som du fick när du etablerade ExpressRoute-kretsen mellan lokalt och Azure. Du måste dessutom ge ditt **ASN** ett namn. Den sista slut produkten är ett **/29 IP-adressintervall** för ExpressRoute-Global Reach.

> [!NOTE]
> Om båda fallen ska hanteras måste du ange två olika/29 IP-adressintervall som inte överlappar med andra IP-adressintervall som används hittills. 




## <a name="next-steps"></a>Nästa steg

- [Ytterligare nätverks krav för HLI](hana-additional-network-requirements.md)

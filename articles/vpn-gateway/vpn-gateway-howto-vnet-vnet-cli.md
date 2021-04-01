---
title: 'Ansluta ett VNet till ett VNet med en VNet-till-VNet-anslutning: Azure CLI'
description: Anslut virtuella nätverk tillsammans med en VNet-till-VNet-anslutning och Azure CLI.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660074"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Konfigurera en VPN-gatewayanslutning mellan virtuella nätverk med hjälp av Azure CLI

Den här artikeln hjälper dig ansluta virtuella nätverk via VNet-till-VNet-anslutningstypen. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. När du ansluter virtuella nätverk från olika prenumerationer, behöver inte prenumerationerna vara associerade med samma Active Directory-klient.

Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen och användning av Azure CLI. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>Om att ansluta virtuella nätverk

Det finns flera sätt att ansluta till virtuella nätverk. I avsnitten nedan beskrivs olika sätt att ansluta till virtuella nätverk.

### <a name="vnet-to-vnet"></a>VNet-till-VNet

Att konfigurera en anslutning mellan virtuella nätverk är ett bra sätt att enkelt ansluta virtuella nätverk. Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk med VNet-till-VNet-anslutningstypen på nästan samma sätt som du skapar en plats-till-plats-IPsec-anslutning till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE, och båda fungerar på samma sätt vid kommunikation. Skillnaden mellan anslutningstyperna är hur den lokala nätverksgatewayen har konfigurerats. När du skapar en anslutning mellan virtuella nätverk ser du inte adressutrymmet för den lokala nätverksgatewayen. Den skapas och fylls i automatiskt. Om du uppdaterar adressutrymmet för ett virtuellt nätverk dirigerar det andra virtuella nätverket automatiskt till det uppdaterade adressutrymmet. Att skapa en anslutning mellan virtuella nätverk är normalt snabbare och enklare än att skapa en plats-till-plats-anslutning mellan virtuella nätverk.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Ansluta virtuella nätverk med stegen för plats-till-plats (IPsec)

Om du arbetar med komplicerade nätverkskonfigurationer kan du överväga att ansluta dina virtuella nätverk med hjälp av stegen för [plats-till-plats](vpn-gateway-howto-site-to-site-resource-manager-cli.md) i stället för stegen för VNet-till-VNet. När du använder stegen för plats-till-plats skapar och konfigurerar du de lokala nätverksgatewayerna manuellt. Den lokala nätverksgatewayen för varje virtuellt nätverk behandlar det andra virtuella nätverket som en lokal plats. På så sätt kan du ange ytterligare adressutrymme för den lokala nätverksgatewayen för att dirigera trafik. Om adressutrymmet för ett virtuellt nätverk ändras måste du uppdatera den motsvarande lokala nätverksgatewayen manuellt för att avspegla ändringen. Den uppdateras inte automatiskt.

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

I den här artikeln beskrivs två olika uppsättningar anvisningar för anslutningar mellan virtuella nätverk. En uppsättning steg för [virtuella nätverk som finns i samma prenumeration](#samesub) och en annan för [virtuella nätverk som finns i olika prenumerationer](#difsub). 

För den här övningen kan du kombinera konfigurationer eller bara välja den du vill arbeta med. Alla konfigurationer använder anslutningstypen VNet-till-VNet. Nätverkstrafik flödar mellan virtuella nätverk som är direkt anslutna till varandra. I den här övningen dirigeras inte trafik från TestVNet4 till TestVNet5.

* [VNets som finns i samma prenumeration:](#samesub) I stegen för den här konfigurationen används TestVNet1 och TestVNet4.

  ![Diagram som visar V net-till-V net-steg för V-nät som finns i samma prenumeration.](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [VNets som finns i olika prenumerationer:](#difsub) I stegen för den här konfigurationen används TestVNet1 och TestVNet5.

  ![v2v-diagram](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Så här ansluter du VNets som finns i samma prenumeration

### <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>Planera dina IP-adressintervall

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

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>Steg 1 - Ansluta till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>Steg 2 – Skapa och konfigurera TestVNet1

1. Skapa en resursgrupp.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Skapa TestVNet1 och undernäten för TestVNet1. I följande exempel skapas ett virtuellt nätverk med namnet ”TestVNet1” och ett undernät, ”FrontEnd”.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Skapa ytterligare ett adressutrymme för ett backend-undernät. Observera att i det här steget ska vi ange såväl adressutrymmet som vi skapade tidigare som ytterligare adressutrymme ett som vi vill lägga till. Detta beror på att kommandot [az network vnet update](/cli/azure/network/vnet) skriver över de tidigare inställningarna. Se till att ange alla adressprefixen när du använder det här kommandot.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Skapa backend-undernät.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Skapa gateway-undernätet. Observera att gateway-undernätet har namnet 'GatewaySubnet'. Namnet är obligatoriskt. I det här exemplet använder gateway-undernätet en /27. Även om det är möjligt att skapa ett gatewayundernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för det virtuella nätverket. Observera att AllocationMethod är Dynamic. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till gatewayen.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Skapa den virtuella nätverksgatewayen för TestVNet1. VNet-till-VNet-konfigurationer kräver VpnType RouteBased. Om du kör det här kommandot med parametern ”--no-wait” ser du ingen feedback eller utdata. Parametern '--no-wait' gör att gatewayen kan skapas i bakgrunden. Det innebär inte att VPN-gateway skapas i bakgrunden. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på den gateway-SKU som använder.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>Steg 3 – Skapa och konfigurera TestVNet4

1. Skapa en resursgrupp.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Skapa TestVNet4.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Skapa extra undernät för TestVNet4.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Skapa gateway-undernätet.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Begär en offentlig IP-adress.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Skapa den virtuella nätverksgatewayen TestVNet4.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>Steg 4 – Skapa anslutningarna

Nu har du två VNets med VPN-gatewayer. Nästa steg är att skapa VPN-gateway-anslutningar mellan de virtuella nätverksgatewayerna. Om du har använt exemplen ovan är VNet-gatewayerna i olika resursgrupper. När gatewayer finns i olika resursgrupper, måste du identifiera och ange resurs-ID för varje gateway när en anslutning upprättas. Om dina Vnets är i samma resursgrupp kan du använda den [andra uppsättningen anvisningar](#samerg) eftersom du inte behöver ange resurs-ID.

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>För att ansluta Vnets som finns i olika resursgrupper

1. Hämta resurs-ID för VNet1GW från utdata från följande kommando:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Hitta raden ”id”: i resultatet. Värden inom citattecken behövs för att skapa anslutningen i nästa avsnitt. Kopiera värdena till en textredigerare, till exempel Anteckningar, så att du enkelt kan klistra in dem när du skapar din anslutning.

   Exempel på utdata:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Kopiera värdena efter **”id”:** inom citattecken.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Hämta resurs-ID för VNet4GW och kopiera värdena till en textredigerare.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Skapa TestVNet1-till-TestVNet4-anslutningen. I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet4. Den finns en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Skapa TestVNet4-till-TestVNet1-anslutningen. Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet4 till TestVNet1. Kontrollera att de delade nycklarna matchar. Det tar några minuter att upprätta anslutningen.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Verifiera dina anslutningar. Se [Verifiera din anslutning](#verify).

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>För att ansluta Vnets som finns i samma resursgrupp

1. Skapa TestVNet1-till-TestVNet4-anslutningen. I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet4. Lägg märke att till resursgrupper är samma i exemplen. Du ser även en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln, men den delade nyckeln måste matcha för både anslutningar. Att skapa en anslutning kan ta en stund att slutföra.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Skapa TestVNet4-till-TestVNet1-anslutningen. Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet4 till TestVNet1. Kontrollera att de delade nycklarna matchar. Det tar några minuter att upprätta anslutningen.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Verifiera dina anslutningar. Se [Verifiera din anslutning](#verify).

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Ansluta VNets som finns i olika prenumerationer

I det här scenariot ansluter vi TestVNet1 och TestVNet5. VNets finns i olika prenumerationer. Prenumerationerna behöver inte vara associerade med samma Active Directory-klient. I stegen för den här konfigurationen lägger vi till ytterligare en VNet-till-VNet-anslutning för att kunna ansluta TestVNet1 till TestVNet5.

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>Steg 5 – Skapa och konfigurera TestVNet1

Dessa instruktioner bygger på stegen i föregående avsnitt. Du måste slutföra [steg 1](#Connect) och [steg 2](#TestVNet1) för att skapa och konfigurera TestVNet1 och VPN gateway för TestVNet1. I den här konfigurationen krävs det inte att du skapar TestVNet4 i enlighet med föregående avsnitt, men om du gör det går det ändå att använda de här instruktionerna. När du har slutfört steg 1 och steg 2 fortsätter du med steg 6 (nedan).

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>Steg 6 – Kontrollera IP-adressintervaller

När du skapar ytterligare anslutningar är det viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, inte överlappar några av dina VNet-intervall eller lokala intervall för nätverksgatewayen. Använd följande VNet-värden för TestVNet5 i den här övningen:

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

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>Steg 7 – Skapa och konfigurera TestVNet5

Det här steget måste utföras i den nya prenumerationen, prenumeration 5. Den här delen kan utföras av administratören i en annan organisation som äger prenumerationen. Om du vill växla mellan prenumerationer använder du för att `az account list --all` lista de prenumerationer som är tillgängliga för ditt konto och använder `az account set --subscription <subscriptionID>` för att växla till den prenumeration som du vill använda.

1. Kontrollera att du är ansluten till prenumeration 5 och sedan skapa en resursgrupp.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Skapa TestVNet5.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Lägg till undernät.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Lägg till gateway-undernätet.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Begär en offentlig IP-adress.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. Skapa TestVNet5-gatewayen

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>Steg 8 – Skapa anslutningarna

Vi har delat upp steget i två CLI-sessioner som kallas för **[Prenumeration 1]** och **[Prenumeration 5]** eftersom gatewayerna finns i olika prenumerationer. Om du vill växla mellan prenumerationer använder du för att `az account list --all` lista de prenumerationer som är tillgängliga för ditt konto och använder `az account set --subscription <subscriptionID>` för att växla till den prenumeration som du vill använda.

1. **[Prenumeration 1]** Logga in och anslut till Prenumeration 1. Kör följande kommando för att hämta namn och ID för gatewayen från utdata:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Kopiera utdata för ”id:”. Skicka ID och namn på den VNet-gatewayen (VNet1GW) till prenumeration 5-administratören via e-post eller någon annan metod.

   Exempel på utdata:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[Prenumeration 5]** Logga in och anslut till Prenumeration 5. Kör följande kommando för att hämta namn och ID för gatewayen från utdata:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Kopiera utdata för ”id:”. Skicka ID och namn på den VNet-gatewayen (VNet5GW) till prenumeration 1-administratören via e-post eller någon annan metod.

3. **[Prenumeration 1]** I det här steget ska du skapa anslutningen från TestVNet1 till TestVNet5. Du kan använda egna värden för den delade nyckeln, men den delade nyckeln måste matcha för både anslutningar. Att skapa en anslutning kan ta en stund att slutföra. Kontrollera att du ansluter till Prenumeration 1.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[Prenumeration 5]** Det här steget liknar det ovan, förutom att du skapar anslutningen från TestVNet5 till TestVNet1. Kontrollera att de delade nycklarna matchar och att du ansluter till Prenumeration 5.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>Kontrollera anslutningarna
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Dokumentationen för virtuella datorer](../index.yml).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
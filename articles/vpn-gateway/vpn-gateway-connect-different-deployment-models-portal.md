---
title: 'Ansluta klassiska virtuella nätverk till Azure Resource Manager virtuella nätverk: Portal | Microsoft Docs'
description: Steg för att ansluta klassiska virtuella nätverk till Resource Manager-virtuella nätverk med hjälp av VPN Gateway och portalen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376810"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Anslut virtuella nätverk från olika distributions modeller med hjälp av portalen

Den här artikeln visar hur du ansluter klassiska virtuella nätverk till Resource Manager-virtuella nätverk så att resurserna som finns i de separata distributions modellerna kan kommunicera med varandra. Stegen i den här artikeln använder främst Azure Portal, men du kan också skapa den här konfigurationen med hjälp av PowerShell genom att välja artikeln i den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Att ansluta ett klassiskt VNet till ett virtuellt nätverk i Resource Manager liknar att ansluta ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge den gateway som de har kon figurer ATS med är dynamisk eller Route-baserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa en, kan du i stället överväga att ansluta din virtuella nätverk med VNet-peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Innan du börjar



* De här stegen förutsätter att båda virtuella nätverk redan har skapats. Om du använder den här artikeln som en övning och inte har virtuella nätverk finns det länkar i stegen för att hjälpa dig att skapa dem.
* Kontrol lera att adress intervallen för virtuella nätverk inte överlappar varandra eller överlappar något av intervallen för andra anslutningar som gatewayerna kan vara anslutna till.
* Installera de senaste PowerShell-cmdletarna för både Resource Manager och Service Management (klassisk). I den här artikeln använder vi både Azure Portal och PowerShell. PowerShell krävs för att skapa anslutningen från det klassiska VNet till Resource Manager VNet. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiskt VNet**

VNet-namn = ClassicVNet <br>
Adress utrymme = 10.0.0.0/24 <br>
Under näts namn = undernät-1 <br>
Adress intervall för undernät = 10.0.0.0/27 <br>
Prenumeration = den prenumeration du vill använda <br>
Resurs grupp = ClassicRG <br>
Plats = västra USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokal plats = RMVNetLocal <br>

**Resource Manager VNet**

VNet-namn = RMVNet <br>
Adress utrymme = 192.168.0.0/16 <br>
Resurs grupp = RG1 <br>
Plats = USA, östra <br>
Under näts namn = undernät-1 <br>
Adress intervall = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Namn på virtuell nätverksgateway = RMGateway <br>
Gateway-typ = VPN <br>
VPN-typ = Route-based <br>
SKU = VpnGw1 <br>
Plats = USA, östra <br>
Virtuellt nätverk = RMVNet <br> (koppla VPN-gatewayen till det här virtuella nätverket) Första IP-konfiguration = rmgwpip <br> (offentlig IP-adress för Gateway) Lokal nätverksgateway = ClassicVNetLocal <br>
Anslutnings namn = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Översikt över anslutning

För den här konfigurationen skapar du en VPN gateway-anslutning via en IPsec/IKE VPN-tunnel mellan de virtuella nätverken. Se till att inget av dina VNet-intervall överlappar varandra, eller med något av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur virtuella nätverk och lokala platser definieras:

| Virtual Network | Adressutrymme | Region | Ansluter till den lokala nätverks platsen |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA, västra | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Avsnitt 1 – Konfigurera de klassiska VNet-inställningarna

I det här avsnittet skapar du det klassiska VNet, det lokala nätverket (lokal plats) och den virtuella Nätverksgatewayen. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna, eller Använd [exempel](#values) värden.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> skapa ett klassiskt VNet

Om du inte har ett klassiskt VNet och kör dessa steg som en övning, kan du skapa ett VNet med hjälp av [den här artikeln](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) och [exemplet](#values) inställnings värden ovan.

Om du redan har ett VNet med en VPN-gateway kontrollerar du att gatewayen är dynamisk. Om den är statisk måste du först ta bort VPN-gatewayen innan du fortsätter att [Konfigurera den lokala platsen](#local).

1. Öppna [Azure Portal](https://ms.portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **+ skapa en resurs** för att öppna sidan nytt.
3. I fältet Sök i Marketplace skriver du Virtual Network. Om du i stället väljer nätverk-> Virtual Network, får du inte alternativet att skapa ett klassiskt VNet.
4. Leta upp Virtual Network från den returnerade listan och klicka på den för att öppna sidan Virtual Network. 
5. På sidan virtuellt nätverk väljer du klassisk för att skapa ett klassiskt VNet. Om du använder standardvärdet så kommer du att få ett Resource Manager VNet i stället.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> Konfigurera den lokala platsen

1. Navigera till **alla resurser** och leta upp **ClassicVNet** i listan.
2. Klicka på **Gateway** i avsnittet **Inställningar** på menyn och klicka sedan på banderollen för att skapa en gateway.
  ![Konfigurera en VPN gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurera en VPN gateway")
3. På sidan **ny VPN-anslutning** väljer du **plats-till-plats** för **Anslutnings typ**.
4. För **lokal plats** klickar du på **Konfigurera nödvändiga inställningar**. Då öppnas sidan **lokal webbplats** .
5. På sidan **lokal plats** skapar du ett namn som refererar till Resource Manager VNet. Till exempel "RMVNetLocal".
6. Om VPN-gatewayen för Resource Manager VNet redan har en offentlig IP-adress, använder du värdet för fältet **VPN gateway IP-adress** . Om du utför dessa steg som en övning, eller ännu inte har en virtuell nätverksgateway för ditt Resource Manager VNet, kan du skapa en IP-adress för plats hållaren. Kontrol lera att IP-adressen för plats hållaren har ett giltigt format. Senare ersätter du IP-adressen för plats hållaren med den offentliga IP-adressen för den virtuella Nätverksgatewayen i Resource Manager.
7. För **klient adress utrymme** använder du [värdena](#connectoverview) för det virtuella nätverkets IP-adressutrymme för Resource Manager VNet. Den här inställningen används för att ange de adress utrymmen som ska vidarebefordras till det virtuella Resource Manager-nätverket. I exemplet använder vi 192.168.0.0/16 adress intervallet för RMVNet.
8. Spara värdena och gå tillbaka till sidan **ny VPN-anslutning** genom att klicka på **OK** .

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. skapa den virtuella Nätverksgatewayen

1. Markera kryss rutan **skapa Gateway omedelbart** på sidan **ny VPN-anslutning** .
2. Klicka på **Valfri gatewaykonfiguration** för att öppna sidan **Gatewaykonfiguration**.

   ![Sidan konfiguration av öppen Gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Sidan konfiguration av öppen Gateway")
3. Klicka på **undernät – konfigurera nödvändiga inställningar** för att öppna sidan **Lägg till undernät** . **Namnet** har redan kon figurer ATS med det obligatoriska värdet: **GatewaySubnet**.
4. **Adress intervallet** avser intervallet för gateway-undernätet. Även om du kan skapa ett Gateway-undernät med ett/29-adressintervall (3 adresser) rekommenderar vi att du skapar ett Gateway-undernät som innehåller fler IP-adresser. Detta kommer att hantera framtida konfigurationer som kan kräva fler tillgängliga IP-adresser. Använd om möjligt/27 eller/28. Om du använder dessa steg som en övning kan du referera till [exempel värden](#values). I det här exemplet använder vi "10.0.0.32/28". Klicka på **OK** för att skapa Gateway-undernätet.
5. På sidan **gateway-konfiguration** refererar **storleken** till gateway-SKU: n. Välj Gateway-SKU: n för din VPN-gateway.
6. Kontrol lera att **Dirigerings typen** är **dynamisk** och klicka sedan på **OK** för att återgå till sidan **ny VPN-anslutning** .
7. På sidan **ny VPN-anslutning** klickar du på **OK** för att börja skapa din VPN-gateway. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. kopiera den virtuella nätverks-gatewayens offentliga IP-adress

När den virtuella Nätverksgatewayen har skapats kan du Visa gatewayens IP-adress. 

1. Navigera till ditt klassiska VNet och klicka på **Översikt**.
2. Klicka på **VPN-anslutningar** för att öppna sidan VPN-anslutningar. På sidan VPN-anslutningar kan du Visa den offentliga IP-adressen. Detta är den offentliga IP-adress som tilldelats till din virtuella nätverksgateway. Anteckna IP-adressen. Du använder det i senare steg när du arbetar med konfigurations inställningarna för lokal nätverksgateway i Resource Manager. 
3. Du kan visa statusen för dina gateway-anslutningar. Observera att den lokala nätverks plats som du har skapat visas som "anslutning". Status ändras när du har skapat dina anslutningar. Du kan stänga den här sidan när du är klar med att visa statusen.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Avsnitt 2 – Konfigurera inställningar för VNet för Resource Manager

I det här avsnittet skapar du den virtuella Nätverksgatewayen och den lokala Nätverksgatewayen för ditt Resource Manager VNet. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna, eller Använd [exempel](#values) värden.

### <a name="1-create-a-virtual-network"></a>1. skapa ett virtuellt nätverk

**Exempel värden:**

* VNet-namn = RMVNet <br>
* Adress utrymme = 192.168.0.0/16 <br>
* Resurs grupp = RG1 <br>
* Plats = USA, östra <br>
* Under näts namn = undernät-1 <br>
* Adress intervall = 192.168.1.0/24 <br>

Om du inte har ett Resource Manager VNet och kör dessa steg som en övning, skapar du ett virtuellt nätverk med stegen i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md)med hjälp av exempel värden.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. skapa en virtuell nätverksgateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Exempel värden:**

* Namn på virtuell nätverksgateway = RMGateway <br>
* Gateway-typ = VPN <br>
* VPN-typ = Route-based <br>
* SKU = VpnGw1 <br>
* Plats = USA, östra <br>
* Virtuellt nätverk = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Första IP-konfiguration = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. skapa en lokal nätverksgateway

**Exempel värden:** Lokal nätverksgateway = ClassicVNetLocal

| Virtual Network | Adressutrymme | Region | Ansluter till den lokala nätverks platsen |Offentlig IP-adress för gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA, västra | RMVNetLocal (192.168.0.0/16) |Den offentliga IP-adress som är tilldelad ClassicVNet-gatewayen|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |Den offentliga IP-adress som är tilldelad RMVNet-gatewayen.|

Den lokala Nätverksgatewayen anger adress intervallet och den offentliga IP-adress som är kopplad till det klassiska virtuella nätverket och den virtuella Nätverksgatewayen. Om du utför dessa steg som en övning, se exempel värden.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Avsnitt 3 – ändra de klassiska VNet-inställningarna för lokal plats

I det här avsnittet ersätter du IP-adressen för den plats hållare som du använde när du angett inställningarna för den lokala platsen, med IP-adressen till Resource Manager VPN gateway. I det här avsnittet används PowerShell-cmdlet: arna (SM).

1. I Azure Portal navigerar du till det klassiska virtuella nätverket.
2. Klicka på **Översikt** på sidan för ditt virtuella nätverk.
3. I avsnittet **VPN-anslutningar** klickar du på namnet på din lokala plats i bilden.

   ![VPN-anslutningar](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-anslutningar")
4. På sidan **plats-till-plats-VPN-anslutningar** klickar du på namnet på platsen.

   ![Plats namn](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Namn på lokal plats")
5. På sidan anslutning för den lokala platsen klickar du på namnet på den lokala platsen för att öppna sidan **lokal plats** .

   ![Öppen – lokal plats](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Öppna lokal plats")
6. På sidan **lokal plats** ersätter du IP- **adressen för VPN-gatewayen** med IP-adressen för Resource Manager-gatewayen.

   ![Gateway-IP-adress](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP-adress för gateway")
7. Klicka på **OK** för att uppdatera IP-adressen.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Avsnitt 4 – Skapa Resource Manager till klassisk anslutning

I de här stegen konfigurerar du anslutningen från Resource Manager VNet till det klassiska virtuella nätverket med hjälp av Azure Portal.

1. Leta upp den lokala Nätverksgatewayen i **alla resurser**. I vårt exempel är den lokala Nätverksgatewayen **ClassicVNetLocal**.
2. Klicka på **konfiguration** och kontrol lera att IP-värdet är VPN-gatewayen för det klassiska VNet. Uppdatera vid behov och klicka sedan på **Spara**. Stäng sidan.
3. I **alla resurser** klickar du på den lokala Nätverksgatewayen.
4. Klicka på **anslutningar** för att öppna sidan anslutningar.
5. På sidan **anslutningar** klickar **+** du för att lägga till en anslutning.
6. På sidan **Lägg till anslutning** namnger du anslutningen. Till exempel "RMtoClassic".
7. **Plats-till-plats** har redan valts på den här sidan.
8. Välj den virtuella nätverksgateway som du vill koppla till den här platsen.
9. Skapa en **delad nyckel**. Den här nyckeln används också i den anslutning som du skapar från det klassiska VNet till Resource Manager VNet. Du kan generera nyckeln eller skapa en. I vårt exempel använder vi "vi abc123", men du kan (och bör) använda något mer komplicerat.
10. Klicka på **OK** för att skapa anslutningen.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Avsnitt 5 – skapa klassiskt till Resource Manager-anslutning

I de här stegen konfigurerar du anslutningen från det klassiska VNet till Resource Manager VNet. De här stegen kräver PowerShell. Du kan inte skapa den här anslutningen i portalen. Kontrol lera att du har laddat ned och installerat både de klassiska PowerShell-cmdletarna (SM) och Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Anslut till ditt Azure-konto

Öppna PowerShell-konsolen med utökade rättigheter och logga in på ditt Azure-konto. När du har loggat in hämtas dina konto inställningar så att de är tillgängliga för Azure PowerShell. I följande cmdlet uppmanas du att ange inloggnings uppgifterna för ditt Azure-konto för distributions modellen för Resource Manager:

```powershell
Connect-AzAccount
```

Hämta en lista över dina Azure-prenumerationer.

```powershell
Get-AzSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Logga sedan in för att använda de klassiska PowerShell-cmdletarna (Service Management). Använd följande kommando för att lägga till ditt Azure-konto för den klassiska distributions modellen:

```powershell
Add-AzureAccount
```

Hämta en lista över dina prenumerationer. Det här steget kan vara nödvändigt när du lägger till tjänst hanterings-cmdletar, beroende på din Azure-modul.

```powershell
Get-AzureSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Visa fil värden för nätverks konfiguration

När du skapar ett VNet i Azure Portal, visas inte det fullständiga namnet som används i Azure i Azure Portal. Till exempel kan ett VNet som verkar ha namnet "ClassicVNet" i Azure Portal ha ett mycket längre namn i nätverks konfigurations filen. Namnet kan se ut ungefär så här: "Group ClassicRG ClassicVNet". I de här stegen laddar du ned nätverks konfigurations filen och visar värdena.

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en text redigerare och visa namnet på det klassiska VNet. Använd namnen i nätverks konfigurations filen när du kör dina PowerShell-cmdlets.

- VNet-namn visas som **VirtualNetworkSite namn =**
- Plats namn anges som **LocalNetworkSite namn =**

### <a name="3-create-the-connection"></a>3. skapa anslutningen

Ange den delade nyckeln och skapa anslutningen från det klassiska VNet-nätverket till Resource Manager VNet. Du kan inte ange den delade nyckeln med hjälp av portalen. Se till att du kör de här stegen när du är inloggad med den klassiska versionen av PowerShell-cmdletarna. Det gör du med hjälp av **Add-AzureAccount**. Annars kommer du inte att kunna ange "-AzureVNetGatewayKey".

- I det här exemplet är **-VNetName** namnet på det klassiska VNet som finns i nätverks konfigurations filen. 
- **LocalNetworkSiteName** är det namn som du har angett för den lokala platsen, som du hittar i din nätverks konfigurations fil.
- **SharedKey** är ett värde som du genererar och anger. I det här exemplet använde vi *vi abc123*, men du kan generera något mer komplicerat. Det viktiga är att värdet du anger här måste vara samma värde som du angav när du skapade Resource Manager till klassisk anslutning.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Avsnitt 6 – kontrol lera dina anslutningar

Du kan verifiera dina anslutningar med hjälp av Azure Portal eller PowerShell. När du verifierar kan du behöva vänta en minut eller två när anslutningen skapas. När en anslutning lyckas ändras anslutnings statusen från "ansluter" till "ansluten".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Så här kontrollerar du anslutningen från det klassiska VNet till ditt Resource Manager VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Så här kontrollerar du anslutningen från det virtuella Resource Manager-nätverket till det klassiska VNet

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

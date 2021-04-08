---
title: 'Skapa en anslutning mellan virtuella nätverk: klassisk: Azure Portal'
description: Anslut virtuella Azure-nätverk tillsammans med PowerShell och Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103228"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurera en VNet-till-VNet-anslutning (klassisk)

Den här artikeln hjälper dig att skapa en VPN gateway-anslutning mellan virtuella nätverk. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagram över klassisk VNet-till-VNet-arkitektur":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Stegen i den här artikeln gäller den klassiska distributions modellen och Azure Portal. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Form](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Anslut virtuella nätverk i olika distributions modeller](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) i den klassiska distributions modellen med hjälp av en VPN-gateway liknar att ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

Virtuella nätverk du ansluter kan finnas i olika prenumerationer och i olika regioner. Du kan kombinera VNet till VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagram över anslutningar":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Load Balancer-och Microsoft-teknik och kluster teknik från tredje part kan du konfigurera arbets belastning med hög tillgänglighet med GEO-redundans i flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regional program med flera nivåer med stark isolerings gränser**

  * Inom samma region kan du konfigurera flera nivåer med flera virtuella nätverk som är anslutna till varandra med stark isolering och säker kommunikation mellan olika nivåer.
* **Kors prenumeration, kommunikation mellan organisationer i Azure**

  * Om du har flera Azure-prenumerationer kan du ansluta arbets belastningar från olika prenumerationer på ett säkert sätt mellan virtuella nätverk.
  * För företag eller tjänste leverantörer kan du aktivera kommunikation mellan organisationer med säker VPN-teknik i Azure.

Mer information om VNet-till-VNet-anslutningar finns i [Att tänka på när du använder VNet-till-VNet](#faq) i slutet av den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Vi använder portalen för de flesta av stegen, men du måste använda PowerShell för att skapa anslutningarna mellan virtuella nätverk. Du kan inte skapa anslutningar med hjälp av Azure Portal eftersom det inte finns något sätt att ange den delade nyckeln i portalen. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planering

Det är viktigt att bestämma vilka intervall som ska användas för att konfigurera dina virtuella nätverk. För den här konfigurationen måste du se till att inga av dina VNet-intervall överlappar varandra, eller med något av de lokala nätverk som de ansluter till.

### <a name="vnets"></a><a name="vnet"></a>Virtuella nätverk

I den här övningen använder vi följande exempel värden:

**Värden för TestVNet1**

Namn: TestVNet1<br>
Adress utrymme: 10.11.0.0/16, 10.12.0.0/16 (valfritt)<br>
Under näts namn: standard<br>
Adressintervall för undernätet: 10.11.0.0/24<br>
Resurs grupp: ClassicRG<br>
Plats: USA, östra<br>
GatewaySubnet: 10.11.1.0/27

**Värden för TestVNet4**

Namn: TestVNet4<br>
Adress utrymme: 10.41.0.0/16, 10.42.0.0/16 (valfritt)<br>
Under näts namn: standard<br>
Adressintervall för undernätet: 10.41.0.0/24<br>
Resurs grupp: ClassicRG<br>
Plats: USA, västra<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Anslutningar

I följande tabell visas ett exempel på hur du kommer att ansluta din virtuella nätverk. Använd intervallen som en rikt linje. Skriv ned intervallen för dina virtuella nätverk. Du behöver den här informationen för senare steg.

I det här exemplet ansluter TestVNet1 till en lokal nätverks plats som du skapar med namnet "VNet4Local". Inställningarna för VNet4Local innehåller adressprefix för TestVNet4.
Den lokala platsen för varje VNet är det andra virtuella nätverket. Följande exempel värden används för vår konfiguration:

**Exempel**

| Virtual Network | Adressutrymme | Location | Ansluter till den lokala nätverks platsen |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Skapa virtuella nätverk

I det här steget skapar du två klassiska virtuella nätverk, TestVNet1 och TestVNet4. Om du använder den här artikeln som en övning använder du [exempel värden](#vnet).

**Tänk på följande inställningar när du skapar din virtuella nätverk:**

* **Virtual Network adress utrymmen** – på sidan Virtual Network adress utrymmen anger du det adress intervall som du vill använda för det virtuella nätverket. Detta är de dynamiska IP-adresser som ska tilldelas de virtuella datorerna och andra roll instanser som du distribuerar till det här virtuella nätverket.<br>De adress utrymmen du väljer får inte överlappa adress utrymmena för någon av de andra virtuella nätverk eller lokala platser som det virtuella nätverket ska ansluta till.

* **Plats** – när du skapar ett virtuellt nätverk kopplar du det till en Azure-plats (region). Om du till exempel vill att de virtuella datorerna som distribueras till ditt virtuella nätverk ska vara fysiskt placerade i västra USA väljer du den platsen. Du kan inte ändra platsen som är kopplad till det virtuella nätverket när du har skapat det.

**När du har skapat din virtuella nätverk kan du lägga till följande inställningar:**

* **Adress utrymme** – ytterligare adress utrymme krävs inte för den här konfigurationen, men du kan lägga till ytterligare adress utrymme när du har skapat VNet.

* **Undernät** – ytterligare undernät krävs inte för den här konfigurationen, men du kanske vill ha dina virtuella datorer i ett undernät som är skilt från dina andra roll instanser.

* **DNS-servrar** – ange DNS-serverns namn och IP-adress. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.

### <a name="to-create-a-classic-virtual-network"></a>Så här skapar du ett klassiskt virtuellt nätverk

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Konfigurera platser och gatewayer

Azure använder inställningarna som anges i varje lokal nätverks plats för att fastställa hur trafik ska dirigeras mellan virtuella nätverk. Varje VNet måste peka på respektive lokalt nätverk som du vill dirigera trafiken till. Du bestämmer det namn som du vill använda för att referera till varje lokal nätverks plats. Det är bäst att använda något beskrivande.

Till exempel ansluter TestVNet1 till en lokal nätverks plats som du skapar med namnet "VNet4Local". Inställningarna för VNet4Local innehåller adressprefix för TestVNet4.

Tänk på att den lokala platsen för varje VNet är det andra virtuella nätverket.

| Virtual Network | Adressutrymme | Location | Ansluter till den lokala nätverks platsen |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Konfigurera en plats

Den lokala platsen avser vanligtvis din lokala plats. Den innehåller IP-adressen för den VPN-enhet som du skapar en anslutning till och IP-adressintervallen som ska dirigeras via VPN-gatewayen till VPN-enheten.

1. Välj **plats-till-plats-anslutningar** under **Inställningar** på sidan för ditt VNet.
1. På sidan plats-till-plats-anslutningar väljer du **+ Lägg till**.
1. På sidan **Konfigurera en VPN-anslutning och gateway** för **Anslutnings typ** lämnar **du plats-till-plats-** vald.

   * **IP-adress till VPN-gateway:** Det här är den offentliga IP-adressen till VPN-enheten för ditt lokala nätverk. I den här övningen kan du ange en dummy-adress eftersom du ännu inte har IP-adressen för VPN-gatewayen för den andra platsen. Till exempel 5.4.3.2. När du senare har konfigurerat gatewayen för det andra virtuella nätverket kan du justera det här värdet.

   * **Klient adress utrymme:** Lista de IP-adressintervall som du vill dirigera till det andra virtuella nätverket via denna gateway. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som ditt virtuella nätverk ansluter till, eller överlappar adressintervallen för det virtuella nätverket.
1. Klicka inte på granska + skapa längst ned på sidan. I stället väljer du **Nästa: Gateway>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Konfigurera en virtuell nätverksgateway

1. På sidan **Gateway** väljer du följande värden:

   * **Storlek:** Det här är Gateway-SKU: n som du använder för att skapa din virtuella nätverksgateway. Klassiska VPN-gatewayer använder de gamla (äldre) gateway-SKU:erna. Mer information om de äldre gateway-SKU:erna finns i [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Arbeta med SKU:er för virtuella nätverksgatewayer (gamla SKU:er)). Du kan välja **standard** för den här övningen.

   * **Typ av dirigering:** Välj typ av routning för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt typ, eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om Dirigerings typ finns i [om VPN Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased. För den här konfigurationen väljer du **dynamisk**.

   * **Gateway-undernät:** Storleken på det Gateway-undernät som du anger beror på konfigurationen av VPN-gatewayen som du vill skapa. Även om det går att skapa ett gatewayundernät som är så litet som /29 rekommenderar vi att du använder /27 eller /28. Då skapas ett större undernät som innehåller fler adresser. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

1. Välj **Granska + skapa** längst ned på sidan för att kontrol lera inställningarna. Välj **skapa** för att distribuera. Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway, beroende på vilken Gateway-SKU som du har valt.
1. Du kan börja fortsätta till nästa steg medan den här gatewayen skapas.

### <a name="configure-testvnet4-settings"></a>Konfigurera TestVNet4-inställningar

Upprepa stegen för att [skapa en plats och gateway](#localsite) för att konfigurera TestVNet4, och ersätt värdena vid behov. Om du gör detta i övningen använder du [exempel värden](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Uppdatera lokala platser

När dina virtuella nätverksgateway har skapats för båda virtuella nätverk måste du justera egenskaperna för den lokala platsen för **VPN-gatewayens IP-adress**.

|VNet-namn|Ansluten webbplats|IP-adress för gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP-adress för VPN-gateway för TestVNet4|
|TestVNet4|VNet1Local|IP-adress för VPN-gateway för TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Del 1 – hämta den virtuella nätverks-gatewayens offentliga IP-adress

1. Navigera till ditt VNet genom att gå till **resurs gruppen** och välja det virtuella nätverket.
1. På sidan för ditt virtuella nätverk, i fönstret **Essentials** till höger, letar du upp **gatewayens IP-adress** och kopiera till Urklipp.

### <a name="part-2---modify-the-local-site-properties"></a>Del 2 – ändra egenskaperna för den lokala platsen

1. Under plats-till-plats-anslutningar väljer du anslutningen. Till exempel SiteVNet4.
1. På sidan **Egenskaper** för plats-till-plats-anslutningen väljer du **Redigera lokal plats**.
1. I fältet **IP-adress för VPN-gateway** klistrar du in IP-adressen för VPN-gatewayen som du kopierade i föregående avsnitt.
1. Välj **OK**.
1. Fältet uppdateras i systemet. Du kan också använda den här metoden för att lägga till ytterligare IP-adresser som du vill dirigera till den här platsen.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Del 3 – Upprepa stegen för det andra virtuella nätverket

Upprepa stegen för TestVNet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Hämta konfigurations värden

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Skapa anslutningar

När alla föregående steg har slutförts, kan du ställa in i förväg delade IPsec/IKE-nycklar och skapa anslutningen. Den här uppsättningen steg använder PowerShell. VNet-till-VNet-anslutningar för den klassiska distributions modellen kan inte konfigureras i Azure Portal eftersom det inte går att ange den delade nyckeln i portalen.

I exemplen ser du att den delade nyckeln är exakt samma. Den delade nyckeln måste alltid matcha. Se till att ersätta värdena i de här exemplen med de exakta namnen för dina virtuella nätverk-och lokala nätverks platser.

1. Skapa TestVNet1-till-TestVNet4-anslutningen. Se till att ändra värdena.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Skapa TestVNet4-till-TestVNet1-anslutningen.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Vänta tills anslutningarna har initierats. När gatewayen har initierats är statusen "lyckades".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Vanliga frågor och svar

Dessa överväganden gäller för klassiska virtuella nätverk och klassiska virtuella nätverks-gatewayer.

* De virtuella nätverken kan finnas i samma eller olika prenumerationer.
* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En moln tjänst eller en slut punkt för belastnings utjämning kan inte omfatta flera virtuella nätverk, även om de är anslutna till varandra.
* Att ansluta flera virtuella nätverk tillsammans kräver inte några VPN-enheter.
* VNet-till-VNet stöder anslutning av virtuella Azure-nätverk. Den stöder inte anslutning av virtuella datorer eller moln tjänster som inte har distribuerats till ett virtuellt nätverk.
* VNet-till-VNet kräver dynamiska routnings-gatewayer. Azures statiska RRAS-gatewayer stöds inte.
* Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser. Det finns högst 10 VPN-tunnlar för en VPN-gateway för virtuellt nätverk som ansluter till antingen andra virtuella nätverk eller lokala platser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adress utrymmen innebär att virtuella nätverk skapas eller att konfiguration av netcfg-filer inte kan köras.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* Alla VPN-tunnlar för VNet, inklusive P2S VPN, delar den tillgängliga bandbredden för VPN-gatewayen och samma VPN-gateway drift tid för service avtal i Azure.
* VNet-till-VNet-trafik överförs över Azure-stamnätet.

## <a name="next-steps"></a>Nästa steg

Verifiera dina anslutningar. Se [Verifiera en VPN gateway anslutning](vpn-gateway-verify-connection-resource-manager.md).

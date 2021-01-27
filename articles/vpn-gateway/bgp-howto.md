---
title: 'Konfigurera BGP för VPN Gateway: Portal'
titleSuffix: Azure VPN Gateway
description: Den här artikeln vägleder dig genom stegen för att konfigurera BGP med Azure VPN Gateway med hjälp av PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: db19b1ae017fa7981747b0e7b4c82e97efc61ed3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878892"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Så här konfigurerar du BGP på Azure VPN-gatewayer

Den här artikeln vägleder dig genom stegen för att aktivera BGP på en plats-till-plats-VPN-anslutning (S2S) och en VNet-till-VNet-anslutning med hjälp av Azure Portal.

## <a name="about-bgp"></a><a name="about"></a>Om BGP

BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. BGP möjliggör Azure VPN-gatewayer och dina lokala VPN-enheter, som kallas BGP-peerer eller grannar, till Exchange-vägar som informerar båda gatewayerna om tillgänglighet och tillgänglighet för dessa prefix för att gå igenom de gatewayer eller routrar som ingår. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers.

Mer information om fördelarna med BGP och att förstå de tekniska kraven och överväganden för att använda BGP finns i [Översikt över BGP med Azure VPN-gatewayer](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Komma igång

Varje del av den här artikeln hjälper dig att bilda ett grundläggande Bygg block för att aktivera BGP i din nätverks anslutning. Om du har slutfört alla tre delarna skapar du topologin som visas i diagram 1.

**Diagram 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram över nätverks arkitektur och inställningar" border="false":::

Du kan kombinera delar tillsammans för att bygga ett mer komplext, flera hopp, överförings nätverk som uppfyller dina behov.

### <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Del 1: Konfigurera BGP på den virtuella Nätverksgatewayen

I det här avsnittet kan du skapa och konfigurera ett virtuellt nätverk, skapa och konfigurera en virtuell nätverksgateway med BGP-parametrar och hämta IP-adressen för Azure BGP-peer. Diagram 2 visar de konfigurations inställningar som ska användas när du arbetar med stegen i det här avsnittet.

**Diagram 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagram som visar inställningar för virtuell nätverksgateway" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. skapa och konfigurera TestVNet1

I det här steget skapar och konfigurerar du TestVNet1. Använd stegen i [självstudien skapa en gateway](./tutorial-create-gateway-portal.md) för att skapa och konfigurera ditt virtuella Azure-nätverk och VPN-gateway. Använd referens inställningarna i skärm bilderna nedan.

* Virtuellt nätverk:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="TestVNet1 med motsvarande adressprefix":::

* Undernät:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1-undernät":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Skapa VPN-gatewayen för TestVNet1 med BGP-parametrar

I det här steget skapar du en VPN-gateway med motsvarande BGP-parametrar.

1. I Azure Portal navigerar du till **Virtual Network Gateway** -resursen från Marketplace och väljer **skapa**.

1. Fyll i de parametrar som visas nedan:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Skapa VNG1":::

1. Konfigurera följande inställningar i avsnittet markerat **Configure BGP** på sidan:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Konfigurera BGP":::

   * Välj **Configure BGP**  -  **Enabled** för att Visa BGP-konfigurations avsnittet.

   * Fyll i ditt ASN (autonoma system nummer).

   * Fältet **Azure APIPA BGP IP-adress** är valfritt. Om dina lokala VPN-enheter använder APIPA-adressen för BGP måste du välja en adress från det reserverade adress intervallet för Azure-reserverade APIPA för VPN, som kommer från **169.254.21.0** till **169.254.22.255**. I det här exemplet används 169.254.21.11.

   * Om du skapar en aktiv-aktiv VPN-gateway visar BGP-avsnittet ytterligare en **anpassad Azure APIPA BGP-IP-adress**. Ange en annan adress än det tillåtna APIPA-intervallet (**169.254.21.0** till **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Som standard tilldelar Azure en privat IP-adress från GatewaySubnet-prefixet automatiskt som Azure BGP IP-adress på Azure VPN-gatewayen. Den anpassade Azure APIPA BGP-adressen krävs när dina lokala VPN-enheter använder en APIPA-adress (169.254.0.1 till 169.254.255.254) som BGP IP. Azure VPN Gateway väljer den anpassade APIPA-adressen om motsvarande lokala nätverks-gateway-resurs (lokalt nätverk) har en APIPA-adress som BGP-peer-IP. Om den lokala Nätverksgatewayen använder en vanlig IP-adress (inte APIPA) kommer Azure VPN Gateway att återgå till den privata IP-adressen från GatewaySubnet-intervallet.
   >
   > * APIPA BGP-adresserna får inte överlappa mellan de lokala VPN-enheterna och alla anslutna Azure VPN-gatewayer.
   >

1. Välj **Granska + skapa** för att köra verifieringen. När verifieringen har godkänts väljer du **skapa** för att distribuera VPN-gatewayen. Det kan ta upp till 45 minuter för en gateway att skapa och distribuera fullständigt. Du kan se distributions status på översikts sidan för din gateway.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Hämta IP-adresserna för Azure BGP-peer

När gatewayen har skapats kan du hämta IP-adresser för BGP-peer på Azure VPN-gatewayen. De här adresserna behövs för att konfigurera dina lokala VPN-enheter för att upprätta BGP-sessioner med Azure VPN-gatewayen.

1. Navigera till resursen för den virtuella Nätverksgatewayen och välj **konfigurations** sidan för att Visa BGP-konfigurationsinformation som visas på följande skärm bild. På den här sidan kan du Visa all konfigurations information för BGP på din Azure VPN-Gateway: ASN, offentlig IP-adress och motsvarande BGP-peer-IP-adresser på Azure-sidan (standard och APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP-Gateway":::

1. På sidan **konfiguration** kan du göra följande konfigurations ändringar:

   * Du kan uppdatera ASN-eller APIPA BGP-IP-adressen om det behövs.
   * Om du har en aktiv-aktiv VPN-gateway visas den offentliga IP-adressen, standard-och APIPA BGP IP-adresserna för den andra Azure VPN gateway-instansen i den här sidan.

1. Om du har gjort några ändringar väljer du **Spara** för att genomföra ändringarna i Azure VPN-gatewayen.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Del 2: Konfigurera BGP i S2S-anslutningar mellan platser

Om du vill upprätta en anslutning mellan olika platser måste du skapa en *lokal nätverksgateway* som representerar din lokala VPN-enhet och en *anslutning* för att ansluta VPN-gatewayen till den lokala Nätverksgatewayen enligt beskrivningen i [skapa plats-till-plats-anslutning](./tutorial-site-to-site-portal.md). Den här artikeln innehåller ytterligare egenskaper som krävs för att ange BGP-konfigurationsparametrar.

**Diagram 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagram över IPsec" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. Konfigurera BGP på den lokala Nätverksgatewayen

I det här steget konfigurerar du BGP på den lokala Nätverksgatewayen. Använd följande skärm bild som exempel. Skärm bilden visar lokal nätverksgateway (site5) med de parametrar som anges i diagram 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Konfigurera BGP för den lokala Nätverksgatewayen":::

#### <a name="important-configuration-considerations"></a>Viktiga konfigurations överväganden

* ASN och BGP-peer-IP-adressen måste matcha din lokala VPN-routers konfiguration.
* Du kan bara lämna **adress utrymmet** tomt om du använder BGP för att ansluta till nätverket. Azure VPN gateway kommer internt att lägga till en väg för din BGP-peer-IP-adress till motsvarande IPsec-tunnel. Om du **inte** använder BGP mellan Azure VPN-gatewayen och det här specifika nätverket, **måste** du ange en lista över giltiga adressprefix för **adress utrymmet**.
* Du kan också använda en **APIPA-IP-adress** (169.254. x. x) som lokal IP-adress för BGP-peer vid behov. Men du måste också ange en IP-adress för APIPA enligt beskrivningen ovan i den här artikeln för din Azure VPN-gateway, annars kan inte BGP-sessionen upprätta för den här anslutningen.
* Du kan ange BGP-konfigurationsinformation när den lokala Nätverksgatewayen skapas, eller så kan du lägga till eller ändra BGP-konfigurationen på sidan **konfiguration** i den lokala nätverks-Gateway-resursen.

**Exempel**

I det här exemplet används en APIPA-adress (169.254.100.1) som lokal IP-adress för BGP-peer:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Lokal nätverksgateway APIPA och BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. Konfigurera en S2S-anslutning med BGP aktiverat

I det här steget skapar du en ny anslutning med BGP aktiverat. Om du redan har en anslutning och du vill aktivera BGP kan du [Uppdatera en befintlig anslutning](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Så här skapar du en anslutning med BGP aktiverat

Om du vill skapa en ny anslutning med BGP aktiverat, fyller du i värdena på sidan **Lägg till anslutning** och kontrollerar sedan alternativet **Aktivera BGP** för att aktivera BGP för den här anslutningen. Välj **OK** för att skapa anslutningen.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="IPsec-anslutning mellan platser med BGP":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Så här uppdaterar du en befintlig anslutning

Om du vill ändra BGP-alternativet för en anslutning navigerar du till sidan **konfiguration** i anslutnings resursen och växlar sedan **BGP** -alternativet som marker ATS i följande exempel. Välj **Spara** för att spara ändringarna.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Uppdatera BGP för en anslutning":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Del 3: Konfigurera BGP på VNet-till-VNet-anslutningar

Stegen för att aktivera eller inaktivera BGP i en VNet-till-VNet-anslutning är samma som för S2S-stegen i [del 2](#crosspremises). Du kan aktivera BGP när du skapar anslutningen eller uppdatera konfigurationen på en befintlig VNet-till-VNet-anslutning.

>[!NOTE] 
>En VNet-till-VNet-anslutning utan BGP begränsar kommunikationen till de två anslutna virtuella nätverk. Aktivera BGP för att tillåta överföring av transit funktioner till andra S2S-eller VNet-till-VNet-anslutningar för dessa två virtuella nätverk.
>

För sammanhang som hänvisar till **diagram 4**, om BGP skulle inaktive ras mellan TestVNet2 och TestVNet1, skulle TestVNet2 inte lära sig vägarna för det lokala nätverket, site5, och därför kunde inte kommunicera med plats 5. När du har aktiverat BGP, som du ser i diagrammet 4, kommer alla tre nätverken att kunna kommunicera via IPsec-och VNet-till-VNet-anslutningarna.

**Diagram 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram över hela nätverket" border="false":::

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md) för anvisningar.
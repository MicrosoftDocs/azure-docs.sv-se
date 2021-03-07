---
title: 'Självstudie: skapa ExpressRoute-anslutningar med Azure Virtual WAN'
description: I den här självstudien lär du dig att använda Azure Virtual WAN för att skapa ExpressRoute-anslutningar till Azure och lokala miljöer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 080136b8fc25b08a6b96464f0a61115a4bb2f3f8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426626"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Självstudie: skapa en ExpressRoute-Association med Azure Virtual WAN

Den här självstudien visar hur du använder Virtual WAN för att ansluta till dina resurser i Azure över en ExpressRoute-krets. Mer information om virtuella WAN-och virtuella WAN-resurser finns i [Översikt över virtuella WAN-nätverk](virtual-wan-about.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt WAN
> * Skapa en hubb och en gateway
> * Ansluta ett virtuellt nätverk till en hubb
> * Ansluta en krets till en hubb-Gateway
> * Testa anslutning
> * Ändra en gateway-storlek
> * Annonsera en standard väg

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa dina adress intervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* ExpressRoute-kretsen måste vara en Premium-eller standard-krets för att kunna ansluta till hubb-gatewayen.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Gå till den virtuella WAN-sidan. Klicka på **+Skapa en resurs** i portalen. Skriv det **virtuella WAN-nätverket** i sökrutan och välj RETUR.
2. Välj **virtuellt WAN** från resultaten. På den virtuella WAN-sidan klickar du på **skapa** för att öppna sidan Skapa WAN.
3. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   ![Skapa WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resursgrupp** – Skapa ny eller använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ** – Välj **standard**. Du kan inte skapa en ExpressRoute-Gateway med Basic SKU.
4. När du har fyllt i fälten väljer du **Granska + skapa**.
5. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Skapa en virtuell hubb och gateway

En virtuell hubb är ett virtuellt nätverk som skapas och används av virtuellt WAN. Den kan innehålla olika gatewayer, till exempel VPN-och ExpressRoute. I det här avsnittet ska du skapa en ExpressRoute-Gateway för din virtuella hubb. Du kan antingen skapa gatewayen när du [skapar en ny virtuell hubb](#newhub), eller så kan du skapa en gateway i ett [befintligt nav](#existinghub) genom att redigera den. 

ExpressRoute-gatewayer har tillhandahållits i enheter om 2 Gbit/s. 1 skalnings enhet = 2 Gbit/s med stöd för upp till 10 skalnings enheter = 20 Gbit/s. Det tar cirka 30 minuter för en virtuell hubb och gateway att helt skapa.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Så här skapar du en ny virtuell hubb och en gateway

Skapa en ny virtuell hubb. När du har skapat en hubb debiteras du för hubben, även om du inte kopplar några platser.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Så här skapar du en gateway i ett befintligt nav

Du kan också skapa en gateway i ett befintligt nav genom att redigera den.

1. Navigera till det virtuella hubb som du vill redigera och markera det.
2. Markera kryss rutan **Inkludera ExpressRoute Gateway** på sidan **Redigera virtuellt nav** .
3. Välj **Bekräfta** för att bekräfta ändringarna. Det tar cirka 30 minuter för nav-och nav resurserna att helt skapa.

   ![befintlig hubb](./media/virtual-wan-expressroute-portal/edithub.png "Redigera en hubb")

### <a name="to-view-a-gateway"></a>Så här visar du en gateway

När du har skapat en ExpressRoute-Gateway kan du Visa information om gatewayen. Navigera till hubben, Välj **ExpressRoute** och Visa gatewayen.

![Visa Gateway](./media/virtual-wan-expressroute-portal/viewgw.png "Visa Gateway")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Anslut ditt VNet till hubben

I det här avsnittet skapar du peering-anslutningen mellan hubben och ett VNet. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **virtuell nätverksanslutning**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket kan inte ha en befintlig virtuell nätverksgateway (varken VPN eller ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Anslut din krets till hubb-gatewayen

När gatewayen har skapats kan du ansluta en [ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) till den. ExpressRoute standard-eller Premium-kretsar som finns på ExpressRoute Global Reach-platser som stöds kan ansluta till en virtuell WAN-ExpressRoute gateway och ta del av alla funktioner för virtuell WAN-överföring (VPN-till-VPN, VPN och ExpressRoute-överföring). ExpressRoute standard-och Premium-kretsar som finns på platser som inte Global Reach kan ansluta till Azure-resurser, men kommer inte att kunna använda funktioner för virtuell WAN-överföring. ExpressRoute Local stöds med Azure virtuella WAN-hubbar så länge eker-virtuella nätverk som är anslutna till en virtuell WAN-hubb finns i samma region som den virtuella WAN-hubben.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Så här ansluter du kretsen till Hub Gateway

I portalen går du till sidan **virtuell hubb-> anslutning-> ExpressRoute** . Om du har åtkomst till en ExpressRoute-krets visas den krets som du vill använda i listan över kretsar. Om du inte ser några kretsar, men har fått en nyckel för auktorisering och peer-krets, kan du lösa in och ansluta en krets. Se [för att ansluta genom att lösa in en auktoriseringsregel](#authkey).

1. Välj kretsen.
2. Välj **Anslut krets (er)**.

   ![ansluta kretsar](./media/virtual-wan-expressroute-portal/cktconnect.png "ansluta kretsar")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Så här ansluter du genom att lösa in en auktoriseringsregel

Använd den autentiseringsregel och den krets-URI du angav för att ansluta.

1. På sidan ExpressRoute klickar du på **+ Lös in nyckel för autentisering**

   ![Skärm bild som visar ExpressRoute för en virtuell hubb med en upplösande auktoriseringskod vald.](./media/virtual-wan-expressroute-portal/redeem.png "löst")
2. Fyll i värdena på sidan Lös in Authorization Key.

   ![Lös in nyckel värden](./media/virtual-wan-expressroute-portal/redeemkey2.png "Lös in nyckel värden")
3. Välj **Lägg till** för att lägga till nyckeln.
4. Visa kretsen. En avbruten krets visar bara namnet (utan typ, Provider och annan information) eftersom den finns i en annan prenumeration än användaren.

## <a name="to-test-connectivity"></a>Så här testar du anslutningen

När krets anslutningen har upprättats visar hubbens anslutnings status "denna hubb", vilket innebär att anslutningen upprättas till Hub ExpressRoute-gatewayen. Vänta ungefär 5 minuter innan du testar anslutningen från en klient bakom din ExpressRoute-krets, till exempel en virtuell dator i det virtuella nätverk som du skapade tidigare.

Om du har platser som är anslutna till en virtuell WAN-gateway i samma hubb som ExpressRoute-gatewayen kan du använda dubbelriktad anslutning mellan VPN-och ExpressRoute-slutpunkter. Dynamisk routning (BGP) stöds. ASN för gatewayerna i hubben är fast och kan inte redige ras just nu.

## <a name="to-change-the-size-of-a-gateway"></a>Ändra storleken på en gateway

Om du vill ändra storleken på din ExpressRoute-Gateway letar du reda på ExpressRoute-gatewayen i hubben och väljer skalnings enheter i list rutan. Spara ändringen. Det tar cirka 30 minuter att uppdatera hubb-gatewayen.

![ändra Gateway-storlek](./media/virtual-wan-expressroute-portal/changescale.png "ändra Gateway-storlek")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Så här annonserar du standard vägen 0.0.0.0/0 till slut punkter

Om du vill att den virtuella Azure-hubben ska annonsera standard vägen 0.0.0.0/0 till dina ExpressRoute-slutpunkter måste du aktivera spridning av standard väg.

1. Välj din **krets->...-> Redigera anslutning**.

   ![Redigera anslutning](./media/virtual-wan-expressroute-portal/defaultroute1.png "Redigera anslutning")

2. Välj **Aktivera** för att sprida standard vägen.

   ![Sprid standard väg](./media/virtual-wan-expressroute-portal/defaultroute2.png "Sprid standard väg")

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat tar du bort dem. Några av de virtuella WAN-resurserna måste tas bort i en viss ordning på grund av beroenden. Borttagningen kan ta ungefär 30 minuter att slutföra.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om virtuellt WAN-nätverk i:

> [!div class="nextstepaction"]
> * [Vanliga frågor om Virtual WAN](virtual-wan-faq.md)

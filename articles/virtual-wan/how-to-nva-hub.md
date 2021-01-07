---
title: 'Azure Virtual WAN: skapa en virtuell nätverks installation (NVA) i hubben'
description: Lär dig hur du distribuerar en virtuell nätverks installation i den virtuella WAN-hubben.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 7a52d3971e4ec0cb8ec104a57ba7fb4057c23b72
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964651"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Så här skapar du en virtuell nätverks installation i Azure Virtual WAN Hub (för hands version)

Den här artikeln visar hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en **virtuell nätverks** installation (NVA) i Azure. Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om virtuellt WAN finns i [Vad är virtuellt WAN?](virtual-wan-about.md).

Stegen i den här artikeln hjälper dig att skapa en virtuell **Barracuda CLOUDGEN WAN** -nätverk i den virtuella WAN-hubben. För att slutföra den här övningen måste du ha en Barracuda-lokal enhet (CPE) och en licens för Barracuda CloudGen WAN-installationen som du distribuerar till hubben innan du börjar.

Distributions dokumentation för **Cisco SD-WAN** i Azure Virtual WAN finns i [Cisco Cloud bearbetar för flera moln](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701). Om du vill registrera ditt konto och få de Cisco-SD-WAN-licenser som krävs skickar du e-post till Cisco på följande e-post adress: vwan_public_preview@external.cisco.com


## <a name="prerequisites"></a>Krav

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Skaffa en licens för din Barracuda CloudGen WAN-Gateway. Mer information om hur du gör detta finns i [Barracuda CLOUDGEN WAN-dokumentationen](https://www.barracuda.com/products/cloudgenwan)

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Skapa ett virtuellt WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Skapa en hubb

En hubb är ett virtuellt nätverk som kan innehålla gatewayer för plats-till-plats-, ExpressRoute-, punkt-till-plats-eller nätverks funktioner för virtuella nätverk. När hubben har skapats debiteras du för hubben, även om du inte kopplar några platser. Om du väljer att skapa en VPN-gateway för plats-till-plats tar det 30 minuter att skapa VPN-gatewayen för plats-till-plats i den virtuella hubben. Till skillnad från plats-till-plats, ExpressRoute eller punkt-till-plats måste hubben skapas först innan du kan distribuera en virtuell nätverks installation till hubb-VNet.

1. Leta upp det virtuella WAN-nätverket som du har skapat. På sidan **virtuellt WAN** , under avsnittet **anslutning** , väljer du **hubbar**.
1. På sidan **hubbar** väljer du + ny hubb för att öppna sidan **Skapa virtuell hubb** .

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Grunder":::
1. Fyll i följande fält på fliken **grundläggande grunder** på sidan **Skapa virtuell hubb** :

   **Projektinformation**

   * Region (tidigare kallad plats)
   * Namn
   * NAV, privat adress utrymme. Det minsta adress utrymmet är/24 för att skapa en hubb, vilket innebär att allt mellan/25 och/32 genererar ett fel när det skapas. Azure Virtual WAN, som en hanterad tjänst av Microsoft, skapar lämpliga undernät i den virtuella hubben för de olika gatewayerna/tjänsterna. (Till exempel: virtuella nätverks enheter, VPN-gatewayer, ExpressRoute-gatewayer, användares VPN-och punkt-till-plats-gatewayer, brand vägg, routning osv.). Användaren behöver inte uttryckligen planera för under nätets adress utrymme för tjänsterna i det virtuella navet eftersom Microsoft gör detta som en del av tjänsten.
1. Välj **Granska + skapa** för att validera.
1. Välj **skapa** för att skapa hubben.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Skapa den virtuella nätverks enheten i hubben

I det här steget ska du skapa en virtuell nätverks installation i hubben. Proceduren för varje NVA är olika för varje NVA-partner produkt. I det här exemplet skapar vi en Barracuda CloudGen WAN-Gateway.

1. Leta upp den virtuella WAN-hubb som du skapade i föregående steg och öppna det.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Virtuell hubb":::
1. Leta upp panelen nätverks virtuella enheter och välj länken **skapa** .
1. På bladet **virtuell nätverks** installation väljer du **Barracuda CloudGen WAN** och väljer sedan knappen **skapa** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Välj NVA":::
1. Detta tar dig till Azure Marketplace-erbjudandet för Barracuda CloudGen WAN Gateway. Läs villkoren och välj sedan knappen **skapa** när du är klar.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA-grunder":::
1. På sidan **grundläggande** måste du ange följande information:

   * **Prenumeration** – Välj den prenumeration som du använde för att distribuera det virtuella WAN-nätverket och hubben.
   * **Resurs grupp** – Välj samma resurs grupp som du använde för att distribuera det virtuella WAN-nätverket och hubben.
   * **Region** – Välj samma region som den virtuella nav resursen finns i.
   * **Program namn** – Barracuda NextGen WAN är ett hanterat program. Välj ett namn som gör det enkelt att identifiera den här resursen, eftersom det här är vad den kommer att anropas när den visas i din prenumeration.
   * **Hanterad resurs grupp** – det här är namnet på den hanterade resurs grupp där Barracuda ska distribuera resurser som hanteras av dem. Namnet måste vara ifyllt för detta.
1. Välj **Nästa: CLOUDGEN WAN Gateway** -knapp.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN-Gateway":::
1. Ange följande information:

   * **Virtuell WAN-hubb** – den virtuella WAN-hubb som du vill distribuera NVA till.
   * **NVA-infrastruktur enheter** – ange antalet infrastruktur enheter för NVA som du vill distribuera NVA med. Välj den mängd bandbredds kapacitet som du vill tillhandahålla på alla de förgrenings platser som ska ansluta till det här navet via den här NVA.
   * **Token** – Barracuda kräver att du anger en autentiseringstoken här för att identifiera dig som en registrerad användare av produkten. Du måste skaffa detta från Barracuda.
1. Fortsätt genom att klicka på knappen **Granska och skapa** .
1. På den här sidan kommer du att uppmanas att godkänna villkoren i Co-Admins åtkomst avtal. Detta är standard med hanterade program där utgivaren kommer att ha åtkomst till vissa resurser i den här distributionen. Markera kryss rutan **Jag godkänner villkoren ovan** och välj sedan **skapa**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Anslut VNet till hubben

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns på sidan [Vad är virtuellt WAN?](virtual-wan-about.md)
* Mer information om NVA i en virtuell WAN-hubb finns i [om virtuell nätverks installation i den virtuella WAN-hubben (för hands version)](about-nva-hub.md).

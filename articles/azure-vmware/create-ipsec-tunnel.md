---
title: Skapa en IPSec-tunnel i Azure VMware-lösningen
description: Lär dig hur du upprättar en VPN-baserad (IPsec IKEv1 och IKEv2) plats-till-plats-tunnel till Azure VMware-lösningar.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027049"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Skapa en IPSec-tunnel i Azure VMware-lösningen

I den här artikeln går vi igenom stegen för att upprätta en VPN-anslutning (IPsec IKEv1 och IKEv2) från plats-till-plats-tunneln som avslutas i Microsoft Azure virtuella WAN-hubben. Hubben innehåller Azure VMware-lösningen ExpressRoute-gateway och plats-till-plats-VPN-gateway. Den ansluter en lokal VPN-enhet med en Azure VMware-lösnings slut punkt.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagram över VPN för plats-till-plats-tunnel." border="false":::

I så här kan du:
- Skapa en virtuell WAN-hubb och en VPN-gateway med en offentlig IP-adress som är kopplad till den. 
- Skapa en Azure ExpressRoute-gateway och upprätta en Azure VMware-lösnings slut punkt. 
- Aktivera en principbaserad VPN-installation på plats. 

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en offentlig IP-adress som avslutas på en lokal VPN-enhet.

## <a name="step-1-create-an-azure-virtual-wan"></a>Steg 1. Skapa ett virtuellt Azure-nätverk

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Steg 2. Skapa en virtuell WAN-hubb och gateway

>[!TIP]
>Du kan också [skapa en gateway i ett befintligt nav](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Välj det virtuella WAN som du skapade i föregående steg.

1. Välj **Skapa virtuell hubb**, ange de obligatoriska fälten och välj sedan **Nästa: webbplats till plats**. 

   Ange under nätet med ett `/24` (minst).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Skärm bild som visar sidan Skapa virtuellt nav.":::

4. Välj fliken **plats-till-plats** , definiera plats-till-plats-gatewayen genom att ställa in det sammanställda data flödet från List rutan för **Gateway-Scale-enheter** . 

   >[!TIP]
   >Skalnings enheterna finns i par för redundans, varje stödd 500 Mbit/s (en skalnings enhet = 500 Mbit/s). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Skärm bild som visar plats-till-plats-information.":::

5. Välj fliken **ExpressRoute** , skapa en ExpressRoute-Gateway. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Skärm bild av ExpressRoute-inställningarna.":::

   >[!TIP]
   >Ett värde för skalnings enhet är 2 Gbit/s. 

    Det tar cirka 30 minuter att skapa varje hubb. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Steg 3. Skapa ett plats-till-plats-VPN

1. I Azure Portal väljer du det virtuella WAN-nätverk som du skapade tidigare.

2. I **Översikt** över den virtuella hubben väljer du VPN för **anslutning**  >  **(plats-till-plats)**  >  **Skapa ny VPN-webbplats**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Skärm bild av översikts sidan för den virtuella hubben med VPN (plats-till-plats) och skapa en ny VPN-plats vald.":::  
 
3. På fliken **grundläggande** anger du de obligatoriska fälten. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Skärm bild av fliken grundläggande för den nya VPN-platsen.":::  

   1. Ange **Border Gateway Protocol** som ska **aktive ras**.  När det är aktiverat ser det till att både Azure VMware-lösningen och de lokala servrarna annonserar sina vägar i tunneln. Om inaktive ras måste de undernät som måste annonseras manuellt underhållas. Om undernät saknas kommer HCX inte att kunna bilda tjänst nätet. Mer information finns i  [om BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. För det **privata adress utrymmet** anger du det lokala CIDR-blocket. Den används för att dirigera all trafik som är bindning för lokal över tunneln. CIDR-blocket krävs endast om du inte aktiverar BGP.

1. Välj **Nästa: länkar** och fyll i de obligatoriska fälten. Genom att ange namn på länkar och providers kan du skilja mellan hur många gatewayer som kan ha skapats som en del av hubben. BGP och autonomt system nummer (ASN) måste vara unikt i din organisation.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Skärm bild som visar länk information.":::

1. Välj **Granska + skapa**. 

1. Navigera till det virtuella hubb du vill ha och avmarkera **hubben** för att ansluta din VPN-plats till hubben.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Skärm bild som visar rutan anslutna webbplatser för den virtuella NAVEt som är klar för I förväg delad nyckel och associerade inställningar.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Steg 4. Valfritt Skapa principbaserad VPN plats-till-plats-tunnlar

>[!IMPORTANT]
>Detta är ett valfritt steg och gäller endast för principbaserad VPN. 

Principbaserade VPN-inställningar kräver lokala och Azure VMware-lösnings nätverk som ska anges, inklusive Hubbs intervall.  Dessa Hubbs intervall Anger krypterings domänen för den principbaserade VPN-tunnelns lokala slut punkt.  Azure VMware-lösnings sidan kräver bara att indikatorn för principbaserad trafik väljs aktive ras. 

1. I Azure Portal går du till din virtuella WAN Hub-webbplats. Under **anslutning** väljer du **VPN (plats till plats)**.

2. Välj VPN-platsens namn, ellipsen (...) längst till höger och redigera sedan **VPN-anslutningen till den här hubben**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Skärm bild av sidan i Azure för den virtuella WAN Hub-platsen som visar att tre punkter har valts för att få åtkomst till redigera VPN-anslutning till hubben." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Redigera anslutningen mellan VPN-platsen och hubben och välj sedan **Spara**.
   - Internet Protocol säkerhet (IPSec) väljer du **anpassad**.
   - Använd principbaserade trafik väljare väljer du **Aktivera**
   - Ange information om **IKE fas 1** och **IKE fas 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Skärm bild av sidan Redigera VPN-anslutning."::: 
 
   Dina trafik väljare eller undernät som ingår i den principbaserad krypterings domänen ska vara:
    
   - Virtuell WAN-hubb `/24`
   - Privat moln för Azure VMware-lösningen `/22`
   - Anslutna Azure Virtual Network (om det finns)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Steg 5. Anslut VPN-platsen till hubben

1. Välj VPN-platsens namn och välj sedan **Anslut VPN-platser**. 

1. I fältet **I förväg delad nyckel** anger du den nyckel som tidigare definierats för den lokala slut punkten. 

   >[!TIP]
   >Om du inte har en tidigare definierad nyckel kan du lämna fältet tomt. En nyckel skapas automatiskt åt dig. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Skärm bild som visar rutan anslutna webbplatser för den virtuella NAVEt som är klar för en I förväg delad nyckel och tillhör ande inställningar. "::: 

1. Om du distribuerar en brand vägg i hubben och den är nästa hopp ställer du in **alternativet för att** **sprida standard väg** . 

   När det här alternativet är aktiverat sprider den virtuella WAN-hubben bara till en anslutning om navet redan lärt sig standard vägen när en brand vägg distribueras i hubben eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad. Standard vägen kommer inte från den virtuella WAN-hubben.  

1. Välj **Anslut**. Efter några minuter visar platsen anslutnings-och anslutnings status.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Skärm bild som visar en plats-till-plats-anslutning och anslutnings status." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Ladda ned VPN-konfigurationsfilen](../virtual-wan/virtual-wan-site-to-site-portal.md#device) för den lokala slut punkten.  

3. Korrigera ExpressRoute för Azure VMware-lösningen i den virtuella WAN-hubben. 

   >[!IMPORTANT]
   >Du måste först ha ett privat moln skapat innan du kan korrigera plattformen. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Länka Azure VMware-lösningen och VPN-gatewayen tillsammans i den virtuella WAN-hubben. Du använder auktoriseringsvärdet och ExpressRoute-ID: t (peer-krets-URI) från föregående steg.

   1. Välj din ExpressRoute-gateway och välj sedan **Lös in nyckel för autentisering**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Skärm bild av ExpressRoute-sidan för det privata molnet med en upplösande nyckel för auktorisering valt.":::

   1. Klistra in verifierings nyckeln i fältet **auktoriseringskod** .
   1. Klistra in ExpressRoute-ID i **peer-kretsens URI** -fält. 
   1. Markera kryss rutan **associera den här ExpressRoute-kretsen automatiskt med hubben** . 
   1. Välj **Lägg till** för att skapa länken. 

5. Testa anslutningen genom att [skapa ett NSX-segment](./tutorial-nsx-t-network-segment.md) och tillhandahålla en virtuell dator i nätverket. Pinga både lokala och Azure VMware-lösningens slut punkter.

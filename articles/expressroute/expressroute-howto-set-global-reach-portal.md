---
title: 'Azure-ExpressRoute: Konfigurera Global Reach med hjälp av Azure Portal'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk och aktivera Global Reach med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539357"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Konfigurera ExpressRoute-Global Reach med hjälp av Azure Portal

Den här artikeln hjälper dig att konfigurera ExpressRoute Global Reach med hjälp av PowerShell. Mer information finns i [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen bekräftar du följande kriterier:

* Du förstår [arbets flöden](expressroute-workflows.md)för ExpressRoute-krets etablering.
* Dina ExpressRoute-kretsar är i ett tillstånd som är etablerade.
* Azures privata peering har kon figurer ATS på dina ExpressRoute-kretsar.
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

## <a name="identify-circuits"></a>Identifiera kretsar

1. Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

2. Identifiera de ExpressRoute-kretsar som du vill använda. Du kan aktivera ExpressRoute Global Reach mellan den privata peering av två ExpressRoute-kretsar, så länge de finns i de länder/regioner som stöds. Kretsarna måste skapas på olika peering-platser. 

   * Om din prenumeration äger båda kretsarna kan du välja någon av kretsarna för att köra konfigurationen i följande avsnitt.
   * Om de två kretsarna finns i olika Azure-prenumerationer måste du auktorisera från en Azure-prenumeration. Sedan skickar du in verifierings nyckeln när du kör konfigurations kommandot i den andra Azure-prenumerationen.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Lista över ExpressRoute-kretsar":::

## <a name="enable-connectivity"></a>Aktivera anslutning

Aktivera anslutningar mellan dina lokala nätverk. Det finns separata uppsättningar instruktioner för kretsar som finns i samma Azure-prenumeration och kretsar som är olika prenumerationer.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-kretsar i samma Azure-prenumeration

1. Välj **Azures privata** peering-konfiguration. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Översikt över ExpressRoute-peering":::

1. Markera kryss rutan **aktivera Global Reach** och välj sedan **Lägg till Global Reach** för att öppna sidan *Lägg till Global Reach* konfiguration.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Aktivera global räckvidd från privat peering":::

1. Ange ett namn på den här konfigurationen på sidan *Lägg till Global Reach* konfiguration. Välj den *ExpressRoute-krets* som du vill ansluta kretsen till och ange i en **/29 IPv4** för *Global Reach under nätet*. Vi använder IP-adresser i det här under nätet för att upprätta anslutningar mellan de två ExpressRoute-kretsarna. Använd inte adresserna i det här under nätet i dina virtuella Azure-nätverk eller i ditt lokala nätverk. Välj **Lägg till** för att lägga till kretsen i den privata peering-konfigurationen.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Global Reach konfigurations sida":::

1. Klicka på **Spara** för att slutföra Global Reach-konfigurationen. När åtgärden har slutförts har du anslutning mellan de två lokala nätverken genom båda ExpressRoute-kretsarna.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Sparar konfiguration av privat peering":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om de två kretsarna inte finns i samma Azure-prenumeration behöver du behörighet. I följande konfiguration genereras auktoriseringen från krets 2-prenumerationen. Verifierings nyckeln skickas sedan till krets 1.

1. Generera en nyckel för autentisering.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Skapa auktoriseringsregel"::: 

   Anteckna krets resurs-ID: t för krets 2 och verifierings nyckeln.

1. Välj **Azures privata** peering-konfiguration. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Översikt över krets 1-peering":::

1. Markera kryss rutan **aktivera Global Reach** och välj sedan **Lägg till Global Reach** för att öppna sidan *Lägg till Global Reach* konfiguration.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Aktivera global räckvidd från krets 1":::

1. Ange ett namn på den här konfigurationen på sidan *Lägg till Global Reach* konfiguration. Markera kryss rutan **Lös in auktorisering** . Ange den **auktoriseringskod** och det **ExpressRoute krets-ID** som genereras och hämtades i steg 1. Ange sedan en **/29 IPv4** för *Global Reach under nätet*. Vi använder IP-adresser i det här under nätet för att upprätta anslutningar mellan de två ExpressRoute-kretsarna. Använd inte adresserna i det här under nätet i dina virtuella Azure-nätverk eller i ditt lokala nätverk. Välj **Lägg till** för att lägga till kretsen i den privata peering-konfigurationen.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Lägg till Global Reach med verifierings nyckel":::

1. Klicka på **Spara** för att slutföra Global Reach-konfigurationen. När åtgärden har slutförts har du anslutning mellan de två lokala nätverken genom båda ExpressRoute-kretsarna.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Sparar privat peering-konfiguration på krets 1":::

## <a name="verify-the-configuration"></a>Verifiera konfigurationen

Verifiera Global Reach-konfigurationen genom att välja *privat peering* under ExpressRoute krets-konfigurationen. När konfigurationen är korrekt konfigurerad bör se ut så här:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Verifiera Global Reach konfiguration":::

## <a name="disable-connectivity"></a>Inaktivera anslutningar

Du har två alternativ när det kommer att inaktivera Global Reach. Inaktivera anslutning mellan alla kretsar genom att avmarkera **aktivera Global Reach** för att inaktivera anslutning mellan alla kretsar. Om du vill inaktivera anslutningen mellan en enskild krets väljer du knappen Ta bort bredvid *Global Reach namn* för att ta bort anslutningen mellan dem. Välj **Spara** för att slutföra åtgärden.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Inaktivera Global Reach konfiguration":::

När åtgärden har slutförts har du inte längre någon anslutning mellan ditt lokala nätverk via dina ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg
1. [Läs mer om ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verifiera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)

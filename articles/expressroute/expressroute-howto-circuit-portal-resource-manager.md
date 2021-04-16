---
title: 'Snabbstart: Skapa och ändra en krets med ExpressRoute – Azure Portal'
description: I den här snabbstarten lär du dig att skapa, etablera, verifiera, uppdatera, ta bort och avetablera en ExpressRoute-krets med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-portal
ms.openlocfilehash: f62b60a9c33cd1b813b8f3307ec82d4242be168b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534795"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Snabbstart: Skapa och ändra en ExpressRoute-krets

Den här snabbstarten visar hur du skapar en ExpressRoute-krets med Azure Portal och Azure Resource Manager distributionsmodellen. Du kan också kontrollera status, uppdatera, ta bort eller avetablera en krets.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Granska kraven [och arbetsflödena](expressroute-prerequisites.md) [innan](expressroute-workflows.md) du påbörjar konfigurationen.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar förstå stegen bättre.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="create-a-new-expressroute-circuit"></a>Skapa en ny ExpressRoute-krets

> [!IMPORTANT]
> ExpressRoute-kretsen debiteras från den tidpunkt då en tjänstnyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att etablera kretsen.

Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. 

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. Välj   >  **ExpressRoute för** nätverk, som du ser i följande bild:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Skapa en ExpressRoute-krets":::

2. När du **har valt ExpressRoute** visas sidan **Skapa ExpressRoute.** Ange **Resursgrupp,** **Region** och  **Namn för** kretsen. Välj sedan **Nästa: Konfiguration >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurera resursgruppen och regionen":::

3. När du fyller i värdena på den här sidan kontrollerar du att du anger rätt SKU-nivå (lokal, Standard eller Premium) och faktureringsmodellen för datamätning (obegränsad eller avgiftsmätt).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurera kretsen":::
    
    * **Porttypen** avgör om du ansluter till en tjänstleverantör eller direkt till Microsofts globala nätverk på en peeringplats.
    * **Skapa ny eller importera från klassisk** avgör om en ny krets skapas eller om du migrerar en klassisk krets till Azure Resource Manager.
    * **Providern** är den Internetleverantör som du kommer att begära din tjänst från.
    * **Peering location (Peeringplats)** är den fysiska plats där du peer-arbetar med Microsoft.

    > [!IMPORTANT]
    > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peer-arbetar med Microsoft. Detta är **inte** länkat till egenskapen "Location", som refererar till det geografiska område där Azure-nätverksresursprovidern finns. Även om de inte är relaterade är det en bra idé att välja en nätverksresursprovider geografiskt nära kretsens peeringplats.

    * **SKU** avgör om ett ExpressRoute-lokalt, ExpressRoute-standard eller ett ExpressRoute Premium-tillägg är aktiverat. Du kan ange **Lokal** för att hämta den lokala SKU:n, **Standard** för att hämta standard-SKU:n eller **Premium** för premium-tillägget. Du kan ändra SKU:n för att aktivera premium-tillägget.
    > [!IMPORTANT]
    > Du kan inte ändra SKU:n **från Standard/Premium** till **Lokal**.
    
    * **Faktureringsmodellen** avgör faktureringstypen. Du kan ange **Mäts för** en dataförbrukningsplan och **Obegränsad för** ett obegränsat dataabonnemang. Du kan ändra faktureringstypen från **Mäts till** **Obegränsad.**

    > [!IMPORTANT]
    > Du kan inte ändra typen från **Unlimited (Obegränsat)** **till Metered (Mäts).**

    * **Tillåt klassisk åtgärd** tillåter att klassiska virtuella nätverk länkas till kretsen.

### <a name="view-the-circuits-and-properties"></a>Visa kretsar och egenskaper

**Visa alla kretsarna**

Du kan visa alla kretsar som du har skapat genom att välja **Alla tjänster > Nätverk > ExpressRoute-kretsar** på menyn till vänster.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menyn ExpressRoute-krets":::

Alla ExpressRoute-kretsar som skapats i prenumerationen visas här.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Lista över ExpressRoute-kretsar":::

**Visa egenskaperna**

Du kan visa kretsens egenskaper genom att välja den. På sidan **Översikt** för kretsen visas tjänstnyckeln i fältet Tjänstnyckel. Se tjänstnyckeln för kretsen och ange den för tjänstleverantören för att slutföra etableringsprocessen. Tjänstnyckeln är specifik för din krets.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Visa egenskaper":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Skicka tjänstnyckeln till anslutningsleverantören för etablering

På den här sidan **ger providerstatus** dig det aktuella tillståndet för etablering på tjänstleverantörssidan. **Kretsstatus** ger dig tillståndet på Microsoft-sidan. Mer information om kretsetablerings tillstånd finns i [artikeln Arbetsflöden.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

När du skapar en ny ExpressRoute-krets har kretsen följande tillstånd:

Providerstatus: **Inte etablerat**<BR>
Kretsstatus: **Aktiverad**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Startar etableringsprocessen":::

Kretsen ändras till följande tillstånd när anslutningsleverantören för närvarande aktiverar den åt dig:

Providerstatus: **Etablering**<BR>
Kretsstatus: **Aktiverad**

Om du vill använda ExpressRoute-kretsen måste den ha följande tillstånd:

Providerstatus: **Etablerat**<BR>
Kretsstatus: **Aktiverad**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Kontrollera regelbundet kretsnyckelns status och tillstånd

Du kan visa egenskaperna för den krets som du är intresserad av genom att välja den. Kontrollera **providerstatusen** och se till att den har **flyttats till Etablerat** innan du fortsätter.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Krets- och providerstatus":::

### <a name="create-your-routing-configuration"></a>Skapa din routningskonfiguration

Stegvisa instruktioner finns i artikeln [](expressroute-howto-routing-portal-resource-manager.md) Konfiguration av ExpressRoute-kretsroutning för att skapa och ändra krets-peerings.

> [!IMPORTANT]
> Dessa instruktioner gäller endast för kretsar som skapas med tjänstleverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänstleverantör som erbjuder hanterade Layer 3-tjänster (vanligtvis ett IP VPN, t.ex. MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med Resource Manager distributionsmodellen.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Hämta status för en ExpressRoute-krets

Du kan visa status för en krets genom att markera den och visa sidan Översikt.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra bandbredd, SKU, faktureringsmodell och tillåta klassiska åtgärder på **sidan** Konfiguration. Information om gränser och begränsningar finns i Vanliga frågor [och svar om ExpressRoute.](expressroute-faqs.md)

Du kan utföra följande uppgifter utan avbrottstid:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets.

> [!IMPORTANT]
  > Det går inte att ändra SKU:n **från** **Standard/Premium** till Lokal.

* Öka bandbredden för din ExpressRoute-krets, förutsatt att det finns tillgänglig kapacitet på porten.

  > [!IMPORTANT]
  > Nedgradering av bandbredden för en krets stöds inte.

* Ändra avläsningsplanen från *Datamätare till* *Obegränsad data*.

  > [!IMPORTANT]
  > Det går inte att ändra **avläsningsplanen från** **Obegränsade data till Datamätare.**

* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder.*
  > [!IMPORTANT]
  > Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
  >
  > Även om du sömlöst kan uppgradera bandbredden kan du inte minska bandbredden för en ExpressRoute-krets utan avbrott. Om du vill nedgradera bandbredden måste du avetablera ExpressRoute-kretsen och sedan ometablera en ny ExpressRoute-krets.
  >
  > Det går inte att inaktivera Premium-tillägget om du använder resurser som är större än vad som tillåts för standardkretsen.

Om du vill ändra en ExpressRoute-krets väljer du **Konfiguration.**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Ändra krets":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Avetablera en ExpressRoute-krets

Om ExpressRoute-kretsens tjänstproviders  etableringstillstånd är Etablering eller Etablerat måste du samarbeta med din tjänstleverantör för att avetablera kretsen på deras sida.  Vi fortsätter att reservera resurser och fakturera dig tills tjänstleverantören har slutfört avetablering av kretsen och meddelar oss.

> [!NOTE]
>* Du måste ta bort *länken till alla virtuella* nätverk från ExpressRoute-kretsen innan du avetablera. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
>* Om tjänstleverantören har avetablera kretsen (tjänsteleverantörens etableringstillstånd är **Inställt** på Inte etablerat ) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort ExpressRoute-kretsen genom att välja **ikonen Ta** bort. Kontrollera att providerstatusen *inte har etablerats innan* du fortsätter.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Ta bort krets":::

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen fortsätter du med följande nästa steg:

> [!div class="nextstepaction"]
> [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)

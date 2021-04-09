---
title: 'Snabb start: skapa och ändra en krets med ExpressRoute-Azure Portal'
description: I den här snabb starten får du lära dig hur du skapar, etablerar, kontrollerar, uppdaterar, tar bort och avetablerar en ExpressRoute-krets med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185206"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Snabb start: skapa och ändra en ExpressRoute-krets

Den här snabb starten visar hur du skapar en ExpressRoute-krets med hjälp av Azure Portal och Azure Resource Manager distributions modell. Du kan också kontrol lera status, uppdatera, ta bort eller avetablera en krets.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="create-a-new-expressroute-circuit"></a>Skapa en ny ExpressRoute-krets

> [!IMPORTANT]
> Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Se till att du utför den här åtgärden när anslutnings leverantören är redo att etablera kretsen.

Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. 

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. Välj **nätverks**  >  -**ExpressRoute**, som du ser i följande bild:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Skapa en ExpressRoute-krets":::

2. När du har valt **ExpressRoute** visas sidan **skapa ExpressRoute** . Ange **resurs grupp**, **region** och  **namn** för kretsen. Välj sedan **Nästa: konfigurations >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurera resurs gruppen och regionen":::

3. När du fyller i värdena på den här sidan, se till att du anger rätt SKU-nivå (lokal, standard eller Premium) och fakturerings modell för data mätning (obegränsad eller uppmätt).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurera kretsen":::
    
    * **Port typen** bestämmer om du ansluter till en tjänst leverantör eller direkt till Microsofts globala nätverk på en peering-plats.
    * **Skapa ny eller importera från klassisk** bestämmer om en ny krets skapas eller om du migrerar en klassisk krets till Azure Resource Manager.
    * **Provider** är Internet tjänst leverantören som du kommer att begära din tjänst från.
    * **Peering-platsen** är den fysiska plats där du är peering med Microsoft.

    > [!IMPORTANT]
    > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peer-koppla med Microsoft. Detta är **inte** länkat till "location"-egenskap som refererar till den geografi där Azure Network Resource-providern finns. Även om de inte är relaterade är det en bra idé att välja en nätverks resurs-Provider geografiskt nära peering-platsen för kretsen.

    * **SKU** avgör om en ExpressRoute lokal, ExpressRoute standard eller ett ExpressRoute Premium-tillägg är aktiverat. Du kan ange **lokal** för att hämta den lokala SKU: n, **standard** för att hämta standard-SKU eller **Premium** för Premium-tillägget. Du kan ändra SKU: n så att Premium-tillägget aktive ras.
    > [!IMPORTANT]
    > Du kan inte ändra SKU: n från **Standard/Premium** till **lokal**.
    
    * **Fakturerings modellen** fastställer fakturerings typen. Du kan ange **Mät** värde för en dataplan med datapriser och **obegränsade** för ett obegränsat data abonnemang. Du kan ändra fakturerings typen från **avgiftsbelagd** till **obegränsad**.

    > [!IMPORTANT]
    > Du kan inte ändra typen från **obegränsad** till **mätning**.

    * Med den **klassiska åtgärden** kan klassiska virtuella nätverk länka till kretsen.

### <a name="view-the-circuits-and-properties"></a>Visa kretsar och egenskaper

**Visa alla kretsar**

Du kan visa alla kretsar som du har skapat genom att välja **alla tjänster > nätverks > ExpressRoute-kretsar** på den vänstra menyn.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="ExpressRoute krets-menyn":::

Alla ExpressRoute-kretsar som skapats i prenumerationen visas här.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="ExpressRoute krets List":::

**Visa egenskaperna**

Du kan visa egenskaperna för kretsen genom att markera den. På **översikts** sidan för din krets visas tjänst nyckeln i fältet tjänst nyckel. Se tjänst nyckeln för din krets och ge den till tjänst leverantören för att slutföra etablerings processen. Tjänst nyckeln är speciell för din krets.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Visa egenskaper":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Skicka tjänst nyckeln till din anslutnings leverantör för etablering

På den här sidan ger **leverantörs status** det aktuella tillståndet för etablering på tjänstens leverantörs sida. **Krets status** ger dig tillståndet på Microsoft-sidan. Mer information om krets etablerings tillstånd finns i artikeln om [arbets flöden](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

Leverantörs status: **inte etablerad**<BR>
Krets status: **aktive rad**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Startar etablerings processen":::

Kretsen ändras till följande tillstånd när anslutnings leverantören för närvarande aktiverar den åt dig:

Leverantörs status: **etablering**<BR>
Krets status: **aktive rad**

Om du vill använda ExpressRoute-kretsen måste den vara i följande tillstånd:

Leverantörs status: **etablerad**<BR>
Krets status: **aktive rad**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Kontrol lera regelbundet status och tillståndet för krets nyckeln

Du kan visa egenskaperna för den krets som du är intresse rad av genom att markera den. Kontrol lera **providerns status** och se till att den har flyttats till **etablerad** innan du fortsätter.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Status för krets och Provider":::

### <a name="create-your-routing-configuration"></a>Skapa konfigurationen för routning

Stegvisa instruktioner finns i [ExpressRoute-kretsens konfigurations](expressroute-howto-routing-portal-resource-manager.md) artikel för att skapa och ändra krets-peering.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med tjänst leverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis ett IP-VPN, som MPLS) konfigurerar och hanterar din anslutnings leverantör routning åt dig.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med distributions modellen för Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Hämta status för en ExpressRoute-krets

Du kan visa statusen för en krets genom att markera den och Visa översikts sidan.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra bandbredd, SKU, fakturerings modell och tillåta klassisk drift på **konfigurations** sidan. Information om begränsningar och begränsningar finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets.

> [!IMPORTANT]
  > Det finns inte stöd för att ändra SKU: n från **Standard/Premium** till **lokal** .

* Öka bandbredden för din ExpressRoute-krets, förutsatt att kapaciteten finns tillgänglig på porten.

  > [!IMPORTANT]
  > Nedgradering av en kretss bandbredd stöds inte.

* Ändra Mät planen från *datapriser* till *obegränsade data*.

  > [!IMPORTANT]
  > Det finns inte stöd för att ändra Mät planen från **obegränsade data** till **datapriser** .

* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.
  > [!IMPORTANT]
  > Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
  >
  > Även om du sömlöst kan uppgradera bandbredden kan du inte minska bandbredden för en ExpressRoute-krets utan avbrott. Med degradering av bandbredd måste du avetablera ExpressRoute-kretsen och sedan etablera om en ny ExpressRoute-krets.
  >
  > Det går inte att inaktivera Premium-tilläggsen om du använder resurser som är större än vad som tillåts för standard kretsen.

Om du vill ändra en ExpressRoute-krets väljer du **konfiguration**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Ändra krets":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Avetablera en ExpressRoute-krets

Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.

> [!NOTE]
>* Du måste ta bort länken mellan *alla virtuella nätverk* från ExpressRoute-kretsen innan du avetablerar. Om den här åtgärden Miss lyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
>* Om tjänst leverantören har avetablerat kretsen (etablerings statusen för tjänst leverantören är inställd på **inte etablerad**) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort ExpressRoute-kretsen genom att välja ikonen **ta bort** . Se till att providerns status *inte är etablerad* innan du fortsätter.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Ta bort krets":::

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets fortsätter du med följande steg:

> [!div class="nextstepaction"]
> [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)

---
title: 'Självstudie: Konfigurera väg filter för Microsoft-peering – Azure Portal'
description: I den här självstudien beskrivs hur du konfigurerar väg filter för Microsoft-peering med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109144"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Självstudie: Konfigurera väg filter för Microsoft-peering med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera väg filter för ExpressRoute-kretsar.

Microsoft 365 tjänster som Exchange Online, SharePoint Online och Skype för företag är tillgängliga via Microsoft-peering. När Microsoft-peering konfigureras i en ExpressRoute-krets annonseras alla prefix som är relaterade till dessa tjänster via de BGP-sessioner som etableras. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över värdena för BGP-communityn och de tjänster som de mappar till finns i [BGP-communities](expressroute-routing.md#bgp).

Anslutning till alla Azure-och Microsoft 365-tjänster gör att ett stort antal prefix annonseras via BGP. Det stora antalet prefix ökar avsevärt storleken på de väg tabeller som hanteras av routrarna i nätverket. Om du planerar att endast använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att använda väg filter på BGP-communities. Flödes filtrering är en vanlig nätverks praxis och används ofta i många nätverk.

* Definiera väg filter och Använd dem i ExpressRoute-kretsen. Ett flödes filter är en ny resurs där du kan välja listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara listan över prefix som hör till de tjänster som identifieras i flödes filtret.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Hämta värden för BGP-communityn.
> - Skapa flödes filter och filter regel.
> - Koppla flödes filter till en ExpressRoute-krets.

### <a name="about-route-filters"></a><a name="about"></a>Om flödes filter

När Microsoft-peering konfigureras på din ExpressRoute-krets, upprättar Microsoft Edge-routrarna ett par med BGP-sessioner med dina gräns-routrar via anslutnings leverantören. Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i stort sett en tillåten lista över alla värden för BGP-communityn. När en väg filter resurs har definierats och kopplats till en ExpressRoute-krets annonseras alla prefix som mappar till BGP-communityns värden till nätverket.

Om du vill bifoga väg filter med Microsoft 365 tjänster måste du ha behörighet att använda Microsoft 365 tjänster via ExpressRoute. Om du inte har behörighet att använda Microsoft 365 tjänster via ExpressRoute Miss lyckas åtgärden att bifoga väg filter. Mer information om auktoriseringsprocessen finns i [Azure-ExpressRoute för Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen.
> 

## <a name="prerequisites"></a>Förutsättningar

- Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerad. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och låt den krets som aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.
  - [Skapa Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md) om du hanterar BGP-sessionen direkt. Du kan också använda din anslutnings leverantör för att etablera Microsoft-peering för din krets.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Hämta en lista över prefix och värden för BGP-community

### <a name="get-a-list-of-bgp-community-values"></a>Hämta en lista över värden för BGP-communityn

BGP community-värden som är kopplade till tjänster som är tillgängliga via Microsoft-peering finns på sidan [ExpressRoute-krav för routning](expressroute-routing.md) .

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Skapa en lista med de värden som du vill använda

Skapa en lista över [värden för BGP-grupper](expressroute-routing.md#bgp) som du vill använda i flödes filtret. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Skapa ett flödes filter och en filter regel

Ett flödes filter kan bara ha en regel och regeln måste vara av typen Allow. Den här regeln kan ha en lista över värden för BGP-communityn.

1. Välj **skapa en resurs** och Sök sedan efter *flödes filter* som du ser i följande bild:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Skärm bild som visar väg filter Sidan":::

1. Placera flödes filtret i en resurs grupp. Se till att platsen är samma som ExpressRoute-kretsen. Välj **Granska + skapa** och sedan **skapa**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Skärm bild som visar sidan Skapa väg filter med exempel värden som anges":::

### <a name="create-a-filter-rule"></a>Skapa en filter regel

1. Om du vill lägga till och uppdatera regler väljer du fliken Hantera regel för ditt flödes filter.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Skärm bild som visar översikts sidan med åtgärden hantera regel markerad":::

1. Välj de tjänster som du vill ansluta till i list rutan och spara regeln när du är färdig.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Skärm bild som visar fönstret Hantera regel med tjänster som marker ATS i list rutan tillåtna tjänste grupper":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Koppla väg filtret till en ExpressRoute-krets

Koppla väg filtret till en krets genom att välja knappen **+ Lägg till krets** och välja ExpressRoute-kretsen i list rutan.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Skärm bild som visar översikts sidan med åtgärden Lägg till krets valt":::

Om anslutnings leverantören konfigurerar peering för ExpressRoute-kretsen uppdaterar du kretsen från ExpressRoute-krets-sidan innan du väljer knappen **+ Lägg till krets** .

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Skärm bild som visar översikts sidan med åtgärden Uppdatera markerad.":::

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Hämta egenskaperna för ett flödes filter

Du kan visa egenskaperna för ett flödes filter när du öppnar resursen i portalen.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Skärmbild som visar sidan Översikt":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödes filter

1. Du kan uppdatera listan med BGP community-värden som är kopplade till en krets genom att välja knappen **Hantera regel** .

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Uppdatera väg filter med åtgärden hantera regel":::

1. Välj de tjänst webb grupper som du vill använda och välj sedan **Spara**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Skärm bild som visar fönstret Hantera regel med tjänster valt":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Koppla bort ett flödes filter från en ExpressRoute-krets

Om du vill koppla bort en krets från flödes filtret högerklickar du på kretsen och väljer Ta **bort koppling**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Skärm bild som visar översikts sidan med åtgärden koppla bort markerad":::


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort ett flödes filter genom att klicka på knappen **ta bort** . Se till att flödes filtret inte är kopplat till några kretsar innan du gör det.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Ta bort ett flödes filter":::

## <a name="next-steps"></a>Nästa steg

Information om konfigurations exempel för routern finns i:

> [!div class="nextstepaction"]
> [Konfigurations exempel för routern för att konfigurera och hantera routning](expressroute-config-samples-routing.md)

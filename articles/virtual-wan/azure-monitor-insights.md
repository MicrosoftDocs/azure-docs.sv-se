---
title: Övervaka virtuella WAN-nätverk med hjälp av Azure Monitor Insights
description: I den här artikeln får du lära dig mer om att övervaka Azure Virtual WAN med hjälp av Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: d14ae86d41caec3c1bd897c2c81bee748dcc312c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050892"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor insikter för virtuellt WAN

[Azure Monitor insikter](../azure-monitor/insights/network-insights-overview.md) för Azure Virtual WAN ger användare och operatörer möjlighet att visa status och status för ett virtuellt WAN-nätverk som presenteras via en automatiskt identifierad Top ologiska karta. Resurs tillstånd och status överlägg på kartan ger dig en Snapshot-vy över det övergripande hälso tillståndet för det virtuella WAN-nätverket. Du kan navigera i resurser på kartan via ett klick till resurs konfigurations sidorna i den virtuella WAN-portalen.

Virtuella WAN-mått på resurs nivå samlas in och presenteras via en förpackad virtuell WAN Metrics-arbetsbok. Arbets boken visar måtten på virtuella WAN-, nav-, gateway-och anslutnings nivåer. Den här artikeln vägleder dig genom stegen för att använda Azure Monitor insikter för virtuellt WAN för att visa din virtuella WAN-topologi och alla mått på en enda plats.

> [!NOTE]
> Meny alternativet **insikter** finns i den virtuella WAN-portalen under **övervakning**. Du kan också få åtkomst till den virtuella WAN-topologin och mått arbets boken med Azure Monitor för nätverk. Mer information finns i [Azure Monitor för nätverk](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln måste du ha ett virtuellt WAN med ett eller flera hubbar. Följ stegen i följande artiklar om du vill skapa ett virtuellt WAN och en hubb:

* [Skapa ett virtuellt WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Skapa en hubb](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Visa VWAN-topologi

Gå till **Azure Portal**  >  **virtuellt WAN**. I menyn **övervaka** i det vänstra fönstret väljer du **insikter (för hands version)**. Vyn **insikter** visas. Den visar den virtuella WAN-huvudschemat och arbets boken med hög nivå **mått** .

**Bild 1: övervaka > insikter-menyn**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Skärm bild som visar vyn insikter (för hands version)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

I vyn **insikter** kan du Visa de automatiskt identifierade virtuella WAN-resurserna. Bland dessa resurser ingår hubbar, gatewayer, brand väggar, anslutningar och ekrar virtuella nätverk, NVA från tredje part och grenar i ett virtuellt WAN-nätverk från slut punkt till slut punkt. Ett exempel finns i **bild 2**.

Resursens tillstånd och status är färgkodade och finns på resurs ikonerna i kartan. Hög nivå av virtuella WAN-mått, som hubb kapacitet och gateway-användning, visas till höger i fönstret i en mini-arbets bok.

**Bild 2: vyn insikter**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Skärm bild som visar vyn insikter." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Beroende vy

**Beroende** vyn för virtuella WAN-nätverk hjälper dig att visualisera den sammankopplade vyn över alla virtuella WAN-resurser i stor ordning i en nav-och-eker-arkitektur.

**Bild 3: VWAN beroende vy**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Skärm bild som visar beroende vyn." lightbox="./media/azure-monitor-insights/dependency-map.png":::

I den **beroende** visnings kartan visas följande resurser som ett anslutet diagram:

* Virtuella WAN-hubbar i olika Azure-regioner.
* Ekrar virtuella nätverk som är direkt anslutna till hubben.
* VPN-och Azure ExpressRoute-avdelnings platser och P2S-användare som är anslutna till varje hubb via deras respektive ExpressRoute-, S2S-och P2S-anslutningar och virtuella Nätverksgatewayen.
* Azure-brandväggar (inklusive brand Väggs proxyservrar från tredje part) distribuerade i en hubb (säker hubb).
* NVA från tredje part (virtuella nätverks enheter) som distribueras i eker-virtuella nätverk.

Beroende kartan visar även indirekt anslutna virtuella nätverk (virtuella nätverk som är peer-kopplade med virtuella WAN eker-nätverk).

Beroende kartan möjliggör enkel navigering till konfigurations inställningarna för varje resurs. Du kan till exempel hovra över Hub-resursen för att visa den grundläggande resurs konfigurationen, som hubb region och hubb. Högerklicka för att öppna sidan Azure Portal i nav resursen.

**Bild 4: navigera till resursinformation**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Skärm bild som visar hur du navigerar till resursinformation.":::

Sök-och filter fältet i **beroende** vyn är ett enkelt sätt att söka igenom grafen. Olika filter ger hjälp att begränsa sökningen till en specifik sökväg och tillstånd.

**Bild 5: sökning och filtrering**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Skärm bild som visar Sök-och filter fältet." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Detaljerade mått

Du kan välja **Visa detaljerade mått** för att få åtkomst till sidan detaljerade **mått** . Sidan **mått** är en instrument panel som är förkonfigurerad med separata flikar. Dessa flikar ger insikter om din virtuella WAN-resurs kapacitet, prestanda och användning på den virtuella WAN-nivån och på navnivå och på nivån för enskilda anslutningar.

**Bild 6: instrument panel för detaljerade mått**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Skärm bild som visar instrument panelen för detaljerade mått." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
* En fullständig beskrivning av alla virtuella WAN-mått finns i [virtuella WAN-loggar och mått](logs-metrics.md).

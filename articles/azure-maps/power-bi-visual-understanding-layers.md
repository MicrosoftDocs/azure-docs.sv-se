---
title: Förstå skikt i Azure Maps Power BI visuella objekt | Microsoft Azure Maps
description: I den här artikeln får du lära dig om de olika skikt som är tillgängliga i Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86262101"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Förstå skikt i Azure Maps Power BI visuella objekt

Det finns två typer av lager tillgängliga i Azure Maps visualiseringen. Den första typen fokuserar på att återge data som skickas till fönstret **fält** i det visuella objektet och består av följande lager, och vi kallar dessa data åter givnings skikt.

:::row:::
    :::column span="":::
        **Bubbelskikt**

        Återger punkter som skalade cirklar på kartan.

        ![Bubbeldiagram på kartan](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Liggande diagram lager**

        Återger punkter som 3D-staplar på kartan.
        
        ![Stapeldiagram på diagram nivå](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Den andra typen av lager ansluter ytterligare externa data källor som ska mappas för att ge mer kontext och består av följande skikt.

:::row:::
    :::column span="":::
        **Referensskikt**

        Överlappa en uppladdad polyjson-fil ovanpå kartan.

        ![Referens skikt på karta](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Panel lager**

        Täcka över ett anpassat panel lager ovanpå kartan.
        
        ![Panel lager på karta](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Trafik skikt**

        Täcka trafik information i real tid på kartan.
        
        ![Trafik skikt på karta](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Alla data åter givnings lager, samt **panel lagret**, har alternativ för min-och Max zoomnings nivåer som används för att ange ett intervall för zoomnings nivå som dessa lager ska visas på. Detta gör att en typ av åter givnings lager kan användas på en zoomnivå och en över gång till ett annat åter givnings lager på en annan zoomnings nivå.

Dessa lager har också ett alternativ som placeras i förhållande till andra lager i kartan. När flera data åter givnings lager används, bestämmer ordningen i vilken de läggs till i kartan sin relativa skikt ordning när de har samma **skikt placerings** värde.

## <a name="general-layer-settings"></a>Allmänna lager inställningar

Avsnittet Allmänt lager i fönstret **format** är vanliga inställningar som gäller för de lager som är anslutna till Power BI data uppsättning i fönstret **fält** (bubbeldiagram, stapeldiagram).

| Inställning     | Beskrivning   |
|-------------|---------------|
| Omarkerad genomskinlighet | Transparensen för former som inte är markerade, när en eller flera former är markerade.  |
| Visa nollor              | Anger om punkter som har ett storleks värde på noll ska visas på kartan med minsta radie. |
| Visa negativa          | Anger om det absoluta värdet av negativa storleks värden ska ritas.   |
| Minsta data värde          | Det minsta värdet för indata som ska skalas. Lämpar sig för avvikande värden i Urklipp.  |
| Max data värde          | Det maximala värdet för indata som ska skalas. Lämpar sig för avvikande värden i Urklipp.  |

## <a name="next-steps"></a>Nästa steg

Ändra hur dina data visas på kartan:

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett stapeldiagramslager](power-bi-visual-add-bar-chart-layer.md)

Lägg till mer kontext till kartan:

> [!div class="nextstepaction"]
> [Lägg till ett referenslager](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Visa trafik i realtid](power-bi-visual-show-real-time-traffic.md)

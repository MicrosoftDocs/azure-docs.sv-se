---
title: Lägga till ett stapeldiagram i Azure Maps Power BI visuella objekt | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder diagram lagret i Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896284"
---
# <a name="add-a-bar-chart-layer"></a>Lägg till ett stapeldiagramslager

**Liggande diagram lagret** är användbart för att ta data till nästa dimension genom att tillåta visualisering av plats data som 3D-staplar eller cylindrar på kartan. På samma sätt som Bubble-lagret kan stapeldiagrammet senare enkelt visualisera två mått samtidigt med hjälp av färg och relativ höjd. För att staplarna ska ha höjden måste ett mått läggas till i **storleks** Bucket för fönstret **fält** . Om ett mått inte anges visas staplar utan höjd som enkla fyr kanter eller cirklar beroende på **form** -alternativet.

> [!div class="mx-imgBorder"]
> ![En karta som visar punkt data med hjälp av diagram lagret](media/power-bi-visual/bar-chart-layer-styled.png)

Användare kan luta och rotera kartan för att visa dina data från olika perspektiv. Kartan kan vara lutande eller kanna med någon av följande metoder.

-   Aktivera alternativet **navigerings kontroller** i **format** fönstrets **kart inställningar** . Då läggs en knapp till för att luta kartan.
-   Tryck på den högra mus knappen och dra musen uppåt eller nedåt.
-   Använd en touch-skärm och vidrör kartan med två fingrar och dra dem uppåt eller nedåt.
-   Med kartan fokuserad, håll **ned SKIFT** -tangenten och tryck på tangenterna **UPPIL** **eller NEDPIL** .

Kartan kan roteras med någon av följande metoder.

-   Aktivera alternativet **navigerings kontroller** i **format** fönstrets **kart inställningar** . Då läggs en knapp till för att rotera kartan.
-   Tryck på den högra mus knappen och dra musen åt vänster eller höger.
-   Använd en touch-skärm och vidrör kartan med två fingrar och rotera.
-   Med kartan fokuserad, håll **ned SKIFT** -tangenten och tryck på **vänster** - **eller HÖGERPIL** .

Följande är alla inställningar i rutan **format** som är tillgängliga i avsnittet **diagram skikt** .

| Inställning              | Beskrivning      |
|----------------------|------------------|
| Liggande form            | 3D-fältets form.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box – staplar som återges som rektangulära rutor.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cylinder – staplar som återges som cylindrar. |
| Höjd               | Höjden för varje stapel. Om ett fält skickas till **storleks** Bucket för **fält** fönstret, skalas staplarna i förhållande till det här värdet för höjd. |
| Skala höjd vid zoomning | Anger om staplarna i staplarna ska skalas i förhållande till zoomnings nivån. |
| Bredd                | Bredden för varje stapel.  |
| Skala bredd vid zoomning  | Anger om bredden på staplarna ska skalas i förhållande till zoomnings nivån.  |
| Fyllningsfärg           | Färg för varje stapel. Det här alternativet är dolt när ett fält skickas till **förklarings** Bucket för **fält** fönstret och ett separat **data färger** -avsnitt visas i fönstret **format** . |
| Transparens         | Transparens för varje stapel. |
| Minsta zoomning             | Minsta zoomnings nivå paneler är tillgängliga. |
| Max zoomning             | Övre zoomnings nivå panelerna är tillgängliga. |
| Skikt position       | Anger lagrets position i förhållande till andra kart skikt. |

> [!NOTE]
> Om staplarna har ett litet bredd värde och **skalnings bredden vid zoomning** är inaktive rad kan de försvinna när de zoomas ut mycket eftersom deras åter givnings bredd skulle vara mindre än en pixel i storlek. Men när alternativet **skala bredd vid zoomning** är aktiverat, utförs ytterligare beräkningar när zoomnings nivån ändras, vilket kan påverka prestanda för stora data mängder.

## <a name="next-steps"></a>Nästa steg

Lägg till mer kontext till kartan:

> [!div class="nextstepaction"]
> [Lägg till ett referenslager](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Visa trafik i realtid](power-bi-visual-show-real-time-traffic.md)

Anpassa det visuella objektet:

> [!div class="nextstepaction"]
> [Tips för färgformatering i Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassa visualiseringens rubriker, bakgrunder och förklaringar](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
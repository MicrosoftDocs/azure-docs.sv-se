---
title: Lägg till ett bubbeldiagram till Azure Maps Power BI visuella objekt | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder Bubble-lagret i Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896233"
---
# <a name="add-a-bubble-layer"></a>Lägga till ett bubbelskikt

**Bubbel lagret** återger plats data som skalade cirklar på kartan.

> [!div class="mx-imgBorder"]
> ![En karta som visar punkt data med hjälp av Bubble-lagret](media/power-bi-visual/bubble-layer-with-legend-color.png)

Alla bubblor har ursprungligen samma fyllnings färg. Om ett fält skickas till **förklarings** Bucket för fönstret **fält** , kommer bubblorna att färgades baserat på deras kategorisering. Bubblornas kontur är vitt som standard, men kan ändras till en ny färg eller genom att aktivera alternativet för hög kontrast disposition. Alternativet för **hög kontrast disposition** tilldelar dynamiskt en kon tur färg som är en variant av en hög kontrast i fyllnings färgen. Detta hjälper till att se till att bubblorna syns tydligt oavsett stil på kartan. Följande är de viktigaste inställningarna i fönstret **format** som är tillgängliga i avsnittet **Bubble Layer** .

| Inställning               | Beskrivning    |
|-----------------------|----------------|
| Storlek                  | Storleken på varje bubbla. Det här alternativet är dolt när ett fält skickas till **storleks** Bucket för fönstret **fält** . Ytterligare alternativ visas som beskrivs i avsnittet om [skalnings storlek i bubblor](#bubble-size-scaling) mer detaljerat i den här artikeln. |
| Fyllningsfärg            | Färg på varje bubbla. Det här alternativet är dolt när ett fält skickas till **förklarings** Bucket för **fält** fönstret och ett separat **data färger** -avsnitt visas i fönstret **format** . |
| Fyll genomskinlighet     | Transparens för varje bubbla. |
| Hög kontrast – kontur | Gör kon tur färg kontrasten med fyllnings färgen för bättre tillgänglighet genom att använda en hög kontrast variant av fyllnings färgen. |
| Konturfärg         | Färg som visar bubblan. Det här alternativet är dolt när alternativet **hög kontrast kon tur** är aktiverat. |
| Konturerad genomskinlighet  | Transparens för konturen. |
| Kon tur bredd         | Bredden på konturen i bild punkter. |
| Suddar ut                  | Mängden oskärpa som tillämpas på konturen. Värdet 1 suddar ut bubblorna så att endast mitt punkten inte har någon genomskinlighet. Värdet 0 använder eventuell suddiga påverkan. |
| Justering av lutning       | Anger hur bubblorna ska se ut när kartan är i bredd. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Visnings området – bubblor visas på kanten på kartan i förhållande till visnings området. (standard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Kart bubblor återges vågrätt på kartans yta. |
| Zoomnings skala            | Mängden som bubblorna ska skalas i förhållande till zoomnings nivån. En zoomnings skala för ett innebär ingen skalning. Stora värden gör att bubblorna blir mindre när de zoomas ut och blir större när de zoomas in. På så sätt kan du minska oredan på kartan när den zoomas ut, men ser till att punkter ser ut mer vid zoomning i. Värdet 1 gäller inte för skalning. |
| Minsta zoomning              | Minsta zoomnings nivå paneler är tillgängliga. |
| Max zoomning              | Övre zoomnings nivå panelerna är tillgängliga. |
| Skikt position        | Anger lagrets position i förhållande till andra kart skikt. |

## <a name="bubble-size-scaling"></a>Storleks skalning för bubblor

Om ett fält skickas till **storleks** Bucket för **fält** fönstret, skalas bubblorna relativt till Mät värdet för varje data punkt. Alternativet **storlek** i avsnittet **bubbeldiagram** i fönstret **format** försvinner när ett fält överförs till **storleks** gruppen, eftersom bubblornas radie skalas mellan ett minsta och högsta värde. Följande alternativ visas i avsnittet **bubbeldiagram** i fönstret **format** när en **storleks** Bucket har ett fält angivet.

| Inställning             | Beskrivning  |
|---------------------|--------------|
| Minsta storlek            | Minsta bubbelstorlek vid skalning av data.|
| Max storlek            | Maximal bubbelstorlek vid skalning av data.|
| Metod för storleks skalning | Skalnings algoritm som används för att fastställa relativ bubbelstorlek.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Linjärt inmatnings data linjärt mappat till minsta och högsta storlek. (standard)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Logg intervallet för indata-logarithmically mappas till minsta och högsta storlek.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier – ange x1, Y1, x2, Y2-värden för en Cubic-Bezier kurva för att skapa en anpassad skalnings metod. |

När **storleks skalnings metoden** är inställd på **log** blir följande alternativ tillgängliga.

| Inställning   | Beskrivning      |
|-----------|------------------|
| Logg skala | Den logaritmiska skala som ska användas när bubblornas storlek beräknas. |

När **storleks skalnings metoden** har angetts till **kubisk-Bezier** görs följande alternativ för att anpassa skalnings kurvan.

| Inställning | Beskrivning                           |
|---------|---------------------------------------|
| PCIe      | X1-parameter för en kubikmeter av en kubikmeter. |
| Y1      | X2-parametern för en kubikmeter av en kubikmeter. |
| X2      | Y1-parameter för en kubikmeter av en kubikmeter. |
| Y2      | Y2-parameter för en kubikmeter av en kubikmeter. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) har ett användbart verktyg för att skapa parametrar för Cubic-Bezier kurvor.

## <a name="next-steps"></a>Nästa steg

Ändra hur dina data visas på kartan:

> [!div class="nextstepaction"]
> [Lägg till ett stapeldiagramslager](power-bi-visual-add-bar-chart-layer.md)

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
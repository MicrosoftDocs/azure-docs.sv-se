---
title: Komma igång med Azure Maps Power BI visualisering | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder Microsoft Azure Maps-visualisering för Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a563a7776e84289e38743057778e8fe10fd17503
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904711"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Komma igång med Azure Maps Power BI visuella objektet

<Token>**gäller för:** ![ Grön bock markering. ](media/power-bi-visual/yes.png) Power BI-tjänst för **_konsumenter_** ![ grön bock markering. ](media/power-bi-visual/yes.png) Power BI-tjänst för designers & utvecklare ![ grön bock markering. ](media/power-bi-visual/yes.png) Power BI Desktop ![ X som anger Nej. ](media/power-bi-visual/no.png) Kräver Pro-eller Premium-licens</Token>

Den här artikeln visar hur du använder Microsoft Azure Maps-visualisering för Power BI.

> [!NOTE]
> Det här visuella objektet kan skapas och visas i både Power BI Desktop och i Power BI-tjänst. Stegen och illustrationerna i artikeln är från Power BI Desktop.

Azure Maps visualiseringen för Power BI ger en omfattande uppsättning data visualiseringar för spatialdata ovanpå en karta. Det uppskattas att över 80% av affärs data har ett plats sammanhang. Azure Maps visuella objekt kan användas för att få insikter om hur den här plats kontexten relaterar till och påverkar dina affärs data.

![Power BI Station ära datorer med Azure Maps visuell visning av affärs data](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Vad skickas till Azure?

Azure Maps-visualiseringen ansluter till moln tjänsten som finns i Azure för att hämta plats data, till exempel kart bilder och koordinater som används för att skapa kart visualiseringen. 

-   Information om området som kartan fokuserar på skickas till Azure för att hämta bilder som behövs för att återge kart ytan (kallas även kart paneler). 
-   Data på plats-, latitud-och longitud-Bucket kan skickas till Azure för att hämta mappnings koordinater (en process som kallas för kodning). 
-   Telemetridata kan samlas in på hälso tillståndet för det visuella objektet (t. ex. krasch rapporter), om alternativet telemetri i Power BI är aktiverat.

Förutom de scenarier som beskrivs ovan skickas inga andra data som finns på kartan till Azure Maps-servrarna. All data åter givning sker lokalt i klienten.

Du eller administratören kan behöva uppdatera brand väggen för att tillåta åtkomst till den Azure Maps-plattform som använder följande URL.

> `https://atlas.microsoft.com`

Mer information om sekretess och användnings villkor som rör Azure Maps visuella objekt finns [Microsoft Azure juridisk information](https://azure.microsoft.com/support/legal/).

## <a name="azure-maps-visual-preview-behavior-and-requirements"></a>Funktioner och krav för Azure Maps Visual (för hands version)

Det finns några överväganden och krav för **Azure Maps** visualisering. :

-   **Azure Maps** visualiseringen (för hands version) måste vara aktive rad i Power BI Desktop. Om du vill aktivera **Azure Maps** visualisering väljer du alternativ för **fil** &gt; **och inställningar** för &gt;  &gt; för **hands versions funktioner** och markerar sedan kryss rutan **Azure Maps visuella** objekt. Om Azure Maps visualiseringen inte är tillgänglig när du har gjort det, är det troligt att en klient administratörs växel i administrations portalen måste vara aktive rad.
-   Data uppsättningen måste innehålla fält som innehåller information om **latitud** och **longitud** . Den här koden kommer att läggas till i en framtida uppdatering.
-   Den inbyggda förklarings kontrollen för Power BI visas inte i den här för hands versionen. Den kommer att läggas till i en framtida uppdatering.

## <a name="use-the-azure-maps-visual-preview"></a>Använda Azure Maps visuella objekt (för hands version)

När **Azure Maps** visualisering har Aktiver ATS väljer du ikonen **Azure Maps** i fönstret **visualiseringar** .

![Azure Maps visuell knapp i fönstret visualiseringar](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI skapar en tom Azure Maps visuell design arbets yta. I för hands versionen visas en extra fri skrivning.

![Power BI Station ära datorer med den Azure Maps som är synlig i det ursprungliga läget](media/power-bi-visual/visual-initial-load.png)

Utför följande steg för att läsa in Azure Maps visuella objekt:

1.  I rutan **fält** drar du data fält som innehåller information om latitud-och longitud-koordinater till **latitud** -och/eller **longitud** -buckets. Detta är den minsta mängd data som krävs för att läsa in Azure Maps visuella objekt.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps visuella objekt som visar punkter som bubblor på kartan efter att latitud-och longitud-fälten har angetts](media/power-bi-visual/bubble-layer.png)

2.  Om du vill färglägga data baserat på kategorisering drar du ett kategoriska-fält till **förklarings** Bucket för fönstret **fält** . I det här exemplet använder vi kolumnen **AdminDistrict** (kallas även delstat eller provins).  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps visuell visning av punkter som färg bubblor i kartan efter förklarings fältet](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > Den inbyggda förklarings kontrollen för Power BI visas inte i den här för hands versionen. Den kommer att läggas till i en framtida uppdatering.

3.  Om du vill skala data relativt drar du ett mått till **storleks** Bucket för fönstret **fält** . I det här exemplet använder vi **Sales** -kolumnen.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps visuella objekt som visar punkter som färgade och skalade bubblor på kartan när storleks fältet har angetts.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  Använd alternativen i fönstret **format** för att anpassa hur data återges. Följande bild är samma karta som ovan, men med alternativet för att fylla genomskinlighet i bubbeldiagram på 50% och alternativet hög kontrast kon figurer ATS.  
    
    > [!div class="mx-imgBorder"]
    > ![Azure Maps visuella objekt som visar punkter som bubblor på kartan med en anpassad stil](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Buckets för fält fönster

Följande databuckets är tillgängliga i rutan **fält** i Azure Maps visualiseringen.

| Fält     | Beskrivning  |
|-----------|--------------|
| Latitud  | Det fält som används för att ange latitud-värdet för data punkterna. Latitude-värden ska vara mellan-90 och 90 i formatet decimal grader.  |
| Longitud | Det fält som används för att ange data punkternas longitud-värde. Longitud-värden ska vara mellan-180 och 180 i decimal format.  |
| Förklaring    | Det fält som används för att kategorisera data och tilldela en unik färg för data punkter i varje kategori. När den här Bucket är fylld visas avsnittet **data färger** i fönstret **format** som tillåter justeringar av färgerna. |
| Storlek      | Måttet som används för relativ storlek på data punkter på kartan.   |
| Knappbeskrivningar  | Ytterligare data fält som visas i knapp beskrivningar när former hovrar över. |

## <a name="map-settings"></a>Kart inställningar

Avsnittet **kart inställningar** i fönstret format ger alternativ för att anpassa hur kartan visas och reagerar på uppdateringar.

| Inställning             | Beskrivning  |
|---------------------|--------------|
| Automatisk zoomning           | Zoomar automatiskt in kartan till de data som läses in via rutan **fält** i visualiseringen. När data ändras kommer kartan att uppdatera sin position enligt detta. När skjutreglaget är i **stängnings** läge visas ytterligare inställningar för kart visning för standard kartan. |
| Världs omslutning          | Gör att användaren kan panorera kartan vågrätt oändligt. |
| Format väljare        | Lägger till en knapp till kartan som gör det möjligt för rapport läsarna att ändra kartans format. |
| Navigerings kontroller | Lägger till knappar till kartan som en annan metod så att rapport läsarna kan zooma, rotera och ändra kartans lutning. Mer information finns i det här dokumentet [navigera på kartan](map-accessibility.md#navigating-the-map) för information om alla olika sätt som användare kan navigera i kartan. |
| Kart stil           | Kartans format. Mer information finns i det här dokumentet för mer information om [mappnings format som stöds](supported-map-styles.md). |

### <a name="map-view-settings"></a>Inställningar för kart visning

Om skjutreglaget för **automatisk zoomning** är i **stängnings** läge visas följande inställningar och låter användaren ange standard information för kart visning.

| Inställning          | Beskrivning   |
|------------------|---------------|
| Zoom             | Kartans standard zoomnings nivå. Kan vara ett tal mellan 0 och 22. |
| Center Latitude  | Standard latitud i mitten av kartan. |
| Mitten longitud | Standard longituden i mitten av kartan. |
| Rubrik          | Standard orienteringen för kartan i grader, där 0 är norr, 90 är öst, 180 är syd och 270 är väst. Kan vara ett tal mellan 0 och 360. |
| Röst            | Standard lutningen för kartan i grader mellan 0 och 60, där 0 ser ut rakt nedåt på kartan. |

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

Det Azure Maps visuella objektet är tillgängligt i följande tjänster och program:

| Tjänst/app                              | Tillgängligt |
|------------------------------------------|--------------|
| Power BI Desktop                         | Ja          |
| Power BI-tjänsten (app.powerbi.com)       | Ja          |
| Power BI Mobile-appar             | Ja          |
| Power BI Publicera på webben                  | Nej           |
| Power BI Embedded                        | Nej           |
| Power BI-tjänstinbäddning (PowerBI.com) | Ja          |

Stöd för ytterligare Power BI tjänster/appar kommer att läggas till i framtida uppdateringar.

**Var finns Azure Maps?**

För tillfället är Azure Maps för närvarande tillgänglig i alla länder och regioner förutom följande:

- Kina
- Sydkorea

För information om disponering för de olika Azure Maps tjänster som använder det här visuella objektet, se dokumentet om [geografisk täcknings information](geographic-coverage.md) .

**Vilka webbläsare stöds av Azure Maps visualiseringen?**

I den här dokumentationen finns information om [Azure Maps webbläsare som stöds av Web SDK](supported-browsers.md).

**Hur många data punkter kan jag visualisera?**

Detta visuella objekt har stöd för upp till 30 000 data punkter.

**Kan adresser eller andra plats strängar användas i det här visuella objektet?**

Den inledande förhands granskningen av det här visuella objektet stöder bara latitud-och longitud-värden i decimal grader. Vid en framtida uppdatering läggs stöd för adresser och andra plats strängar till.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Power BI visuella objekt:

> [!div class="nextstepaction"]
> [Förstå skikt i Azure Maps Power BI visuella objekt](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Hantera det Azure Maps visuella objektet i din organisation](power-bi-visual-manage-access.md)

Anpassa det visuella objektet:

> [!div class="nextstepaction"]
> [Tips för färgformatering i Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassa visualiseringens rubriker, bakgrunder och förklaringar](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
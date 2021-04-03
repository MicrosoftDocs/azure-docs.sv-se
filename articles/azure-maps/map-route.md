---
title: Visa väg riktningar på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar vägvisningar mellan två platser på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890725"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B

Den här artikeln visar hur du gör en cirkulations förfrågan och visar vägen på kartan.

Det finns två sätt att göra detta på. Det första sättet är att fråga [Azure Maps Route-API: et](/rest/api/maps/route/getroutedirections) via en service-modul. Det andra sättet är att använda [hämtnings-API: et](https://fetch.spec.whatwg.org/) för att göra en Sök förfrågan till [Azure Maps Route-API: et](/rest/api/maps/route/getroutedirections). Båda sätten beskrivs nedan.

## <a name="query-the-route-via-service-module"></a>Fråga vägen via Service-modulen

<iframe height='500' scrolling='no' title='Visa vägvisningar från A till B på en karta (service Module)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se anvisningarna för <a href='https://codepen.io/azuremaps/pen/RBZbep/'>att Visa pennan från a till B på en karta (service Module)</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I ovanstående kod konstruerar det första blocket ett kart objekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar en `TokenCredential` för att autentisera HTTP-begäranden för att Azure Maps med åtkomsttoken. Den skickar sedan `TokenCredential` till `atlas.service.MapsURL.newPipeline()` och skapar en [pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) -instans. `routeURL`Representerar en URL som Azure Maps [väg](/rest/api/maps/route) åtgärder.

Det tredje blocket med kod skapar och lägger till ett [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) -objekt till kartan.

Det fjärde blocket Code skapar start-och slut [punkts](/javascript/api/azure-maps-control/atlas.data.point) objekt och lägger till dem i DataSource-objektet.

En linje är en [funktion](/javascript/api/azure-maps-control/atlas.data.feature) för lin Est ring. En [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) återger rad objekt i  [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) som rader på kartan. Det fjärde blocket med kod skapar och lägger till ett linje skikt i kartan. Se egenskaperna för ett linje lager på [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Ett [symbol lager](/javascript/api/azure-maps-control/atlas.layer.symbollayer) använder text eller ikoner för att återge punktbaserade data i data [källan](/javascript/api/azure-maps-control/atlas.source.datasource). Texten eller ikonerna återges som symboler på kartan. Det femte blocket med kod skapar och lägger till ett symbol lager till kartan.

Det sjätte blocket med kod frågar Azure Maps Routningstjänst, som är en del av [Service-modulen](how-to-use-services-module.md). [CalculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) -metoden för RouteURL används för att hämta en väg mellan start-och slut punkterna. En samling av en interjson-funktion från svaret extraheras sedan med hjälp av- `geojson.getFeatures()` metoden och läggs till i data källan. Sedan återges svaret som en väg på kartan. Mer information om hur du lägger till en linje till kartan finns i [lägga till en rad på kartan](map-add-line-layer.md).

Det sista blocket i koden anger gränserna för kartan med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) -egenskap.

Route-fråga, data källa, symbol, linje lager och kamera gränser skapas i [händelse lyssnaren](/javascript/api/azure-maps-control/atlas.map#events). Den här kod strukturen ser till att resultaten visas först när kartan har lästs in helt.

## <a name="query-the-route-via-fetch-api"></a>Fråga vägen via hämtnings-API

<iframe height='500' scrolling='no' title='Visa vägvisningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Penn spetsen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>från a till B på en karta</a> med Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket kod ett kart objekt och ställer in autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar och lägger till ett [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) -objekt till kartan.

Det tredje kod blocket skapar start-och mål platserna för vägen. Sedan läggs de till i data källan. Du kan se [Lägg till en PIN-kod på kartan](map-add-pin.md) för instruktioner om hur du använder [addPins](/javascript/api/azure-maps-control/atlas.map).

En [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) återger rad objekt i  [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) som rader på kartan. Det fjärde blocket med kod skapar och lägger till ett linje skikt i kartan. Se egenskaperna för ett linje lager på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Ett [symbol lager](/javascript/api/azure-maps-control/atlas.layer.symbollayer) använder text eller ikoner för att återge punktbaserade data i [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) som symboler på kartan. Det femte blocket med kod skapar och lägger till ett symbol lager till kartan. Se egenskaperna för ett symbol lager på [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Nästa kodblock skapar `SouthWest` och `NorthEast` pekar från start-och mål punkterna och anger kartans gränser med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) -egenskap.

Det sista blocket i koden använder [hämtnings-API: t](https://fetch.spec.whatwg.org/) för att göra en Sök förfrågan till [Azure Maps Route-API: et](/rest/api/maps/route/getroutedirections). Svaret parsas sedan. Om svaret lyckades används informationen om latitud och longitud för att skapa en matris med en linje genom att ansluta dessa punkter. Linje data läggs sedan till i data källan för att rendera vägen på kartan. Du kan se [Lägg till en rad på kartan](map-add-line-layer.md) för instruktioner.

Route-fråga, data källa, symbol, linje lager och kamera gränser skapas i [händelse lyssnaren](/javascript/api/azure-maps-control/atlas.map#events). Återigen vill vi se till att resultaten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för att använda routningstjänsten](how-to-use-best-practices-for-search.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Visa trafik på kartan](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagera med kart mus händelser](./map-events.md)
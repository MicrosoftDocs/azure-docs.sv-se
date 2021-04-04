---
title: Visa Sök resultat på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du utför en search-begäran med Microsoft Azure Maps-webbsdk och visar resultaten på kartan.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895281"
---
# <a name="show-search-results-on-the-map"></a>Visa Sök resultat på kartan

Den här artikeln visar hur du söker efter intressanta platser och visar Sök resultaten på kartan.

Det finns två sätt att söka efter en plats av intresse. Ett sätt är att använda en service-modul för att göra en Sök förfrågan. Det andra sättet är att göra en sökbegäran för att [Azure Maps oskarp söknings-API](/rest/api/maps/search/getsearchfuzzy) via [hämtnings-API: et](https://fetch.spec.whatwg.org/). Båda sätten beskrivs nedan.

## <a name="make-a-search-request-via-service-module"></a>Gör en search-begäran via Service-modulen

<iframe height='500' scrolling='no' title='Visa Sök resultat på en karta (service Module)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Sök resultaten för pennan på en karta (service Module)</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kart objekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar en `TokenCredential` för att autentisera HTTP-begäranden för att Azure Maps med åtkomsttoken. Den skickar sedan `TokenCredential` till `atlas.service.MapsURL.newPipeline()` och skapar en [pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) -instans. `searchURL`Visar en URL som Azure Maps [Sök](/rest/api/maps/search) åtgärder.

Det tredje blocket med kod skapar ett data käll objekt med klassen [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) och lägger till Sök resultat. Ett [symbol lager](/javascript/api/azure-maps-control/atlas.layer.symbollayer) använder text eller ikoner för att återge punktbaserade data i [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) som symboler på kartan.  Sedan skapas ett symbol lager. Data källan läggs till i symbol skiktet, som sedan läggs till i kartan.

Det fjärde kod blocket använder metoden [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) i [modulen Service](how-to-use-services-module.md). Det gör att du kan utföra en texts ökning i fritext genom att söka efter intresse punkter i [fuzzy REST-API](/rest/api/maps/search/getsearchfuzzy) . Get-begäranden till Sök fuzzy API kan hantera valfri kombination av suddiga indata. En samling av en interjson-funktion från svaret extraheras sedan med hjälp av `geojson.getFeatures()` metoden och läggs till i data källan, vilket automatiskt resulterar i att de data som återges på kartan överförs via symbol lagret.

Det sista blocket med kod justerar kamerans gränser för kartan med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) -egenskap.

Sök förfrågan, data källan, symbol skiktet och kamera gränserna finns inuti [händelse lyssnaren](/javascript/api/azure-maps-control/atlas.map#events) för kartan. Vi vill se till att resultaten visas när kartan har lästs in helt.


## <a name="make-a-search-request-via-fetch-api"></a>Gör en Sök förfrågan via hämtnings-API

<iframe height='500' scrolling='no' title='Visa Sök resultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Sök resultaten för pennan på en karta</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Den anger att autentiseringsmekanismen ska använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar en URL som gör en Sök förfrågan till. Det skapar också två matriser för lagring av gränser och PIN-koden för Sök resultat.

Det tredje blocket kod använder [hämtnings-API: et](https://fetch.spec.whatwg.org/). [Hämtnings-API: et](https://fetch.spec.whatwg.org/) används för att göra en begäran om att [Azure Maps fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) för att söka efter intressanta punkter. API för fuzzy search kan hantera valfri kombination av suddiga indata. Den hanterar och tolkar sedan Sök svaret och lägger till resultat fästarna i searchPins-matrisen.

Det fjärde blocket med kod skapar ett data käll objekt med hjälp av [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) -klassen. I koden lägger vi till Sök resultat till källobjektet. Ett [symbol lager](/javascript/api/azure-maps-control/atlas.layer.symbollayer) använder text eller ikoner för att återge punktbaserade data i [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) som symboler på kartan. Sedan skapas ett symbol lager. Data källan läggs till i symbol skiktet, som sedan läggs till i kartan.

Det sista blocket med kod skapar ett [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) -objekt. Den använder en resultat mat ris och justerar sedan kamerans gränser för kartan med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Den återger sedan resultat stiften.

Sök förfrågan, data källan, symbol lagret och kamerans gränser anges i kartans [händelse lyssnare](/javascript/api/azure-maps-control/atlas.map#events) för att säkerställa att resultaten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för att använda Sök tjänsten](how-to-use-best-practices-for-search.md)

Lär dig mer om **fuzzy search**:

> [!div class="nextstepaction"]
> [API för Azure Maps oskarp sökning](/rest/api/maps/search/getsearchfuzzy)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Hämta information från en koordinat](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](map-route.md)
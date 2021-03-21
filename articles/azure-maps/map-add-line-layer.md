---
title: Lägg till ett linje lager i en karta | Microsoft Azure Maps
description: Lär dig hur du lägger till rader till kartor. Se exempel som använder Azure Maps Web SDK för att lägga till linje lager i kartor och för att anpassa rader med symboler och färg toningar.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: fedf1bc37661100404106859d3aef52b3eddf06d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048145"
---
# <a name="add-a-line-layer-to-the-map"></a>Lägg till ett linje skikt i kartan

Ett linje lager kan användas för att återge `LineString` och `MultiLineString` funktioner som sökvägar eller vägar på kartan. Ett linje lager kan också användas för att återge konturen för `Polygon` och `MultiPolygon` funktionerna. En data källa är ansluten till ett linje lager för att ge den data som ska återges. 

> [!TIP]
> Linje lager som standard återger koordinaterna för polygoner och linjer i en data källa. För att begränsa lagret så att det bara återger lin Est ring-funktioner, anger `filter` du egenskapen för lagret till `['==', ['geometry-type'], 'LineString']` eller `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` om du även vill inkludera MultiLineString-funktioner.

Följande kod visar hur du skapar en rad. Lägg till raden i en data källa och återge den sedan med ett linje lager med klassen [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Lägg till en linje till en karta</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Linje lager kan skrivas med [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) och [använda uttryck för data drivna format](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Lägg till symboler längs en linje

Det här exemplet visar hur du lägger till pil-ikoner längs en linje på kartan. När du använder ett symbol lager ställer du in alternativet "placering" på "linje". Med det här alternativet återges symbolerna längs linjen och ikonerna roteras (0 grader = höger).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Visa pil längs linje" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se pilen för Penn <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Visa utmed rad</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda med symbol lagret. Mer information finns i dokumentet om [att använda bildmallar](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Lägg till en linje toning till en linje

Du kan använda en enda linje färg för en linje. Du kan också fylla en linje med en toning av färger för att visa över gången från ett linje segment till nästa rad segment. Till exempel kan rad övertoningar användas för att representera ändringar över tid och avstånd, eller olika temperaturer över en ansluten linje med objekt. För att kunna använda den här funktionen på en rad måste data källan ha `lineMetrics` alternativet inställt på `true` och ett uttryck för färg toning kan skickas till `strokeColor` alternativet på raden. Uttrycket linje toning måste referera till det `['line-progress']` data uttryck som visar de beräknade linje måtten för uttrycket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje med Penseldrags toning" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>linjen med penseldrags toning</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Anpassa ett linje lager

Linje skiktet har flera format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för linje skikt' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>linje skiktet</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)
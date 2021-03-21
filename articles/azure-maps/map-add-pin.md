---
title: Lägg till ett symbol lager till en karta | Microsoft Azure Maps
description: Lär dig hur du lägger till anpassade symboler, till exempel text eller ikoner, till kartor. Se hur du använder data källor och symbol lager i Azure Maps Web SDK för det här ändamålet.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891078"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Lägga till ett symbol lager till en karta

Anslut en symbol till en data källa och Använd den för att återge en ikon eller en text vid en viss tidpunkt. 

Symbol lager återges med WebGL. Använd ett symbol lager för att rendera stora samlingar av punkter på kartan. Jämfört med HTML-markör återger symbol lagret ett stort antal punkt data på kartan med bättre prestanda. Symbol lagret stöder dock inte traditionella CSS-och HTML-element för formatering.  

> [!TIP]
> Symbol lager som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner anger du `filter` egenskapen för lagret till `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du vill kan du även inkludera Multipoint-funktioner.

Maps bild Sprite Manager läser in anpassade bilder som används av symbol skiktet. Det stöder följande bild format:

- -
- PNG
- SVG
- BMP
- GIF (inga animeringar)

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Innan du kan lägga till ett symbol lager till kartan måste du utföra några steg. Börja med att skapa en data källa och Lägg till den på kartan. Skapa ett symbol lager. Sedan skickar du data källan till symbol lagret för att hämta data från data källan. Slutligen lägger du till data i data källan så att det finns något att återge. 

Koden nedan visar vad som ska läggas till i kartan när den har lästs in. Det här exemplet återger en enda punkt på kartan med ett symbol lager. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Det finns fyra olika typer av punkt data som kan läggas till i kartan:

- Geometrisk punkt geometri – det här objektet innehåller bara en koordinat för en punkt och inget annat. `atlas.data.Point`Hjälp klassen kan användas för att enkelt skapa dessa objekt.
- Real JSON MultiPoint-geometri – det här objektet innehåller koordinaterna för flera punkter och inget annat. `atlas.data.MultiPoint`Hjälp klassen kan användas för att enkelt skapa dessa objekt.
- Funktionen för geometriskt JSON – det här objektet består av valfri geometrisk geometri och en uppsättning egenskaper som innehåller metadata som är kopplade till geometrin. `atlas.data.Feature`Hjälp klassen kan användas för att enkelt skapa dessa objekt.
- `atlas.Shape` klassen liknar funktionen för polyjson. Båda består av en geometrisk geometri och en uppsättning egenskaper som innehåller metadata som är kopplade till geometrin. Om ett interjson-objekt läggs till i en data källa kan det enkelt återges i ett lager. Men om egenskapen koordinater för det här interjson-objektet uppdateras, ändras inte data källan och kartan. Det beror på att det inte finns någon mekanism i JSON-objektet för att utlösa en uppdatering. Klassen Shape innehåller funktioner för att uppdatera data som den innehåller. När en ändring görs, meddelas data källan och-mappningen automatiskt och uppdateras. 

Följande kod exempel skapar en geometrisk punkt geometri och skickar den till- `atlas.Shape` klassen så att den blir lätt att uppdatera. Mitten av kartan används ursprungligen för att återge en symbol. En klick händelse läggs till i kartan, så att koordinaterna för musen används med funktionen shapes när den utlöses `setCoordinates` . Mus koordinaterna registreras vid tidpunkten för klicknings händelsen. Sedan `setCoordinates` uppdateras platsen för symbolen på kartan.

<br/>

<iframe height='500' scrolling='no' title='Byt PIN-sökväg' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Fäst punkten</a> för Penn växlar genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra. När du zoomar in blir de dolda symbolerna synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden, anger du `allowOverlap` egenskapen för `iconOptions` alternativen till `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i ett symbol lager

Symbol lager återges med WebGL. Alla resurser, till exempel ikon bilder, måste läsas in i WebGL-kontexten. Det här exemplet visar hur du lägger till en anpassad ikon i kart resurserna. Den här ikonen används sedan för att återge punkt data med en anpassad symbol på kartan. För `textField` symbol skiktets egenskap krävs att ett uttryck anges. I det här fallet vill vi återge temperatur egenskapen. Eftersom temperatur är ett tal måste det konverteras till en sträng. Dessutom vill vi lägga till "°F". Ett uttryck kan användas för att utföra den här sammanfogningen. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikon för anpassad symbol bild' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>bild ikonen för den anpassade symbolen</a> för penna genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda med symbol lagret. Mer information finns i dokumentet om [att använda bildmallar](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Anpassa ett symbol lager 

Symbol skiktet har många tillgängliga format alternativ. Här är ett verktyg för att testa dessa olika format alternativ.

<br/>

<iframe height='700' scrolling='no' title='Symbol lager alternativ' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/PxVXje/'>symbol lager</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om du bara vill rendera text med ett symbol lager kan du dölja ikonen genom `image` att ange egenskapen för ikon alternativen till `'none'` .

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

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
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
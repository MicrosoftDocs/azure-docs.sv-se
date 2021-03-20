---
title: Lägg till ett enkelt data lager | Microsoft Azure Maps
description: Lär dig hur du lägger till ett enkelt data lager med hjälp av den spatiala IO-modulen, som tillhandahålls av Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896165"
---
# <a name="add-a-simple-data-layer"></a>Lägga till ett enkelt datalager

Den spatiala IO-modulen tillhandahåller en `SimpleDataLayer` klass. Den här klassen gör det enkelt att återge formateringsfunktioner på kartan. Det kan även återge data uppsättningar som har stil egenskaper och data uppsättningar som innehåller blandade geometri typer. Det enkla data lagret uppnår den här funktionen genom att figursätta flera åter givnings lager och använda format uttryck. Format uttrycken söker efter vanliga stil egenskaper för funktionerna i dessa figursatta lager. `atlas.io.read`Funktionen och `atlas.io.write` funktionen använder dessa egenskaper för att läsa och skriva format i ett fil format som stöds. När du har lagt till egenskaperna till ett fil format som stöds, kan filen användas i olika syfte. Filen kan till exempel användas för att visa de formatbaserade funktionerna på kartan.

Förutom formateringsåtgärder `SimpleDataLayer` innehåller en inbyggd popup-funktion med en popup-mall. Popup-fönstret visas när användaren klickar på en funktion. Standard-popup-funktionen kan inaktive ras om du vill. Det här lagret stöder också klustrade data. När du klickar på ett kluster kommer kartan att zooma in i klustret och expandera den till enskilda punkter och under kluster.

`SimpleDataLayer`Klassen är avsedd att användas på stora data mängder med många geometri typer och många olika format tillämpas på funktionerna. När den här klassen används lägger den till en kostnad på sex lager som innehåller format uttryck. Det finns därför fall då det är mer effektivt att använda kärn åter givnings skiktet. Använd till exempel ett kärn lager för att rendera ett par geometri typer och några format på en funktion

## <a name="use-a-simple-data-layer"></a>Använd ett enkelt data lager

`SimpleDataLayer`Klassen används som andra åter givnings skikt används. Koden nedan visar hur du använder ett enkelt data lager på en karta:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Lägg till funktioner till data källan. Sedan kommer det enkla data lagret att ta reda på hur det är bäst att återge funktionerna. Format för enskilda funktioner kan anges som egenskaper för funktionen. Följande kod visar en funktion för en interjson-punkt med en `color` egenskap inställd på `red` . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Följande kod återger ovanstående punkt funktion med hjälp av det enkla data lagret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Använd det enkla data lagret" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Se pennan <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Använd det enkla data lagret genom att</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Den verkliga kraften i det enkla data lagret kommer när:

- Det finns flera olika typer av funktioner i en data källa. eller
- Funktioner i data uppsättningen har flera olika format egenskaper individuellt angivna. eller
- Du är inte säker på vilken data uppsättning som innehåller exakt.

Om du till exempel tolkar XML-datafeeds kanske du inte känner till de exakta formaten och geometri typerna för funktionerna. Följande exempel visar kraften i det enkla data lagret genom att återge funktionerna i en KML-fil. Det visar också olika alternativ som den enkla data skikts klassen tillhandahåller.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för enkla data lager" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Se alternativen för <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>enkel data lager</a> för pennor genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Det här enkla data lagret använder rutan [popup-mall](map-add-popup.md#add-popup-templates-to-the-map) för att visa KML-ballonger eller funktions egenskaper som en tabell. Som standard kommer allt innehåll som återges i popup-fönstret att visas i en iframe som en säkerhetsfunktion. Det finns dock begränsningar:
>
> - Alla skript, formulär, lås och bästa navigerings funktioner inaktive ras. Länkar får öppnas på en ny flik när du klickar på dem. 
> - Äldre webbläsare som inte stöder `srcdoc` parametern på iframes begränsas till åter givning av en liten mängd innehåll.
> 
> Om du litar på data som läses in i popup-fönster och eventuellt vill att dessa skript ska läsas in i popup-fönster, kan du inaktivera detta genom att ställa in alternativet för popup-mallar `sandboxContent` på falskt. 

## <a name="default-supported-style-properties"></a>Standard stil egenskaper som stöds

Som tidigare nämnts radbryter det enkla data lagret flera av kärn åter givnings lagren: bubbeldiagram, symbol, linje, polygon och extruderad polygon. Den använder sedan uttryck för att söka efter giltiga stil egenskaper för enskilda funktioner.

Azure Maps-och GitHub stil egenskaperna är de två huvud uppsättningarna med egenskaps namn som stöds. De flesta egenskaps namnen för de olika Azure Maps Layer-alternativen stöds som stil egenskaper för funktioner i det enkla data lagret. Uttryck har lagts till i vissa lager alternativ för att stödja format egenskaps namn som ofta används av GitHub. Dessa egenskaps namn definieras av [GitHub-stöd för interjson-kartor](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)och de används för att formatera inaktuella JSON-filer som lagras och återges i plattformen. Alla format egenskaper för GitHub stöds i det enkla data lagret, förutom `marker-symbol` stil egenskaperna.

Om läsaren har en mindre gemensam stil egenskap konverteras den till den närmsta Azure Maps Style-egenskapen. Dessutom kan standard stil uttrycken åsidosättas med hjälp av `getLayers` funktionen för det enkla data lagret och uppdatera alternativen på något av lagren.

I följande avsnitt finns information om standard stil egenskaperna som stöds av det enkla data lagret. Ordningen på egenskaps namnet som stöds är också egenskapens prioritet. Om två stil egenskaper har definierats för samma lager alternativ, har den första i listan högre prioritet. Färger kan vara valfritt CSS3 färg värde; HEX, RGB, RGBA, HSL, HSLA eller namngivet färg värde.

### <a name="bubble-layer-style-properties"></a>Stil egenskaper för bubbeldiagram

Om en funktion är en `Point` eller en `MultiPoint` , och funktionen inte har en `image` egenskap som skulle användas som en anpassad ikon för att återge punkten som en symbol, kommer funktionen att återges med en `BubbleLayer` .

| Lager alternativ | Egenskaps namn som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] `size` värdena och `scale` betraktas som skalära värden, och de multipliceras med `8`

\[2 \] om alternativet GitHub `marker-size` anges används följande värden för RADIUS.

| Markörstorlek | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Kluster återges också med hjälp av Bubble-skiktet. Som standard är radien för ett kluster inställt på `16` . Klustrets färg varierar beroende på antalet punkter i klustret, enligt vad som anges nedan:

| antal punkter | Färg    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Egenskaper för symbol stil

Om en funktion är en `Point` eller en `MultiPoint` , och funktionen har en `image` egenskap som skulle användas som en anpassad ikon för att återge punkten som en symbol, kommer funktionen att återges med en `SymbolLayer` .

| Lager alternativ | Egenskaps namn som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] om alternativet GitHub `marker-size` anges används följande värden för alternativet ikon storlek.

| Markörstorlek | Symbolstorlek |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Om punkt funktionen är ett kluster, `point_count_abbreviated` återges egenskapen som en text etikett. Ingen bild kommer att återges.

### <a name="line-style-properties"></a>Egenskaper för linje format

Om funktionen är en `LineString` ,, `MultiLineString` `Polygon` eller `MultiPolygon` , kommer funktionen att återges med en `LineLayer` .

| Lager alternativ | Egenskaps namn som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Egenskaper för polygon

Om funktionen är en `Polygon` eller en `MultiPolygon` , och funktionen antingen saknar `height` egenskap eller om `height` egenskapen är noll, kommer funktionen att återges med en `PolygonLayer` .

| Lager alternativ | Egenskaps namn som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Egenskaper för extruderad polygon

Om funktionen är en `Polygon` eller en `MultiPolygon` , och har en `height` egenskap med ett värde som är större än 0, kommer funktionen att återges med en `PolygonExtrusionLayer` .

| Lager alternativ | Egenskaps namn som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Läsa och skriva rumsliga data](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Lägga till ett OGC-kartskikt](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Använda kärnåtgärder](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)
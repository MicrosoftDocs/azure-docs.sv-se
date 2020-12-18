---
title: Skapa en data källa för en karta i Microsoft Azure Maps
description: 'Ta reda på hur du skapar en data källa för en karta. Lär dig mer om de data källor som Azure Maps Web SDK använder: JSON-källor och vektor paneler.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678859"
---
# <a name="create-a-data-source"></a>Skapa en datakälla

Azure Maps Web SDK lagrar data i data källor. Att använda data källor optimerar data åtgärder för frågor och åter givning. Det finns för närvarande två typer av data Källor:

* **Mål-JSON-källa**: hanterar rå data i det lokala JSON-formatet lokalt. Lämpat för små till medel stora data mängder (flera hundra tusentals former).
* **Vektor panels källa**: läser in data som är formaterade som vektor paneler för den aktuella Map-vyn, baserat på kartans placerings system. Perfekt för stora till enorma data uppsättningar (miljon tals eller miljarder former).

## <a name="geojson-data-source"></a>Data källa för interjson

En interjson-baserad data källa för inläsning och lagring av data lokalt med hjälp av `DataSource` klassen. Du kan skapa eller skapa indata från interjson manuellt med hjälp av klasser i [Atlas. data](/javascript/api/azure-maps-control/atlas.data) område. `DataSource`Klassen innehåller funktioner för att importera lokala eller fjärranslutna JSON-filer. Fjärranslutna interjson-filer måste finnas på en CORs-aktiverad slut punkt. `DataSource`Klassen innehåller funktioner för kluster punkt data. Du kan enkelt lägga till, ta bort och uppdatera data med- `DataSource` klassen. Följande kod visar hur du kan skapa data i en interjson-data i Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

När du har skapat data källor kan du lägga till dem i kartan via `map.sources` egenskapen, som är en [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager). Följande kod visar hur du skapar en `DataSource` och lägger till den på kartan.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

Följande kod visar de olika sätt som data kan läggas till i en `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> Du kan säga att du vill skriva över alla data i en `DataSource` . Om du gör anrop till `clear` `add` Functions-funktionerna kan kartan återges på nytt två gånger, vilket kan leda till en stund. Använd i stället `setShapes` funktionen, som tar bort och ersätter alla data i data källan och utlöser bara en åter givning av kartan.

## <a name="vector-tile-source"></a>Vektor panels källa

En vektor panels källa beskriver hur du får åtkomst till ett vektor panels lager. Använd klassen [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) för att instansiera en vektor panels källa. Vektor panels lager liknar panel lager, men de är inte samma. Ett panel lager är en raster bild. Vektor panels lager är en komprimerad fil i **PBF** -format. Den här komprimerade filen innehåller vektor kart data och ett eller flera lager. Filen kan återges och formateras på klienten, baserat på formatet för varje lager. Data i en vektor panel innehåller geografiska funktioner i form av punkter, linjer och polygoner. Det finns flera fördelar med att använda vektor panels lager i stället för raster panels lager:

* En fil storlek på en vektor panel är vanligt vis mycket mindre än en motsvarande raster panel. Som sådan används mindre bandbredd. Det innebär kortare svars tid, en snabbare karta och en bättre användar upplevelse.
* Eftersom vektor paneler återges på klienten, anpassas de till lösning av enheten som de visas på. Resultatet blir att de åter givnings kartorna visas mer väldefinierade med Crystal Clear-etiketter.
* Att ändra formatet för data i vektor Maps kräver inte att data hämtas igen, eftersom det nya formatet kan tillämpas på klienten. Att ändra formatet på ett raster panels lager kräver däremot vanligt vis inläsning av paneler från servern och sedan att använda det nya formatet.
* Eftersom data levereras i vektor form krävs mindre bearbetning på Server sidan för att förbereda data. Därför kan nya data göras tillgängliga snabbare.

Azure Maps följer [specifikationen Mapbox Vector panel](https://github.com/mapbox/vector-tile-spec), en öppen standard. Azure Maps tillhandahåller följande tjänster för vektor paneler som en del av plattformen:

- Information om [](/rest/api/maps/renderv2/getmaptilepreview)  |  [data format](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) för Road panels dokumentation
- Information om trafik incidenter [dokumentation](/rest/api/maps/traffic/gettrafficincidenttile)  |  [data format](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- [](/rest/api/maps/traffic/gettrafficflowtile)  |  [Information om data format](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) för trafikflödets dokumentation
- Azure Maps Creator (förhands granskning) tillåter också att anpassade vektor paneler skapas och nås via [Get panel rendering v2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> När du använder vektor-eller raster bild rutor från tjänsten Azure Maps Render med webb-SDK kan du ersätta `atlas.microsoft.com` med plats hållaren `{azMapsDomain}` . Plats hållaren ersätts med samma domän som används av kartan och lägger automatiskt till samma autentiseringsinformation. Detta fören klar autentiseringen med Render-tjänsten avsevärt när du använder Azure Active Directory autentisering.

Om du vill visa data från en vektor panels källa på kartan ansluter du källan till en av data åter givnings skikten. Alla lager som använder en Vector-källa måste ange ett `sourceLayer` värde i alternativen. Följande kod läser in tjänsten Azure Maps Traffic Flow Vector panel som en vektor panels källa. därefter visas den på en karta med ett linje lager. Den här vektor panels källan har en enda uppsättning data i käll skiktet som kallas "trafikflöde". Rad data i den här data uppsättningen har en egenskap `traffic_level` som kallas som används i den här koden för att välja färg och skala storleken på linjerna.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje skikt för vektor panel" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>linje lagret</a> för pennan tecknings panelen genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Ansluta en data källa till ett lager

Data återges på kartan med åter givnings lager. En enskild data källa kan refereras till av en eller flera åter givnings skikt. Följande åter givnings skikt kräver en data Källa:

* [Bubbel Layer](map-add-bubble-layer.md) – återger punkt data som skalade cirklar på kartan.
* [Symbol lager](map-add-pin.md) – återger punkt data som ikoner eller text.
* [Termiskt kart skikt](map-add-heat-map-layer.md) – återger punkt data som en densitets värme karta.
* [Linje lager](map-add-shape.md) – återge en linje och eller återge konturen för polygoner. 
* [Polygon-lager](map-add-shape.md) – fyller ytan i en polygon med ett mönster för en solid färg eller bild.

Följande kod visar hur du skapar en data källa, lägger till den i kartan och kopplar den till ett bubbeldiagram. Och importera sedan data från en fjärran sluten plats till data källan. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Det finns ytterligare åter givnings skikt som inte ansluter till dessa data källor, men som läser in data direkt för åter givning. 

* [Bild lager](map-add-image-layer.md) – lägger till en enda bild ovanpå kartan och binder dess hörn till en uppsättning angivna koordinater.
* [Panel lager](map-add-tile-layer.md) – superdriver ett raster panels lager ovanpå kartan.

## <a name="one-data-source-with-multiple-layers"></a>En data källa med flera lager

Flera lager kan anslutas till en enda data källa. Det finns många olika scenarier där det här alternativet är användbart. Anta till exempel scenariot där en användare ritar en polygon. Vi ska återge och fylla i polygon-ytan när användaren lägger till punkter i kartan. Om du lägger till en formaterad linje för att disponera polygonen blir det enklare att se polygonens kanter när användaren ritar. För att enkelt redigera en enskild position i polygonen kan vi lägga till en referens, t. ex. en PIN-kod eller en markör, ovanför varje position.

![Kartning visar flera nivåer som återger data från en enskild data Källa](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

I de flesta mappnings plattformar skulle du behöva ett polygon-objekt, ett linje objekt och en PIN-kod för varje position i polygonen. När polygonen ändras måste du uppdatera linjen och PIN-fälten manuellt, vilket snabbt kan bli komplext.

Med Azure Maps måste allt du behöver vara en enda polygon i en data källa som visas i koden nedan.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> När du lägger till lager till kartan med `map.layers.add` funktionen, kan ID eller instans för ett befintligt lager skickas som en andra parameter. Detta skulle berätta för kartan att infoga det nya lagret som läggs till under det befintliga lagret. Förutom att skicka in ett lager-ID, stöder den här metoden även följande värden.
>
> * `"labels"` -Infogar det nya lagret under kart etikettens lager.
> * `"transit"` -Infogar det nya lagret under kart vägs-och transport lager.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Datakälla](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till en värme karta](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)
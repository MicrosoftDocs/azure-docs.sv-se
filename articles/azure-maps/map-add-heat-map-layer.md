---
title: Lägg till ett värme kart skikt i en karta | Microsoft Azure Maps
description: Lär dig hur du skapar en värme karta. Se hur du använder Azure Maps Web SDK för att lägga till ett värme kart skikt till en karta. Lär dig hur du anpassar värme kart skikt.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b15ee7091a68f7fcc79c71877c4af28b511b84de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680140"
---
# <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering. De används för att representera densiteten för data med hjälp av en rad färger och visa data "frekventa fläckar" på en karta. Värme kartor är ett bra sätt att rendera data uppsättningar med ett stort antal punkter. 

Åter givning av tusentals punkter som symboler kan avse de flesta av kart områdena. Det här fallet resulterar förmodligen i att många symboler överlappar varandra. Gör det svårt att få bättre förståelse för dina data. Att visualisera samma data uppsättning som en värme karta gör det dock enkelt att se densiteten och den relativa densiteten för varje data punkt.

Du kan använda värme kartor i många olika scenarier, inklusive:

- **Temperatur data**: tillhandahåller ungefärliger för vad temperaturen är mellan två data punkter.
- **Data för buller sensorer**: visar inte bara intensiteten för bruset där sensorn är, men det kan också ge insikter om avtagningen över ett avstånd. Brus nivån på en plats kanske inte är hög. Om brus täcknings området från flera sensorer överlappar varandra, är det möjligt att det överlappande området kan uppleva högre buller nivåer. Därför skulle det överlappande område vara synligt i värme kartan.
- **GPS-spårning**: omfattar hastigheten som en viktad höjd karta där intensiteten för varje data punkt baseras på hastigheten. Den här funktionen är till exempel ett sätt att se var ett fordon går snabbare.

> [!TIP]
> Värme kart skikt återges som standard koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter` egenskapen för lagret till `['==', ['geometry-type'], 'Point']` . Om du vill inkludera MultiPoint-funktioner anger du även `filter` egenskapen för lagret till `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` .

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Om du vill rendera en data källa med punkter som en värme karta kan du skicka data källan till en instans av `HeatMapLayer` klassen och lägga till den i kartan.

I följande kod har varje värme punkt en radie på 10 bild punkter på alla zoomnings nivåer. För att säkerställa en bättre användar upplevelse är värme kartan under etikett skiktet. Etiketterna förblir tydligt synliga. Data i det här exemplet är från [USGS datauppsättningen jord bävning-riskhanterings program](https://earthquake.usgs.gov/). Det är för betydande jord bävningar som har inträffat under de senaste 30 dagarna.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Här är det fullständiga kod exemplet i föregående kod.

<br/>

<iframe height='500' scrolling='no' title='Enkelt värme kart skikt' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>enkla värme kart skiktet</a> Ritstift genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Anpassa värme kart skiktet

I föregående exempel har du anpassat värme kartan genom att ange alternativ för radie och opacitet. Värme kart skiktet innehåller flera alternativ för anpassning, inklusive:

* `radius`: Definierar en pixel-radie där varje data punkt ska renderas. Du kan ställa in radien som ett fast tal eller som ett uttryck. Genom att använda ett uttryck kan du skala radien baserat på zoomnivån och representera ett konsekvent rums område på kartan (till exempel en radie på 5 mil).
* `color`: Anger hur värme kartan är färgad. En färg toning är en vanlig funktion i värme kartor. Du kan uppnå resultatet med ett `interpolate` uttryck. Du kan också använda ett `step` uttryck för att färgsätta värme kartan och dela upp densiteten i intervall som liknar en profil för konturer eller radar format. Dessa färgpaletter definierar färgerna från minimi värdet till högsta densitet. 

  Du anger färg värden för värme Maps som ett uttryck för `heatmap-density` värdet. Färgen på det områden där det inte finns några data som definieras vid index 0 i "interpolationsmetod"-uttrycket eller standard färgen för ett "stegat"-uttryck. Du kan använda det här värdet för att definiera en bakgrunds färg. Ofta är det här värdet inställt på transparent eller en halv genomskinlig svart. 
   
  Här är exempel på färg uttryck:

  | Färg uttryck för interpolation | Uttryck för stegvisa färger | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;interpolerat,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' linjär ' \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet" \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, transparent,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, lila,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, #fb00fb,<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, #00c3ff<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"steg",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet" \] ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;transparent,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, flottan,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, grönt,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "gul",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "röd"<br/>\] |   

- `opacity`: Anger hur ogenomskinligt eller transparent det termiska kart skiktet är.
- `intensity`: Tillämpar en multiplikator till vikten för varje data punkt för att öka termisk karta totala intensitet. Det orsakar en skillnad i vikten av data punkter, vilket gör det lättare att visualisera dem.
- `weight`: Som standard har alla data punkter en vikt på 1, och viktas lika. Vikt alternativet fungerar som en multiplikator och du kan ange det som ett tal eller ett uttryck. Om ett tal har angetts som vikt, är det likvärdigheten att placera varje data punkt på kartan två gånger. Om vikten till exempel är 2, så dubbleras densiteten. Om du ställer in viktnings alternativet på ett tal återges den termiska kartan på samma sätt som med alternativet intensitet. 

  Men om du använder ett uttryck kan vikten för varje data punkt baseras på egenskaperna för varje data punkt. Anta till exempel att varje data punkt representerar en jord bävning. Värdet för storlek har varit ett viktigt mått för varje jord bävning data punkt. Jord bävningar sker hela tiden, men de flesta har låg storlek och har inte märkts. Använd värdet i ett uttryck för att tilldela vikten till varje data punkt. Genom att använda värdet för att tilldela vikten får du en bättre representation av betydelsen av jord bävningar i värme kartan.
- `source` och `source-layer` : gör det möjligt att uppdatera data källan.

Här är ett verktyg för att testa olika alternativ för termisk kart skikt.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för termisk kart skikt' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>termisk Map-lager</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsekvent zoomnings bara termisk karta

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När du zoomar kartan ser data samman varandra och värme kart lagret annorlunda ut. 

Använd ett `zoom` uttryck för att skala radien för varje zoomnings nivå, så att varje data punkt täcker samma fysiska område av kartan. Det här uttrycket gör att värme kart lagret ser mer statiskt och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. 

Att skala radien så att den dubbleras med varje zoomnivå skapar en värme karta som ser konsekvent ut på alla zoomnings nivåer. Om du vill använda den här skalningen använder du `zoom` med ett bas-2- `exponential interpolation` uttryck med pixel-radien inställd på minsta zoomnivå och en skalad radie för den högsta zoomnings nivån som `2 * Math.pow(2, minZoom - maxZoom)` visas i följande exempel. Zooma kartan för att se hur värme kartan skalar med zoomnings nivån.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsekvent zoomnings bara termisk karta" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>inkonsekventa instabila värme kartan</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du aktiverar klustring på data källan grupperas punkter som är nära varandra grupperade tillsammans som en klustrad punkt. Du kan använda antalet punkter för varje kluster som värme kartans vikt uttryck. Detta kan avsevärt minska antalet poäng som ska återges. Antalet poäng i ett kluster lagras i en `point_count` egenskap för punkt funktionen: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Om den klustrade radien bara är några få pixlar, skulle det finnas en liten visuell skillnad i åter givningen. En större RADIUS-grupp ger fler punkter i varje kluster och förbättrar prestandan för termisk karta.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
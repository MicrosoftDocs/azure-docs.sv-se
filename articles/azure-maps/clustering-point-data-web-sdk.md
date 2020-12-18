---
title: Kluster plats data på en karta | Microsoft Azure Maps
description: Lär dig mer om kluster punkts data på Maps. Se hur du använder Azure Maps Web SDK för att klustra data, reagera på kluster mus händelser och Visa kluster mängder.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e80465cf8d43918e6ed6da8ebb3b96f3f197e887
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679954"
---
# <a name="clustering-point-data"></a>Kluster plats data

Vid visualisering av många data punkter på kartan kan data punkterna överlappa varandra. Överlappningen kan leda till att kartan blir oläslig och svår att använda. Kluster plats data är en process för att kombinera punkt data som ligger nära varandra och som representerar dem på kartan som en enda klustrad data punkt. När användaren zoomar in i kartan delas klustren isär i sina enskilda data punkter. När du arbetar med ett stort antal data punkter kan du använda kluster processerna för att förbättra användar upplevelsen.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="enabling-clustering-on-a-data-source"></a>Aktivera klustring på en data Källa

Aktivera klustring i `DataSource` klassen genom att ange `cluster` alternativet till sant. Ange `ClusterRadius` om du vill markera närliggande punkter och kombinera dem i ett kluster. Värdet för `ClusterRadius` är i bild punkter. Använd `clusterMaxZoom` för att ange en zoomnings nivå där kluster logiken ska inaktive ras. Här är ett exempel på hur du aktiverar klustring i en data källa.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Om två data punkter ligger nära varandra på marken är det möjligt att klustret aldrig delas upp, oavsett hur nära användaren zoomar in. Du kan åtgärda detta genom att ställa in `clusterMaxZoom` alternativet för att inaktivera kluster logiken och bara Visa allt.

Här följer ytterligare metoder som `DataSource` klassen tillhandahåller för kluster:

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | &lt;Funktions geometri för Promise array &lt; &lt; , vilken &gt; \| form som helst&gt;&gt; | Hämtar underordnade för det aktuella klustret på nästa zoomnings nivå. Dessa underordnade kan vara en kombination av former och under kluster. Under klustren är funktioner med egenskaper som matchar ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Löftes &lt; nummer&gt; | Beräknar en zoomnings nivå där klustret börjar expandera eller dela upp. |
| getClusterLeaves (clusterId: Number, Limit: Number, offset: Number) | &lt;Funktions geometri för Promise array &lt; &lt; , vilken &gt; \| form som helst&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` om du vill returnera en delmängd av punkterna och Använd `offset` sidan till för att gå igenom punkterna. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visa kluster med ett bubbel-lager

Ett bubbeldiagram är ett bra sätt att återge grupperade punkter. Använd uttryck för att skala radien och ändra färgen baserat på antalet punkter i klustret. Om du visar kluster med ett bubbel-lager bör du använda ett separat lager för att återge data punkter som inte är grupperade.

Om du vill visa storleken på klustret ovanpå bubblan använder du ett symbol lager med text och använder inte en ikon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basic Bubble Layer-klustring" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se kluster för rit <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>skiktet Ritstift Basic</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visa kluster med ett symbol lager

När data punkter visualiseras döljs symbol lagret automatiskt symboler som överlappar varandra för att säkerställa ett renare användar gränssnitt. Det här standard beteendet kan vara olämpligt om du vill visa data punkts täthet på kartan. Dessa inställningar kan dock ändras. Om du vill visa alla symboler anger du `allowOverlap` alternativet för symbol lager `iconOptions` egenskapen till `true` . 

Använd klustring för att visa data punkternas densitet samtidigt som ett rent användar gränssnitt hålls kvar. Exemplet nedan visar hur du lägger till anpassade symboler och representerar kluster och enskilda data punkter med hjälp av symbol skiktet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grupperat symbol lager" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se det pennan <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>grupperade symbol skiktet</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Kluster och termisk Maps-skikt

Värme kartor är ett bra sätt att Visa densiteten för data på kartan. Den här visualiserings metoden kan hantera ett stort antal data punkter. Om data punkterna är klustrade och kluster storleken används som värme kartans vikt, kan värme kartan hantera ännu mer data. För att uppnå det här alternativet ställer du in `weight` alternativet för värme kart skiktet till `['get', 'point_count']` . När kluster-radien är liten ser värme kartan nästan identisk med en värme karta med hjälp av de data punkter som inte är klustrade, men den fungerar mycket bättre. Men ju mindre kluster-RADIUS, desto bättre blir värme kartan, men med färre prestanda fördelar.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster viktad termisk karta" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>klustrets viktade värme karta</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mus händelser på klustrade data punkter

När mus händelser inträffar i ett lager som innehåller grupperade data punkter, återgår den klustrade data punkten till händelsen som ett objekt av en interjson-punkt. Den här punkt funktionen kommer att ha följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som kan användas med DataSource `getClusterExpansionZoom` -, `getClusterChildren` -och- `getClusterLeaves` metoder. |
| `point_count`             | antal  | Antalet platser som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som förkortar `point_count` värdet om det är långt. (till exempel 4 000 blir 4K)  |

Det här exemplet tar ett bubbeldiagram som återger kluster punkter och lägger till en klickning-händelse. När du klickar på händelse utlösare, beräknar koden och zoomar in kartan till nästa zoomnings nivå, där klustret delar isär. Den här funktionen implementeras med hjälp av `getClusterExpansionZoom` metoden för `DataSource` klassen och `cluster_id` egenskapen för den klickade klustrade data punkten.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/moZWeV/'>klustrets getClusterExpansionZoom</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visa kluster plats 

De punkt data som ett kluster representerar sprids över ett utrymme. I det här exemplet när musen hovrar över ett kluster inträffar två huvudsakliga beteenden. Först kommer de enskilda data punkterna i klustret att användas för att beräkna en konvex skrov. Sedan visas den konvexa skroven på kartan för att visa ett område.  En konvex skrovet är en polygon som omsluter en uppsättning punkter som ett elastiskt band och kan beräknas med hjälp av `atlas.math.getConvexHull` metoden. Alla punkter som ingår i ett kluster kan hämtas från data källan med hjälp av- `getClusterLeaves` metoden.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kluster Area konvex skrov" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kluster ytan konvext skrov</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregera data i kluster

Kluster representeras ofta med hjälp av en symbol med antalet platser i klustret. Men ibland är det önskvärt att anpassa formatet för kluster med ytterligare mått. Med kluster agg regeringar kan anpassade egenskaper skapas och fyllas i med hjälp av en [mängd uttrycks](data-driven-style-expressions-web-sdk.md#aggregate-expression) beräkning.  Kluster agg regeringar kan definieras i `clusterProperties` alternativ för `DataSource` .

I följande exempel används ett mängd uttryck. Koden beräknar ett antal baserat på egenskapen entitetstyp för varje data punkt i ett kluster. När en användare klickar på ett kluster visas ett popup-fönster med ytterligare information om klustret.

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster mängd" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se mängd med Penn <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>kluster</a> med Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [DataSource-klass](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions-objekt](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [namn område för Atlas. matematik](/javascript/api/azure-maps-control/atlas.math)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)
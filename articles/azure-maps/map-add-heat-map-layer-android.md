---
title: Lägga till ett termiskt kart skikt i Android Maps | Microsoft Azure Maps
description: Lär dig hur du skapar en värme karta. Se hur du använder Azure MapsAndroid SDK för att lägga till ett värme kart skikt till en karta. Lär dig hur du anpassar värme kart skikt.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100193"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Lägg till ett termiskt kart skikt (Android SDK)

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering. De används för att representera densiteten för data med hjälp av en rad färger och visa data "frekventa fläckar" på en karta. Värme kartor är ett bra sätt att rendera data uppsättningar med ett stort antal punkter. 

Åter givning av tusentals punkter som symboler kan avse de flesta av kart områdena. Det här fallet resulterar förmodligen i att många symboler överlappar varandra. Gör det svårt att få bättre förståelse för dina data. Att visualisera samma data uppsättning som en värme karta gör det dock enkelt att se densiteten och den relativa densiteten för varje data punkt.

Du kan använda värme kartor i många olika scenarier, inklusive:

- **Temperatur data**: tillhandahåller ungefärliger för vad temperaturen är mellan två data punkter.
- **Data för buller sensorer**: visar inte bara intensiteten för bruset där sensorn är, men det kan också ge insikter om avtagningen över ett avstånd. Brus nivån på en plats kanske inte är hög. Om brus täcknings området från flera sensorer överlappar varandra, är det möjligt att det överlappande området kan uppleva högre buller nivåer. Därför skulle det överlappande område vara synligt i värme kartan.
- **GPS-spårning**: omfattar hastigheten som en viktad höjd karta där intensiteten för varje data punkt baseras på hastigheten. Den här funktionen är till exempel ett sätt att se var ett fordon går snabbare.

> [!TIP]
> Värme kart skikt återges som standard koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter` alternativet för lagret till `eq(geometryType(), "Point")` . Om du vill inkludera MultiPoint-funktioner anger du även `filter` alternativet för skiktet `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Om du vill rendera en data källa med punkter som en värme karta kan du skicka data källan till en instans av `HeatMapLayer` klassen och lägga till den i kartan.

Följande kod exempel läser in en jord bävningar från den senaste veckan och återger dem som en värme karta. Varje data punkt återges med en radie på 10 bild punkter på alla zoomnings nivåer. För att säkerställa en bättre användar upplevelse är värme kartan under etikett lagret så att etiketterna förblir tydligt synliga. Data i det här exemplet är från [USGS datauppsättningen jord bävning-riskhanterings program](https://earthquake.usgs.gov/). Det här exemplet läser in data från en webbdel från webben med hjälp av kod blocket data import verktyg som finns i [skapa ett dokument för data källa](create-data-source-android-sdk.md) .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

Följande skärm bild visar en karta som läser in en värme karta med ovanstående kod.

![Mappa med värme kart skiktet senaste jord bävningar](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Anpassa värme kart skiktet

I föregående exempel har du anpassat värme kartan genom att ange alternativ för radie och opacitet. Värme kart skiktet innehåller flera alternativ för anpassning, inklusive:

- `heatmapRadius`: Definierar en pixel-radie där varje data punkt ska renderas. Du kan ställa in radien som ett fast tal eller som ett uttryck. Genom att använda ett uttryck kan du skala radien baserat på zoomnivån och representera ett konsekvent rums område på kartan (till exempel en radie på 5 mil).
- `heatmapColor`: Anger hur värme kartan är färgad. En färg toning är en vanlig funktion i värme kartor. Du kan uppnå resultatet med ett `interpolate` uttryck. Du kan också använda ett `step` uttryck för att färgsätta värme kartan och dela upp densiteten i intervall som liknar en profil för konturer eller radar format. Dessa färgpaletter definierar färgerna från minimi värdet till högsta densitet.

  Du anger färg värden för värme Maps som ett uttryck för `heatmapDensity` värdet. Färgen på det områden där det inte finns några data som definieras vid index 0 i "interpolationsmetod"-uttrycket eller standard färgen för ett "stegat"-uttryck. Du kan använda det här värdet för att definiera en bakgrunds färg. Ofta är det här värdet inställt på transparent eller en halv genomskinlig svart. 

  Här är exempel på färg uttryck:

  | Färg uttryck för interpolation | Uttryck för stegvisa färger |
  |--------------------------------|--------------------------|
  | ingå<br/>&nbsp;&nbsp;&nbsp;&nbsp;linjär (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0, färg (färg. TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0,01, färg (färg. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0,5, färg (parseColor ("#fb00fb"))))<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (1, färg (parseColor ("#00c3ff")))<br/>)` | aktivitets<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;färg (färg. TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0,01, färg (parseColor ("#000080"))))<br/>&nbsp;&nbsp;&nbsp;&nbsp;stoppa (0,25, Color (parseColor ("#000080"))))<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0,5, färg (färg. grönt)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (0,5, färg (färg. gul)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stopp (1, färg (färg. röd))<br/>) |

- `heatmapOpacity`: Anger hur ogenomskinligt eller transparent det termiska kart skiktet är.
- `heatmapIntensity`: Tillämpar en multiplikator till vikten för varje data punkt för att öka termisk karta totala intensitet. Det orsakar en skillnad i vikten av data punkter, vilket gör det lättare att visualisera dem.
- `heatmapWeight`: Som standard har alla data punkter en vikt på 1, och viktas lika. Vikt alternativet fungerar som en multiplikator och du kan ange det som ett tal eller ett uttryck. Om ett tal har angetts som vikt, är det likvärdigheten att placera varje data punkt på kartan två gånger. Om vikten till exempel är är `2` densiteten dubbelt. Om du ställer in viktnings alternativet på ett tal återges den termiska kartan på samma sätt som med alternativet intensitet.

  Men om du använder ett uttryck kan vikten för varje data punkt baseras på egenskaperna för varje data punkt. Anta till exempel att varje data punkt representerar en jord bävning. Värdet för storlek har varit ett viktigt mått för varje jord bävning data punkt. Jord bävningar sker hela tiden, men de flesta har låg storlek och har inte märkts. Använd värdet i ett uttryck för att tilldela vikten till varje data punkt. Genom att använda värdet för att tilldela vikten får du en bättre representation av betydelsen av jord bävningar i värme kartan.
- `minZoom` och `maxZoom` : zoomnings nivå intervallet där lagret ska visas.
- `filter`: Ett filter uttryck som används för att begränsa hämtade från källan och återges i lagret.
- `sourceLayer`: Om data källan som är kopplad till lagret är en vektor panels källa måste ett käll skikt i vektor panelerna anges.
- `visible`: Döljer eller visar lagret.

Följande är ett exempel på en termisk karta där ett liner-interpolationsmetod används för att skapa en mjuk färg toning. `mag`Egenskapen som definieras i data används med en exponentiell interpolation för att ange vikten eller relevansen för varje data punkt.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

Följande skärm bild visar det anpassade värme kart skiktet ovan med samma data från det föregående exemplet för termisk karta.

![Mappa med anpassat termiskt kart skikt med senaste jord bävningar](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Konsekvent zoomnings bara termisk karta

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När du zoomar kartan ser data samman varandra och värme kart lagret annorlunda ut. Följande video visar standard beteendet för värme kartan där den har en pixel-radie vid zoomning av kartan.

![Animering som visar en kart zoomning med ett termiskt kart skikt som visar en konsekvent bild punkts storlek](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Använd ett `zoom` uttryck för att skala radien för varje zoomnings nivå, så att varje data punkt täcker samma fysiska område av kartan. Det här uttrycket gör att värme kart lagret ser mer statiskt och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå.

Att skala radien så att den dubbleras med varje zoomnivå skapar en värme karta som ser konsekvent ut på alla zoomnings nivåer. Om du vill använda den här skalningen använder du `zoom` med ett bas-2- `exponential interpolation` uttryck med pixel-radien inställd på minsta zoomnivå och en skalad radie för den högsta zoomnings nivån som `2 * Math.pow(2, minZoom - maxZoom)` visas i följande exempel. Zooma kartan för att se hur värme kartan skalar med zoomnings nivån.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

Följande video visar en karta som kör ovanstående kod, som skalar radien medan kartan zoomas för att skapa en enhetlig kart åter givning på zoomnings nivåer.

![Animering som visar en kart zoomning med ett termiskt kart skikt som visar en konsekvent Geospatial storlek](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Nästa steg

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

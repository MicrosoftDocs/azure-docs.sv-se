---
title: Skapa en data källa för Android Maps | Microsoft Azure Maps
description: 'Ta reda på hur du skapar en data källa för en karta. Lär dig mer om de data källor som Azure Maps Android SDK använder: inaktuella JSON-källor och vektor paneler.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fc68dc25aad3671a55e5c11cbee094b4027e7070
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047680"
---
# <a name="create-a-data-source-android-sdk"></a>Skapa en data källa (Android SDK)

Azure Maps Android SDK lagrar data i data källor. Att använda data källor optimerar data åtgärder för frågor och åter givning. Det finns för närvarande två typer av data Källor:

- **Mål-JSON-källa**: hanterar rå data i det lokala JSON-formatet lokalt. Lämpat för små till medel stora data mängder (flera hundra tusentals former).
- **Vektor panels källa**: läser in data som är formaterade som vektor paneler för den aktuella Map-vyn, baserat på kartans placerings system. Perfekt för stora till enorma data uppsättningar (miljon tals eller miljarder former).

## <a name="geojson-data-source"></a>Data källa för interjson

Azure Maps använder en interjson som en av dess primära data modeller. Interjson är ett öppet, geospatialt standard sätt för att representera geospatiala data i JSON-format. Interjson-klasser som är tillgängliga i Azure Maps Android SDK för att enkelt skapa och serialisera data från en enda JSON-fil. Läs in och lagra data i ett särskilt `DataSource` lager i klassen och återge dem med hjälp av lager. Följande kod visar hur du kan skapa ett interjson-objekt i Azure Maps.

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

Alternativt kan egenskaperna läsas in i en JsonObject först och sedan skickas till funktionen när den skapas, som du ser nedan.

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

När du har skapat en injson-funktion kan du lägga till en data källa i kartan via `sources` mappningens egenskap. Följande kod visar hur du skapar en `DataSource` , lägger till den i kartan och lägger till en funktion i data källan.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

Följande kod visar flera olika sätt att skapa en interjson-funktion, FeatureCollection och Geometries.

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>Serialisera och deserialisera en multijson

Funktionerna Feature, Feature och Geometry har alla `fromJson()` och `toJson()` statiska metoder, som hjälper dig med serialisering. Den formaterade giltiga JSON-strängen som skickas via `fromJson()` metoden skapar objektet Geometry. Den här `fromJson()` metoden innebär också att du kan använda Gson eller andra strategier för serialisering/deserialisering. Följande kod visar hur du tar en stringified-multijson-funktion och deserialiserar den till funktions klassen, och sedan serialiserar du tillbaka den till en injson-sträng.

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importera data för en webbdel från en webbplats eller till gångar

De flesta polyjson-filer innehåller en FeatureCollection. Läsa in-JSON-filer som strängar och Använd `FeatureCollection.fromJson` metoden för att deserialisera den.

Följande kod är en återanvändbar klass för att importera data från webb-eller lokala till gångar-mappen som en sträng och returnera den till UI-tråden via en callback-funktion.

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

Koden nedan visar hur du använder det här verktyget för att importera data från en data sträng och returnera dem till UI-tråden via ett återanrop. I återanropet kan sträng data serialiseras till en samling med injson-funktioner och läggas till i data källan. Du kan också uppdatera Maps-kameran för att fokusera på data.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>Vektor panels källa

En vektor panels källa beskriver hur du får åtkomst till ett vektor panels lager. Använd `VectorTileSource` klassen för att instansiera en vektor panels källa. Vektor panels lager liknar panel lager, men de är inte samma. Ett panel lager är en raster bild. Vektor panels lager är en komprimerad fil i **PBF** -format. Den här komprimerade filen innehåller vektor kart data och ett eller flera lager. Filen kan återges och formateras på klienten, baserat på formatet för varje lager. Data i en vektor panel innehåller geografiska funktioner i form av punkter, linjer och polygoner. Det finns flera fördelar med att använda vektor panels lager i stället för raster panels lager:

- En fil storlek på en vektor panel är vanligt vis mycket mindre än en motsvarande raster panel. Som sådan används mindre bandbredd. Det innebär kortare svars tid, en snabbare karta och en bättre användar upplevelse.
- Eftersom vektor paneler återges på klienten, anpassas de till lösning av enheten som de visas på. Resultatet blir att de åter givnings kartorna visas mer väldefinierade med Crystal Clear-etiketter.
- Att ändra formatet för data i vektor Maps kräver inte att data hämtas igen, eftersom det nya formatet kan tillämpas på klienten. Att ändra formatet på ett raster panels lager kräver däremot vanligt vis inläsning av paneler från servern och sedan att använda det nya formatet.
- Eftersom data levereras i vektor form krävs mindre bearbetning på Server sidan för att förbereda data. Därför kan nya data göras tillgängliga snabbare.

Azure Maps följer [specifikationen Mapbox Vector panel](https://github.com/mapbox/vector-tile-spec), en öppen standard. Azure Maps tillhandahåller följande tjänster för vektor paneler som en del av plattformen:

- Information om [](/rest/api/maps/renderv2/getmaptilepreview)  |  [data format](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) för Road panels dokumentation
- Information om trafik incidenter [dokumentation](/rest/api/maps/traffic/gettrafficincidenttile)  |  [data format](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- [](/rest/api/maps/traffic/gettrafficflowtile)  |  [Information om data format](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) för trafikflödets dokumentation
- Med Azure Maps Creator kan du också skapa och få till gång till anpassade vektor paneler via [Get panel rendering v2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> När du använder vektor-eller raster bild rutor från tjänsten Azure Maps Render med webb-SDK kan du ersätta `atlas.microsoft.com` med plats hållaren `azmapsdomain.invalid` . Plats hållaren ersätts med samma domän som används av kartan och lägger automatiskt till samma autentiseringsinformation. Detta fören klar autentiseringen med Render-tjänsten avsevärt när du använder Azure Active Directory autentisering.

Om du vill visa data från en vektor panels källa på kartan ansluter du källan till en av data åter givnings skikten. Alla lager som använder en Vector-källa måste ange ett `sourceLayer` värde i alternativen. Följande kod läser in tjänsten Azure Maps Traffic Flow Vector panel som en vektor panels källa. därefter visas den på en karta med ett linje lager. Den här vektor panels källan har en enda uppsättning data i käll skiktet som kallas "trafikflöde". Rad data i den här data uppsättningen har en egenskap `traffic_level` som kallas som används i den här koden för att välja färg och skala storleken på linjerna.

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![Mappa med färgkodade väg linjer som visar trafik flödes nivåer](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Ansluta en data källa till ett lager

Data återges på kartan med åter givnings lager. En enskild data källa kan refereras till av en eller flera åter givnings skikt. Följande åter givnings skikt kräver en data Källa:

- [Bubbel Layer](map-add-bubble-layer-android.md) – återger punkt data som skalade cirklar på kartan.
- [Symbol lager](how-to-add-symbol-to-android-map.md) – återger punkt data som ikoner eller text.
- [Termiskt kart skikt](map-add-heat-map-layer-android.md) – återger punkt data som en densitets värme karta.
- [Linje lager](android-map-add-line-layer.md) – återge en linje och eller återge konturen för polygoner.
- [Polygon-lager](how-to-add-shapes-to-android-map.md) – fyller ytan i en polygon med ett mönster för en solid färg eller bild.

Följande kod visar hur du skapar en data källa, lägger till den i kartan och kopplar den till ett bubbeldiagram. Och importera sedan data från en fjärran sluten plats till data källan.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Det finns ytterligare åter givnings skikt som inte ansluter till dessa data källor, men som läser in data direkt för åter givning.

- [Panel lager](how-to-add-tile-layer-android-map.md) – superdriver ett raster panels lager ovanpå kartan.

## <a name="one-data-source-with-multiple-layers"></a>En data källa med flera lager

Flera lager kan anslutas till en enda data källa. Det finns många olika scenarier där det här alternativet är användbart. Anta till exempel scenariot där en användare ritar en polygon. Vi ska återge och fylla i polygon-ytan när användaren lägger till punkter i kartan. Om du lägger till en formaterad linje för att disponera polygonen blir det enklare att se polygonens kanter när användaren ritar. För att enkelt redigera en enskild position i polygonen kan vi lägga till en referens, t. ex. en PIN-kod eller en markör, ovanför varje position.

![Kartning visar flera nivåer som återger data från en enskild data Källa](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

I de flesta mappnings plattformar skulle du behöva ett polygon-objekt, ett linje objekt och en PIN-kod för varje position i polygonen. När polygonen ändras måste du uppdatera linjen och PIN-fälten manuellt, vilket snabbt kan bli komplext.

Med Azure Maps måste allt du behöver vara en enda polygon i en data källa som visas i koden nedan.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> När du lägger till lager till kartan med hjälp av `map.layers.add` -metoden kan ID eller instans av ett befintligt lager skickas som en andra parameter. Detta skulle berätta för kartan att infoga det nya lagret som läggs till under det befintliga lagret. I utöver för att överföra ett lager-ID, stöder den här metoden även följande värden.
>
> - `"labels"` -Infogar det nya lagret under kart etikettens lager.
> - `"transit"` -Infogar det nya lagret under kart vägs-och transport lager.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägg till en värme karta](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Kod exempel för webb-SDK](/samples/browse/?products=azure-maps)
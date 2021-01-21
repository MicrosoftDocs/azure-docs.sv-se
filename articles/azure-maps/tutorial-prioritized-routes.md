---
title: 'Självstudie: Sök efter flera vägar i färd läge | Microsoft Azure Maps'
description: Själv studie kurs om hur du använder Azure Maps för att hitta vägar för specifika rese lägen till intressanta punkter. Se hur du visar flera vägar på Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: dbf2331dc6bcd3187175554b61d347e8bb2c04a4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625006"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Självstudie: hitta och Visa vägar för olika färd sätt med hjälp av Azure Maps

I den här självstudien får du lära dig hur du använder Azure Maps [Route service](/rest/api/maps/route) och [kart kontroll](./how-to-use-map-control.md) för att Visa väg riktningarna för både privata fordon och nytto Last bilar med `USHazmatClass2` Last typ. Dessutom vägleder vi dig genom hur du visualiserar trafik data i real tid på en karta. I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa och Visa kart kontrollen på en webb sida
> * Återge trafik data i real tid på en karta
> * Begära och Visa privata och kommersiella fordons vägar på en karta

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

Du kan hämta den fullständiga käll koden för exemplet [här](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Du hittar ett Live-exempel [här](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Skapa en ny webbsida med API:n för kartkontroll

Följande steg visar hur du skapar och visar kart kontrollen på en webb sida.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapTruckRoute.html**.
2. Kopiera/klistra in följande HTML-kod i filen.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

     HTML-huvudet innehåller CSS-och JavaScript-resursfiler som finns i Azure Kartkontroll-biblioteket. Bröd textens `onload` händelse anropar `GetMap` funktionen. I nästa steg ska vi lägga till initierings koden för kart kontrollen.

3. Lägg till följande JavaScript-kod i funktionen `GetMap`. Ersätt strängen `<Your Azure Maps Key>` med den primära nyckel som du kopierade från ditt Maps-konto.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. Spara filen och öppna den i webbläsaren. En enkel visas.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Grundläggande kart åter givning av kart kontroll":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Återge trafik data i real tid på en karta

1. Lägg till följande JavaScript-kod i `GetMap` funktionen. Den här koden implementerar kart kontrollens `ready` händelse hanterare. Resten av koden i den här självstudien placeras i `ready` händelse hanteraren.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    I kartans `ready` händelse hanterare anges trafik flödes inställningen på kartan `relative` , vilket är hastigheten på vägen i förhållande till det fria flödet. Mer trafik alternativ finns i [TrafficOptions-gränssnittet](/javascript/api/azure-maps-control/atlas.trafficoptions?preserve-view=false&view=azure-maps-typescript-latest).

2. Spara filen **MapTruckRoute.html** och uppdatera sidan i webbläsaren. Om du zoomar in i någon stad, t. ex. Los Angeles, ser du att gator visas med aktuella trafikflödes data.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Visa trafik på en karta":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definiera rendering av flödes visning

I den här självstudien beräknas och renderas två vägar på kartan. Den första vägen kommer att beräknas för ett privat fordon (Car). Den andra vägen kommer att beräknas för en kommersiell vehikel (Truck) för att visa skillnaden mellan resultaten. När kartan visas visas en symbol ikon för vägens start-och slut punkter och Geometries med olika färger för varje väg Sök väg. Mer information om hur du lägger till linje lager finns i [lägga till ett linje lager till en karta](map-add-line-layer.md). Mer information om symbol lager finns i [lägga till ett symbol lager till en karta](map-add-pin.md).

1. `ready`Lägg till följande kod i kart kontrollens händelse hanterare.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round'
    }), 'labels');

    //Add a layer for rendering point data.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: ['get', 'icon'],
            allowOverlap: true
        },
        textOptions: {
            textField: ['get', 'title'],
            offset: [0, 1.2]
        },
        filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
    }));

    ```


    I kart kontrollens `ready` händelse hanterare skapas en data källa som lagrar vägen från början till slut. [Uttryck](data-driven-style-expressions-web-sdk.md) används för att hämta linje bredd och färg från egenskaper på väg linje funktionen. För att se till att flödes linjen inte visar väg etiketterna har vi skickat en andra parameter med värdet `'labels'` .

    Därefter skapas ett symbol lager som är kopplat till data källan. Det här lagret anger hur start-och slut punkterna återges. Uttryck har lagts till för att hämta ikon bilden och text etiketts information från egenskaper för varje punkt objekt. Mer information om uttryck finns i [uttryck för data drivna format](data-driven-style-expressions-web-sdk.md).

2. Ange start punkten som ett fiktivt företag i Seattle som heter Fabrikam, och slut punkten som ett Microsoft Office.  `ready`Lägg till följande kod i kart kontrollens händelse hanterare.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Den här koden skapar två [objekt av punkt-JSON-plats](https://en.wikipedia.org/wiki/GeoJSON) som representerar start-och slut punkter, som sedan läggs till i data källan.

    Det sista blocket i kod ställer in kameravy med latitud och longitud för start-och slut punkterna. Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Den här markerings rutan används för att ställa in kart kameror för visning över hela vägen med hjälp av `map.setCamera` funktionen. Utfyllnad läggs till för att kompensera pixel måtten för symbol ikonerna. Mer information om kart kontrollens egenskap setCamera finns i [setCamera (CameraOptions | Egenskapen CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) .

3. Spara **TruckRoute.html** och uppdatera webbläsaren. Kartan centreras nu över Seattle. Den blå Teardrop-PIN-koden markerar start punkten. Den runda blå PIN-koden markerar slut punkten.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Visa karta med start- och slutpunkter":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Begära och Visa privata och kommersiella fordons vägar på en karta

Det här avsnittet visar hur du använder tjänsten Azure Maps Route för att få vägvisningar från en punkt till en annan, baserat på ditt transport sätt. Vi använder två transport sätt: Truck och bil.

>[!TIP]
>Route service innehåller API: er för att planera *snabbast*, *kortaste*, *eko* eller *thrilLing* vägar baserat på avstånd, trafik villkor och transport läge. Tjänsten låter också användare planera framtida vägar baserat på historiska trafik villkor. Användarna kan se förutsägelsen av väg varaktigheter för en bestämd tid. Mer information finns i [Hämta väg riktnings-API](/rest/api/maps/route/getroutedirections).

1. I `GetMap` -funktionen, i kontrollens `ready` händelse hanterare, lägger du till följande i JavaScript-koden.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Skapar en `SubscriptionKeyCredentialPolicy` för att autentisera HTTP-begäranden till Azure Maps med prenumerations nyckeln. `atlas.service.MapsURL.newPipeline()`Principen tar i `SubscriptionKeyCredential` principen och skapar en [pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) -instans. `routeURL`Representerar en URL som Azure Maps [väg](/rest/api/maps/route) åtgärder.

2. När du har angett autentiseringsuppgifter och URL: en lägger du till följande JavaScript-kod för att skapa en väg väg för en truck från start till slut punkt. Den här vägen skapas och visas för en truck som bär `USHazmatClass2` klassa Last.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Koden ovan skickar frågor till Azure Maps Route-tjänsten via [API: et för väg riktningar i Azure Maps](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). Flödes raden extraheras sedan från insamlingen för polyjson-funktionen från svaret som extraheras med hjälp av `geojson.getFeatures()` metoden. Slutligen läggs väg linjen till i data källan. Vi lägger till den i indexet 0, för att säkerställa att trucken renderas före andra rader i data källan, eftersom beräkningen av Last bil flödet ofta är långsammare än en flödes beräkning. Om väg linjen för trucken läggs till i data källan efter Car-vägen, kommer den att visas ovanför den. Två egenskaper läggs till i Truck-väg linjen: en blå linje färg och en linje bredd på nio bild punkter.

    >[!TIP]
    > Om du vill se alla möjliga alternativ och värden för API: et för Azure Maps väg riktningar, se [URI-parametrar för post riktningar](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Lägg nu till följande JavaScript-kod för att skapa en väg för en bil.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    Koden ovan frågar Azure Maps Routningstjänst via API-metoden för  [Azure Maps väg riktning](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) . Flödes raden extraheras sedan från insamlingen för polyjson-funktionen från svaret som extraheras med hjälp av `geojson.getFeatures()` metoden. Slutligen läggs väg linjen till i data källan. Två egenskaper läggs till i Truck-väg linjen: en lila linje färg och en linje bredd på fem bild punkter.

4. Spara **TruckRoute.html** -filen och uppdatera din webbläsare. Kartan bör nu Visa trucken och Car-vägarna.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Privata och kommersiella fordons vägar på en karta med Azure Route Service":::

    Truck-vägen visas med en tjock blå linje. Car-vägen visas med en tunn lila linje. Bilen går över Lake Washington via I-90 och passerar tunnlar under bostads områden. Eftersom tunnlarna ligger nära bostads områden är farligt avfall Last begränsat. Truck-vägen, som anger en `USHazmatClass2` Last typ, dirigeras för att använda en annan väg.

Du kan hämta den fullständiga käll koden för exemplet [här](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Du hittar ett Live-exempel [här](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

Du kan också [använda data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Nästa självstudiekurs visar processen med att skapa en enkel butikslokaliserare med hjälp av Azure Maps.

> [!div class="nextstepaction"]
> [Skapa en butikslokaliserare med hjälp av Azure Maps](./tutorial-create-store-locator.md)
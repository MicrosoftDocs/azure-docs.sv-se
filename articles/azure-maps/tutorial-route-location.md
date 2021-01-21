---
title: 'Självstudie: hitta en väg till en plats | Microsoft Azure Maps'
description: Själv studie kurs om hur du hittar en väg till en orienterings punkt. Se hur du ställer in adress koordinater och skickar frågor till tjänsten Azure Maps Route för vägbeskrivning till punkten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d8aaf093a7e2a0ad1470d72d815e919f5b4e2d05
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624990"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Självstudie: så här visar du väg riktningar med Azure Maps Route service och kart kontroll

Den här självstudien visar hur du använder [API för Azure Maps Route service](/rest/api/maps/route) och [kart kontroll](./how-to-use-map-control.md) för att Visa väg riktningar från start till slut punkt. I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa och Visa kart kontrollen på en webb sida. 
> * Definiera visnings åter givning för vägen genom att definiera [symbol lager](map-add-pin.md) och [linje lager](map-add-line-layer.md).
> * Skapa och Lägg till interjson-objekt till kartan som representerar start-och slut punkter.
> * Få vägvisningar från start-och slut punkter med hjälp av API: et för att [Hämta väg riktningar](/rest/api/maps/route/getroutedirections).

Du kan hämta den fullständiga käll koden för exemplet [här](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Du hittar ett Live-exempel [här](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Skapa och Visa kart kontrollen

Följande steg visar hur du skapar och visar kart kontrollen på en webb sida.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapRoute.html**.
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Grundläggande kart åter givning av kart kontroll":::

## <a name="define-route-display-rendering"></a>Definiera rendering av flödes visning

I den här självstudien återger vi vägen med ett linje lager. Start-och slut punkterna kommer att renderas med ett symbol lager. Mer information om hur du lägger till linje lager finns i [lägga till ett linje lager till en karta](map-add-line-layer.md). Mer information om symbol lager finns i [lägga till ett symbol lager till en karta](map-add-pin.md).

1. Lägg till följande JavaScript-kod i `GetMap` funktionen. Den här koden implementerar kart kontrollens `ready` händelse hanterare. Resten av koden i den här självstudien placeras i `ready` händelse hanteraren.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    });
    ```

    I kart kontrollens `ready` händelse hanterare skapas en data källa för att lagra vägen från start till slut punkt. För att definiera hur väg linjen ska återges skapas ett linje lager och kopplas till data källan.  För att se till att flödes linjen inte visar väg etiketterna har vi skickat en andra parameter med värdet `'labels'` .

    Därefter skapas ett symbol lager som är kopplat till data källan. Det här lagret anger hur start-och slut punkterna återges. Uttryck har lagts till för att hämta ikon bilden och text etiketts information från egenskaper för varje punkt objekt. Mer information om uttryck finns i [uttryck för data drivna format](data-driven-style-expressions-web-sdk.md).

2. Ange start punkten som Microsoft och slut punkten som en gas station i Seattle.  `ready`Lägg till följande kod i kart kontrollens händelse hanterare.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Den här koden skapar två [objekt av punkt-JSON-plats](https://en.wikipedia.org/wiki/GeoJSON) som representerar start-och slut punkter, som sedan läggs till i data källan. 

    Det sista blocket i kod ställer in kameravy med latitud och longitud för start-och slut punkterna. Start- och slutpunkterna läggs till i datakällan. Avgränsningsfältet för start- och slutpunkterna beräknas med hjälp av funktionen `atlas.data.BoundingBox.fromData`. Den här markerings rutan används för att ställa in kart kameror för visning över hela vägen med hjälp av `map.setCamera` funktionen. Utfyllnad läggs till för att kompensera pixel måtten för symbol ikonerna. Mer information om kart kontrollens egenskap setCamera finns i [setCamera (CameraOptions | Egenskapen CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) .

3. Spara **MapRoute.html** och uppdatera webbläsaren. Kartan centreras nu över Seattle. Den blå Teardrop-PIN-koden markerar start punkten. Den runda blå PIN-koden markerar slut punkten.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Visa vägarnas start-och slut punkt på kartan":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Hämta väg riktningar

Det här avsnittet visar hur du använder API: et för Azure Maps väg riktningar för att få väg riktningar och den uppskattade ankomst tiden från en punkt till en annan.

>[!TIP]
>Azure Maps Route Services erbjuder API: er för att planera vägar baserat på olika typer av vägar, till exempel *snabbast*, *kortaste*, *eko* eller *thrilLing* vägar baserat på avstånd, trafik villkor och transport läge. Tjänsten låter också användare planera framtida vägar baserat på historiska trafik villkor. Användarna kan se förutsägelsen av väg varaktigheter för en bestämd tid. Mer information finns i [Hämta väg riktnings-API](/rest/api/maps/route/getroutedirections).

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

2. När du har angett autentiseringsuppgifter och URL: en lägger du till följande kod i kontrollens `ready` händelse hanterare. Den här koden skapar vägen från start punkt till slut punkt. `routeURL`Begär API: et för Azure Maps Route service för att beräkna väg riktningar. En insamling av en interjson-funktion från svaret extraheras sedan med hjälp av `geojson.getFeatures()` metoden och läggs till i data källan.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Spara filen **MapRoute.html** och uppdatera webbläsaren. Kartan ska nu visa vägen från start till slut punkt.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Azure kart kontroll och Route service":::

Du kan hämta den fullständiga käll koden för exemplet [här](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Du hittar ett Live-exempel [här](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Nästa själv studie kurs visar hur du skapar en väg fråga med begränsningar, t. ex. rese-eller typ av Last. Du kan sedan Visa flera vägar på samma karta.

> [!div class="nextstepaction"]
> [Hitta rutter för olika färdmedel](./tutorial-prioritized-routes.md)
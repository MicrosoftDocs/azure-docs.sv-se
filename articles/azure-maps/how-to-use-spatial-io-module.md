---
title: Så här använder du den Azure Maps spatiala i/o-modulen | Microsoft Azure Maps
description: Lär dig hur du använder den spatiala IO-modul som tillhandahålls av Azure Maps Web SDK. Den här modulen ger robusta funktioner som gör det enkelt för utvecklare att integrera spatialdata med Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680686"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Använda den Azure Maps spatiala IO-modulen

Azure Maps Web SDK tillhandahåller den **spatiala IO-modulen**, som integrerar spatialdata med Azure Maps webb-SDK med hjälp av Java Script eller typescript. Med robusta funktioner i den här modulen kan utvecklare:

- [Läs och skriv filer för vanliga spatialdata](spatial-io-read-write-spatial-data.md). Fil format som stöds är: KML, KMZ, GPX, GeoRSS, GML, interjson-och CSV-filer som innehåller kolumner med spatial information. Stöder också Well-Known text (well).
- [Anslut till Open Geospatial Consortium-tjänster (OGC) och integrera med Azure Maps Web SDK. Täcka in webb kart tjänster (WMS) och webb kart panels tjänster (WMTS) som lager på kartan](spatial-io-add-ogc-map-layer.md).
- [Fråga efter data i en webb funktions tjänst (WFS)](spatial-io-connect-wfs-service.md).
- [Lägg till komplexa data uppsättningar som innehåller format information och återger dem automatiskt med minimal kod](spatial-io-add-simple-data-layer.md).
- [Utnyttja höghastighets-och avgränsade fil läsare och skrivar klasser](spatial-io-core-operations.md).

I den här guiden får vi lära dig hur du integrerar och använder den spatiala IO-modulen i ett webb program.

Den här videon ger en översikt över den spatiala IO-modulen i Azure Maps Web SDK.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> Använd endast data och tjänster från en källa som du litar på, särskilt om du refererar till den från en annan domän. Den spatiala i/o-modulen vidtar åtgärder för att minimera risken, men den säkraste metoden är för att inte tillåta att danagerous-data i ditt program börjar med. 

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda den spatiala IO-modulen måste du [skapa ett Azure Maps konto](./quick-demo-map-app.md#create-an-azure-maps-account) och [Hämta den primära prenumerations nyckeln för ditt konto](./quick-demo-map-app.md#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Installera den spatiala IO-modulen

Du kan läsa in den Azure Maps spatiala IO-modulen med något av de två alternativen:

* Den globalt värdbaserade Azure CDN för den Azure Maps spatiala IO-modulen. För det här alternativet lägger du till en referens till java script i `<head>` HTML-filens element.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Käll koden för [Azure-Maps-spatial-IO](https://www.npmjs.com/package/azure-maps-spatial-io) kan läsas in lokalt och sedan vara värd för din app. Det här paketet innehåller även TypeScript-definitioner. För det här alternativet använder du följande kommando för att installera paketet:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Lägg sedan till en referens till java script i `<head>` elementet i HTML-dokumentet:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Använda den spatiala IO-modulen

1. Skapa en ny HTML-fil.

2. Läs in Azure Maps Web SDK och initiera kart kontrollen. Mer information finns i [Azure Maps kartans kontroll](./how-to-use-map-control.md) guide. När du är klar med det här steget bör HTML-filen se ut så här:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Läs in Azure Maps spatial-IO-modul. I den här övningen använder du CDN för den Azure Maps spatiala IO-modulen. Lägg till referensen nedan till `<head>` elementet i HTML-filen:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Initiera en `datasource` och Lägg till data källan till kartan. Initiera en `layer` och Lägg till data källan till kart skiktet. Sedan återger du både data källan och lagret. Innan du bläddrar nedåt för att se den fullständiga koden i nästa steg bör du tänka på de bästa platserna för att placera data källan och lager kods tycken. Kom ihåg att innan vi program mässigt ändrar kartan bör vi vänta tills kart resursen är klar.

    ```javascript
    var datasource, layer;
    ```

    och

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Genom att sätta ihop allt, bör din HTML-kod se ut som i följande kod. Det här exemplet visar hur du läser en XML-fil från en URL. Läs sedan in och Visa filens funktions data på kartan. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Kom ihåg att ersätta `<Your Azure Maps Key>` med den primära nyckeln. Öppna HTML-filen så ser du resultat som liknar följande bild:

    <center>

    ![Exempel på spatialdata](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Nästa steg

Funktionen som vi demonstrerar här är bara en av de många funktionerna som är tillgängliga i den spatiala IO-modulen. Läs rikt linjerna nedan för att lära dig hur du använder andra funktioner i den spatiala i/o-modulen:

> [!div class="nextstepaction"]
> [Lägga till ett enkelt datalager](spatial-io-add-simple-data-layer.md)

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

Läs dokumentationen om Azure Maps spatial IO:

> [!div class="nextstepaction"]
> [Azure Maps spatialt IO-paket](/javascript/api/azure-maps-spatial-io/)
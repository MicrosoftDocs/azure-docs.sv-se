---
title: 'Självstudie: Sök efter närliggande platser på en karta | Microsoft Azure Maps'
description: Själv studie kurs om hur du söker efter intressanta punkter på en karta. Se hur du använder Azure Maps Web SDK för att lägga till Sök funktioner och interaktiva popup-rutor i en karta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624974"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Självstudie: Sök efter intresse punkter i närheten med hjälp av Azure Maps

Den här självstudiekursen visar hur du skapar ett konto med Azure Maps och sedan använder API:er för Maps för att söka efter en orienteringspunkt. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Maps-konto
> * Hämta primärnyckeln för ditt Maps-konto
> * Skapa en ny webbsida med API:n för kartkontroll
> * Använda Maps-söktjänsten för att hitta orienteringspunkter i närheten

## <a name="prerequisites"></a>Förutsättningar

<a id="createaccount"></a>
<a id="getkey"></a>

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Skapa en ny karta

Kartkontroll API är ett användbart klient bibliotek. Med det här API: et kan du enkelt integrera kartor i ditt webb program. Det döljer komplexiteten hos inga REST service-anrop och ökar produktiviteten med anpassningsbara komponenter. Följande steg visar hur du skapar en statisk HTML-sida inbäddad med API:et Kartkontroll.

1. Skapa en ny fil på den lokala datorn och ge den namnet **MapSearch.html**.
2. Lägg till följande HTML-komponenter i filen:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Observera att HTML-huvudet innehåller CSS- och JavaScriptresursfiler som med Azure Kartkontroll-biblioteket som värd. Observera `onload`-händelsen i innehållet på sidan, som anropar funktionen `GetMap` när sidans innehåll har lästs in. `GetMap`Funktionen kommer att innehålla den infogade JavaScript-koden för att komma åt Azure Maps-API: er.

3. Lägg till följande JavaScript-kod i HTML-filens `GetMap`-funktion. Ersätt strängen `<Your Azure Maps Key>` med den primära nyckel som du kopierade från ditt Maps-konto.

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

   Det här segmentet initierar API:et Kartkontroll för din Azure Maps-kontonyckel. `atlas` är det namn område som innehåller API: et och relaterade visuella komponenter. `atlas.Map` ger kontrollen för en visuell och interaktiv webb karta.

4. Spara dina ändringar i filen och öppna HTML-sidan i en webbläsare. Kartan som visas är den mest grundläggande kartan som du kan göra genom att anropa `atlas.Map` med din konto nyckel.

   ![Visa kartan](./media/tutorial-search-location/basic-map.png)

5. I funktionen `GetMap`, när du har initierat kartan, lägger du till följande JavaScript-kod.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   I det här kod segmentet läggs en händelse till i `ready` kartan, som aktive ras när mappnings resurserna har lästs in och kartan är klar att nås. I kartans `ready` händelse hanterare skapas en data källa för att lagra resultat data. Ett symbollager skapas och ansluts till datakällan. Det här lagret anger hur resultat data i data källan ska återges. I det här fallet återges resultatet med en mörkblå, blå, runt-ikon, centrerad över resultat koordinaten och låter andra ikoner överlappa varandra. Resultat lagret läggs till i kart skikten.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Lägga till sökfunktioner

Det här avsnittet visar hur du använder [Sök-API: t](/rest/api/maps/search) kartor för att hitta en orienterings punkt på kartan. Det är ett RESTful-API för utvecklare för att söka efter adresser, orienteringspunkter och annan geografisk information. Search Service tilldelar en angiven adress latitud- och longitudinformation. **Tjänstemodulen** som beskrivs nedan kan användas till att söka efter en plats med hjälp av API:et för kartsökning.

### <a name="service-module"></a>Tjänstmodul

1. I kartans `ready` händelse hanterare skapar du URL: en för Sök tjänsten genom att lägga till följande JavaScript-kod.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential`Skapar en `SubscriptionKeyCredentialPolicy` för att autentisera HTTP-begäranden till Azure Maps med prenumerations nyckeln. `atlas.service.MapsURL.newPipeline()`Principen tar i `SubscriptionKeyCredential` principen och skapar en [pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) -instans. `searchURL`Visar en URL som Azure Maps [Sök](/rest/api/maps/search) åtgärder.

2. Lägg därefter till följande skriptblock för att skapa sökfrågan. Den använder det enkla söknings-API:et i Search Service, som kallas Fuzzy Search. Fuzzy Search-tjänsten hanterar de flesta fuzzy-indata som adresser, platser och platser av intresse (POI). Den här koden söker efter i närheten bensin stationer inom den angivna radien för den angivna latitud och longitud. En samling av en interjson-funktion från svaret extraheras sedan med hjälp av `geojson.getFeatures()` metoden och läggs till i data källan, vilket automatiskt resulterar i att de data som återges på kartan överförs via symbol lagret. Den sista delen av skriptet ställer in kameravyn med hjälp av avgränsningsrektangeln för resultat med kartans [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)-egenskap.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Spara filen **MapSearch.html** och uppdatera webbläsaren. Du bör se kartan centrerad på Seattle med runda blå PIN-områden för platser hos bensin stationer i-ytan.

   ![Visa kartan med sökresultat](./media/tutorial-search-location/pins-map.png)

4. Du kan visa rådata som kartan återger genom att ange följande HTTP-förfrågan i webbläsaren. Ersätt \<Your Azure Maps Key\> med din primär nyckel.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

I det här läget kan MapSearch-sidan visa orienteringspunkterna som returneras från en fuzzy-sökfråga. Lägg till vissa interaktiva funktioner och mer information om platser.

## <a name="add-interactive-data"></a>Lägga till interaktiva data

Karta som har vi gjort tittar hittills bara på longitud-/latituddata för sökresultaten. Men RAW JSON som Maps Search-tjänsten returnerar innehåller ytterligare information om varje gas Station. Inklusive namn och gatuadress. Du kan införliva dessa data i kartan med interaktiva popup-rutor.

1. Lägg till följande rader med kod i kartans `ready` händelse hanterare efter koden för att skicka frågor till fuzzy Search-tjänsten. Den här koden skapar en instans av en popup och lägger till en mouseOver-händelse i symbol skiktet.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API: et `*atlas.Popup` tillhandahåller ett informations fönster som fästs på den plats som krävs på kartan. 

2. Lägg till följande kod i `GetMap` funktionen om du vill visa den mus visade resultat informationen i popup-fönstret.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Spara filen och uppdatera webbläsaren. Nu visar kartan i webbläsaren när du hovrar över någon av sökningskartnålarna.

    ![Azure Kartkontroll och Search Service](./media/tutorial-search-location/popup-map.png)

Klicka [här](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)om du vill visa den fullständiga koden för den här självstudien. Klicka [här](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest) om du vill visa Live-exemplet

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Nästa självstudie demonstrerar hur du visar en väg mellan två platser.

> [!div class="nextstepaction"]
> [Dirigera mot ett mål](./tutorial-route-location.md)
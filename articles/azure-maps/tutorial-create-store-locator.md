---
title: 'Självstudie: skapa ett program för Store Locator med Azure Maps | Microsoft Azure Maps'
description: Själv studie kurs om hur du skapar webb program för Store Locator. Använd Azure Maps Web SDK för att skapa en webb sida, fråga Sök tjänsten och Visa resultat på en karta.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 801c2fe1710952a12584bf10dd8e5c77de3b839c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625105"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Självstudie: skapa en Store-lokaliserare med hjälp av Azure Maps

Den här självstudien vägleder dig genom processen med att skapa en enkel butikslokaliserare med hjälp av Azure Maps. Butikslokaliserare är vanliga. Många av de begrepp som används i den här typen av program är tillämpliga på många andra typer av program. En butikslokaliserare för kunder är ett måste för de flesta företag som säljer direkt till konsumenter. I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa en ny webbsida med API:et Azure Kartkontroll.
> * Läs in anpassade data från en fil och visa dem på en karta.
> * Använd Azure Maps Search-tjänsten för att hitta en adress eller ange en fråga.
> * Hämta användarens plats från webbläsaren och visa den på kartan.
> * Kombinera flera lager för att skapa anpassade symboler på kartan.  
> * Klusterdatapunkter.  
> * Lägg till zoomkontroller på kartan.

<a id="Intro"></a>

Gå vidare till [exemplet på livebutikslokaliserare](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) eller [källkoden](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator).

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto med pris nivån S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

## <a name="design"></a>Design

Innan du sätter igång i koden är det en bra idé att börja med en design. Din butikslokaliserare kan vara så enkel eller komplex som du vill. I den här självstudien skapar vi en enkel butikslokaliserare. Vi tar några tips på vägen för att hjälpa dig att utöka vissa funktioner om du vill. Vi skapar en butikslokaliserare för det fiktiva företaget Contoso Coffee. Följande bild visar ett trådblock i en allmän layout för butikslokaliserarestore som vi skapar i den här självstudien:

![Tråd block för ett Store Locator-program för Contoso kaffe Shop-platser](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)

För att maximera butikslokaliserarens användbarhet använder vi en dynamisk layout som anpassar sig när en användares skärmbredd är mindre än 700 bildpunkter. En dynamisk layout gör det enklare att använda butikslokaliseraren på en liten skärm, som på en mobil enhet. Här är ett trådblock i en layout för liten skärm:  

![Tråd block för Contoso kaffe Store Locator-appen på en mobil enhet](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</

Trådblocken visar ett ganska enkelt program. Programmet har en sökruta, en lista över närliggande butiker och en karta som har vissa markörer, till exempel symboler. Och har ett popup-fönster som visar ytterligare information när användaren väljer en markör. Närmare bestämt följer här de funktioner som vi bygger in i den här butikslokaliseraren i den här självstudien:

* Alla platser från den importerade tabbavgränsade datafilen har lästs in på kartan.
* Användaren kan panorera och zooma på kartan, söka och välja knappen My Location GPS (Min plats-GPS).
* Sidlayouten justeras baserat på bredden på enhetens skärm.  
* En rubrik visar en logotyp.  
* Användaren kan använda en sökruta och sökknapp för att söka efter en plats, till exempel en adress, postnummer eller ort. 
* En `keypress`-händelse som läggs till sökrutan utlöser en sökning om användaren trycker på RETUR. Den här funktionen förbises ofta, men det skapar en bättre användarupplevelse.
* När kartan rör sig beräknas avståndet till varje plats från mitten av kartan. Resultatlistan uppdateras för att visa de närmaste platserna överst på kartan.  
* När du markerar ett resultat i resultatlistan centreras kartan över den valda platsen och information om platsen visas i ett popup-fönster.  
* Att välja en specifik plats på kartan utlöser även ett popup-fönster.
* När användaren zoomar ut grupperas platser i kluster. Kluster representeras av en cirkel med en siffra i cirkeln. Kluster formas och separeras när användaren ändrar zoomningsnivån.
* När du väljer ett kluster zoomas kartan in två nivåer och centreras över klustrets plats.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Skapa datauppsättning för lagringsplats

Innan vi skapar ett program för butikslokaliserare måste vi skapa en datauppsättning för butikerna vi vill ska visas på kartan. I den här självstudien använder vi en datauppsättning för ett fiktivt kafé som heter Contoso Coffee. Datauppsättningen för den här enkla butikslokaliseraren hanteras i en Excel-arbetsbok. Data uppsättningen innehåller 10 213 contoso kaffe handlande platser i nio länder/regioner: USA, Kanada, Förenade kungariket, Frankrike, Tyskland, Italien, Nederländerna, Danmark och Spanien. Här är en skärmbild av hur data ser ut:

![Skärmbild av butikslokaliserardata i en Excel-arbetsbok](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)

Du kan [ladda ned Excel-arbetsboken](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

När vi tittar på skärmbilden över data kan vi göra följande observationer:

* Platsinformation lagras med hjälp av kolumnerna **AddressLine**, **Stad**, **Kommun** (län), **AdminDivision** (region), **PostCode** (postnummer) och **Land**.  
* Kolumnerna **Latitud** och **Longitud** innehåller koordinaterna för varje plats för Contoso Coffee. Om du inte har koordinaternas information kan du använda Search-tjänsterna i Azure Maps för att fastställa platskoordinaterna.
* Vissa ytterligare kolumner innehåller metadata som är relaterade till Café butiker: ett telefonnummer, booleska kolumner och lagrings tider i 24-timmarsformat. Booleska kolumner är för Wi-Fi-och Wheelchair-tillgänglighet. Du kan skapa egna kolumner som innehåller metadata som är mer relevanta för dina plats data.

> [!NOTE]
> Azure Maps återger data i den sfäriska Mercator-projektionen "EPSG: 3857" men läser data i "EPSG: 4326" som använder WGS84 datum.

Det finns många sätt att exponera datauppsättningen för programmet. En metod är att läsa in data i en databas och exponera en webb tjänst som frågar data. Du kan sedan skicka resultatet till användarens webbläsare. Det här alternativet är perfekt för stora datauppsättningar eller för datauppsättningar som uppdateras ofta. Det här alternativet kräver dock mer utvecklings arbete och har en högre kostnad.

En annan metod är att konvertera datauppsättningen till en flat textfil som webbläsaren enkelt kan parsa. Själva filen kan finnas i resten av programmet. Det här alternativet gör allt enkelt, men det är endast ett bra alternativ för mindre datauppsättningar eftersom användaren hämtar alla data. Vi använder den flata textfilen för den här datauppsättningen eftersom filens datastorlek är mindre än 1 MB.  

Om du vill konvertera arbetsboken till en flat textfil sparar du arbetsboken som en tabbavgränsad fil. Varje kolumn avgränsas med ett tabbtecken, vilket gör kolumnerna enkla att parsa i vår kod. Du kan använda formatet kommaavgränsade värden (CSV), men det alternativet kräver mer parsningslogik. Alla fält som har ett kommatecken runt sig skulle vara omslutna av citattecken. Om du vill exportera dessa data som en tabbavgränsad fil i Excel väljer du **Spara som**. I listrutan **Filformat** väljer du **Text (tabbavgränsad)(*.txt)**. Ge filen namnet *ContosoCoffee.txt*.

![Skärmbild av dialogrutan Filformat](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)

Om du öppnar textfilen i anteckningar liknar den följande bild:

![Skärmbild av en fil i anteckningar som visar en tabbavgränsad datauppsättning](./media/tutorial-create-store-locator/StoreDataTabFile.png)

## <a name="set-up-the-project"></a>Konfigurera projektet

För att skapa projektet använder du [Visual Studio](https://visualstudio.microsoft.com) eller valfritt kodredigeringsprogram. Skapa tre filer i din projektmapp: *index.html*, *index.css* och *index.js*. De här filerna definierar layout, stil och logik för programmet. Skapa en mapp med namnet *data* och Lägg till *ContosoCoffee.txt* till mappen. Skapa en annan mapp med namnet *images*. Vi använder 10 bilder i det här programmet för ikoner, knappar och markörer på kartan. Du kan [ladda ned bilderna](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Din projektmapp bör nu se ut som följande bild:

![Skärmbild av projektmappen Simple Store Locator](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)

## <a name="create-the-user-interface"></a>Skapa användargränssnittet

Lägg till kod till *index.html* för att skapa användargränssnittet:

1. Lägg till följande `meta`-taggar till `head` i *index.html*. `charset`Taggen definierar teckenuppsättningen (UTF-8). Värdet för `http-equiv` visar att Internet Explorer och Microsoft Edge använder de senaste webb läsar versionerna. Och den sista `meta` taggen anger ett visnings område som fungerar bra för layouter som svarar.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Lägg till referenser till JavaScript- och CSS-filer för webbkontrollen för Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Lägg till en referens i Azure Maps-tjänstmodulen. Modulen är ett JavaScript-bibliotek som omsluter Azure Maps REST-tjänster och gör dem enkla att använda i JavaScript. Modulen är användbar för sökfunktioner.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Lägg till referenser till *index.js* och *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. I dokumentets brödtext lägger du till en `header`-tagg. I taggen `header` lägger du till logotyp och företagets namn.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Lägg till `main`-tagg och skapa en sökruta som har en textruta och en sökknapp. Lägg även till `div`-referenser för kartan, listpanelen och knappen My Location GPS (Min plats-GPS).

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

När du är klar ska *index.html* se ut som [den här index.html-exempelfilen](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Nästa steg är att definiera CSS-format. CSS-format anger hur programkomponenterna är uppbyggda och programmets utseende. Öppna *index.css* och lägg till följande kod till den. Formatet `@media` definierar andra formatalternativ som ska användas när skärmen är mindre än 700 bildpunkter.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Kör programmet nu, du ser rubriken, sökrutan och Sök knappen. Men kartan syns inte eftersom den inte har lästs in ännu. Om du försöker göra en sökning händer ingenting. Vi behöver konfigurera JavaScript-logiken, som beskrivs i nästa avsnitt. Den här logiken har åtkomst till alla funktioner i Store Locator.

## <a name="wire-the-application-with-javascript"></a>Ställa in programmet med JavaScript

Allt är nu konfigurerat i användar gränssnittet. Vi behöver fortfarande lägga till Java Script för att läsa in och parsa data och sedan återge data på kartan. Kom igång genom att öppna *index.js* och lägg till kod till den, enligt beskrivningen i följande steg.

1. Lägg till globala alternativ för att göra det lättare att uppdatera inställningarna. Definiera variablerna för kartan, popup-fönstret, data källan, ikon skiktet och HTML-markören. Ange HTML-markören som anger mitten på ett Sök område. Och definiera en instans av Azure Maps Sök tjänst klienten.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Lägg till kod till *index.js*. Följande kod initierar kartan. Vi har lagt till en [händelse lyssnare](/javascript/api/azure-maps-control/atlas.map#events) att vänta tills sidan har lästs in. Sedan kabelansluter vi händelser för att övervaka inläsningen av kartan och ge funktioner till knappen Sök och min plats.

   När användaren väljer Sök knappen, eller anger en plats i sökrutan, trycker på RETUR, en Fuzzy-sökning mot användarens fråga initieras. Skicka i en matris med ISO 2-värden för land/region till `countrySet` alternativet om du vill begränsa Sök resultaten till dessa länder/regioner. Genom att begränsa de länder/regioner som ska genomsökas kan du öka noggrannheten i de resultat som returneras. 
  
   När sökningen är klar ska du ta det första resultatet och ställa in kart kameran över detta område. När användaren väljer knappen min plats hämtar du användarens plats med hjälp av HTML5s API för geolokalisering. Detta API är inbyggt i webbläsaren. Centrera sedan kartan över deras plats.  

   > [!Tip]
   > När du använder popup-fönster är det bäst att skapa en enda `Popup`-instans och återanvända instansen genom att uppdatera dess innehåll och position. För varje `Popup`-instans som du lägger till i koden läggs flera DOM-element till på sidan. Ju fler DOM-element det finns på en sida, desto fler saker måste webbläsaren hålla reda på. Om det finns för många objekt kan webbläsaren bli långsam.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. På kartans `ready`-händelselyssnare lägger du till en zoomkontroll, och en HTML-markör ska visas i mitten av sökområdet.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. På kartans `ready`-händelselyssnare lägger du till en datakälla. Gör sedan ett anrop för att läsa in och parsa datauppsättningen. Aktivera klustring på datakällan. Klustring av överlappande punkter i datakällgrupper i ett kluster. Klustren separeras i enskilda punkter när användaren zoomar in. Det här beteendet ger en bättre användar upplevelse och förbättrar prestandan.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. När du läser in datauppsättningen i kartans `ready`-händelselyssnare definierar du en uppsättning lager för att återge data. Ett bubbellager används för att återge klustrade datapunkter. Ett symbollager används för att återge antalet punkter i varje kluster över bubbellagret. Ett andra symbollager visas med en anpassad ikon för enskilda platser på kartan.

   Lägg till händelserna `mouseover` och `mouseout` till bubbel- och ikonlagren för att ändra markören när användaren för muspekaren över ett kluster eller en ikon på kartan. Lägg till en `click`-händelse i bubbellagret. Den här `click` händelsen zoomar in två nivåer och centrerar kartan över ett kluster när användaren väljer ett kluster. Lägg till en `click`-händelse i ikonlagret. `click`-händelsen visar ett popup-fönster som visar information om ett kafé när en användare väljer en enskild platsikon. Lägg till en händelse på kartan för att övervaka när kartan har rört sig klart. Uppdatera objekten i listpanelen när den här händelsen utlöses.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. När ett kafés datauppsättning har lästs in måste den först laddas ned. Textfilen måste sedan delas upp i rader. Den första raden innehåller rubrikinformation. Om du vill göra koden lättare att följa parsar vi den i rubriken till ett objekt som vi sedan kan använda för att leta upp cellindex för varje egenskap. Gå igenom de återstående raderna efter den första raden och skapa en punktfunktion. Lägg till punktfunktionen till datakällan. Uppdatera slutligen listpanelen.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. När List panelen uppdateras beräknas avståndet. Avståndet är från mitten av kartan till alla punkt funktioner i den aktuella Map-vyn. Funktionerna sorteras sedan efter avstånd. HTML genereras för att visa varje plats i listpanelen.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. När användaren väljer ett objekt i listpanelen hämtas formen som objektet tillhör från datakällan. Ett popup-fönster genereras baserat på egenskapens information som finns lagrad i formen. Kartan centreras över formen. Om kartan är mindre än 700 bildpunkter förskjuts kartvyn så att popup-fönstret visas.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Du har nu en fullt fungerande butikslokaliserare. I en webbläsare öppnar du filen *index.html* för butikslokaliseraren. När klustren visas på kartan kan söka du efter en plats med hjälp av sökrutan, genom att välja knappen My Location (Min plats), genom att välja ett kluster eller zooma in på kartan för att se enskilda platser.

Första gången en användare väljer knappen min plats visas en säkerhets varning i webbläsaren som ber om behörighet att komma åt användarens plats. Om användaren samtycker till att dela sin plats zoomar kartan in på användarens plats och kaféer i närheten visas.

![Skärmbild av webbläsaren begäran för att komma åt användarens plats](./media/tutorial-create-store-locator/GeolocationApiWarning.png)

När du zoomar in tillräckligt i ett område som har kaféer separeras klustren i enskilda platser. Välj en av ikonerna på kartan eller Välj ett objekt på sido panelen för att se ett popup-fönster. Popup-fönstret visar information om den valda platsen.

![Skärmbild av den färdiga butikslokaliseraren](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)

Om du ändrar storlek på webbläsarfönstret till mindre än 700 bildpunkter eller öppnar programmet på en mobil enhet ändras layouten så att den passar bättre för mindre skärmar.

![Skärmbild av butikslokaliserarversionen för små skärmar](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)

I den här självstudien har du lärt dig hur du skapar en grundläggande Store-lokaliserare med hjälp av Azure Maps. Butikslokaliseraren som du skapar i den här självstudien kanske har alla funktioner som du behöver. Du kan lägga till funktioner till butikslokaliseraren eller använda mer avancerade funktioner för en mer anpassad användarupplevelse: 

 * Aktivera [förslag medan du skriver](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) i sökrutan.  
 * Lägg till [stöd för flera språk](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
 * Tillåt att användaren [filtrerar på platser längs en väg](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Lägg till möjligheten att [ange filter](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
 * Lägg till stöd för att ange ett inledande sökvärde med hjälp av en frågesträng. När du inkluderar det här alternativet i butikslokaliseraren kan du skapa bokmärken och dela sökningar. Det ger också ett enkelt sätt att skicka sökningar till den här sidan från en annan sida.  
 * Distribuera din butikslokaliserare som en [Azure App Service-webbapp](../app-service/quickstart-html.md). 
 * Lagra dina data i en databas och sök efter närliggande ställen. Mer information finns i [översikten över rumsliga data för SQL Server](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) och [Query spatial data for the nearest neighbor](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017) (Fråga rumsliga data efter den närmaste grannen).

Du kan [Visa fullständig källkod](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator), [Visa Live-exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) och lära dig mer om täckningen och funktionerna i Azure Maps med hjälp av [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md). Du kan också [använda data drivna format uttryck](data-driven-style-expressions-web-sdk.md) för din affärs logik.

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Fler kodexempel och en interaktiv kodupplevelse:

> [!div class="nextstepaction"]
> [Använda kart kontrollen](how-to-use-map-control.md)

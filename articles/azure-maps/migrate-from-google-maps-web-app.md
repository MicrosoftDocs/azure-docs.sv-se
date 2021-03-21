---
title: Självstudie – migrera en webbapp från Google Maps till Microsoft Azure Maps
description: Självstudie om hur du migrerar en webbapp från Google Maps till Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: fcb8090427530271600a6699fafa5c488c426784
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680881"
---
# <a name="tutorial-migrate-a-web-app-from-google-maps"></a>Självstudier: Migrera en webbapp från Google Maps

De flesta Web Apps, som använder Google Maps, använder Google Maps v3 Java Script SDK. Azure Maps Web SDK är lämplig Azure-baserad SDK för att migrera till. Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder. Du kan köra din app på både webb-och mobil program. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med det här SDK: t med Java Script eller TypeScript. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Läsa in en karta
> * Lokalisera en karta
> * Lägg till markörer, polystreck och polygoner.
> * Visa information i ett popup-eller informations fönster
> * Läsa in och visa KML-och interjson-data
> * Kluster markörer
> * Täcka över ett panel lager
> * Visa trafikdata
> * Lägg till ett mark överlägg

Du får också lära dig:

> [!div class="checklist"]
> * Hur du utför vanliga mappnings aktiviteter med hjälp av Azure Maps Web SDK.
> * Bästa praxis för att förbättra prestanda och användar upplevelsen.
> * Tips om hur du gör programmet med fler avancerade funktioner som är tillgängliga i Azure Maps.

Om du migrerar ett befintligt webb program bör du kontrol lera om det använder ett kart kontroll bibliotek med öppen källkod. Exempel på kart kontroll bibliotek med öppen källkod är: cesium, häfte och openlager. Du kan fortfarande migrera ditt program, även om det använder ett kart kontroll bibliotek med öppen källkod, och du inte vill använda Azure Maps Web SDK. I det här fallet ansluter du ditt program till Azure Maps panels tjänster[(](/rest/api/maps/render/getmaptile) \| [satellit paneler satellit paneler](/rest/api/maps/render/getmapimagerytile)). Följande beskriver hur du använder Azure Maps i några vanliga kart kontroll bibliotek med öppen källkod.

* Cesium – en 3D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentation](https://cesiumjs.org/)
* Broschyr – förenklad 2D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentation](https://leafletjs.com/)
* OpenLayers – en 2D-kart kontroll för webben som stöder projektioner. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentation](https://openlayers.org/)

Om du utvecklar med ett JavaScript-ramverk kan något av följande projekt med öppen källkod vara användbart:

* [ng – Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -vinkel 10-omslutning runt Azure Maps.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – en Azure Maps blixt komponent.
* [Azure Maps Reakta komponent](https://github.com/WiredSolutions/react-azure-maps) – ett reaktat omslutning för kontrollen Azure Maps.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – en Azure Maps komponent för Vue-program.

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Viktiga funktioner stöder

Tabellen innehåller nyckel-API-funktioner i Google Maps v3 JavaScript SDK och API-funktionen som stöds i Azure Maps Web SDK.

| Google Maps-funktion     | Stöd för Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Markörer                 | ✓                          |
| Markör kluster       | ✓                          |
| Polylinjer & polygoner    | ✓                          |
| Data lager             | ✓                          |
| Mark överlägg         | ✓                          |
| Värme kartor               | ✓                          |
| Panel lager             | ✓                          |
| KML-skikt               | ✓                          |
| Ritningsverktyg           | ✓                          |
| Netencoder-tjänst        | ✓                          |
| Vägbeskrivnings tjänst      | ✓                          |
| Distans mat ris tjänst | ✓                          |
| Tjänsten Elevation       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Viktiga skillnader i webb-SDK: er

Följande är några viktiga skillnader mellan Google Maps och Azure Maps webb-SDK: er för att vara medveten om:

- Förutom att tillhandahålla en värdbaserad slut punkt för åtkomst till Azure Maps Web SDK är ett NPM-paket tillgängligt. Bädda in webb-SDK-paketet i appar. Mer information finns i den här [dokumentationen](how-to-use-map-control.md). Det här paketet innehåller även TypeScript-definitioner.
- Du måste först skapa en instans av kart klassen i Azure Maps. Vänta tills kartorna `ready` eller `load` händelsen har startats innan du interagerar med kartan. I den här ordningen ser du till att alla kart resurser har lästs in och är redo att nås.
- Båda plattformarna använder ett liknande inpassande system för bas Maps. Panelerna i Google Maps är 256 pixlar i dimensionen. panelerna i Azure Maps är dock 512 pixlar i dimensionen. Om du vill hämta samma Map-vy i Azure Maps som Google Maps subtraherar du Google Maps-zoomnings nivån med siffran en i Azure Maps.
- Koordinaterna i Google Maps kallas `latitude,longitude` , medan Azure Maps används `longitude,latitude` . Azure Maps-formatet justeras med standard `[x, y]` , som följs av de flesta GIS-plattformarna.
- Former i Azure Maps Web SDK baseras på det interjson-schemat. Hjälp klasser exponeras genom [ *Atlas. data* område](/javascript/api/azure-maps-control/atlas.data). Det finns även [*atlasen. Shape*](/javascript/api/azure-maps-control/atlas.shape) -klass. Använd den här klassen för att figursätta inaktuella JSON-objekt så att det blir enkelt att uppdatera och underhålla data bindnings sätt.
- Koordinater i Azure Maps definieras som positions objekt. En koordinat anges som en nummer mat ris i formatet `[longitude,latitude]` . Eller så har den angetts med New Atlas. data. position (longitud, latitud).
    > [!TIP]
    > Positions klassen har en statisk hjälp metod för att importera koordinater i formatet "latitud, longitud". Metoden [Atlas. data. position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) kan ofta ersättas med `new google.maps.LatLng` metoden i Google Maps Code.
- I stället för att ange formateringsinformation för varje form som läggs till i kartan Azure Maps separerar format från data. Data lagras i en data källa och är anslutna till åter givning av lager. Azure Maps koden använder data källor för att rendera data. Den här metoden ger bättre prestanda nytta. Dessutom stöder många lager data drivna format där affärs logik kan läggas till i lager stil alternativ. Detta stöder ändringar av hur enskilda former återges inom ett lager baserat på egenskaper som definierats i formen.

## <a name="web-sdk-side-by-side-examples"></a>Webb-SDK sida vid sida-exempel

Den här samlingen innehåller kod exempel för varje plattform och varje exempel omfattar ett vanligt användnings fall. Det är avsett att hjälpa dig att migrera ditt webb program från Google Maps v3 JavaScript SDK till Azure Maps Web SDK. Kod exempel som är relaterade till webb program finns i Java Script. Azure Maps även tillhandahålla TypeScript-definitioner som ett ytterligare alternativ genom en [NPM-modul](how-to-use-map-control.md).

**Ämnen**

* [Läsa in en karta](#load-a-map)
* [Lokalisera kartan](#localizing-the-map)
* [Ställa in Map-vyn](#setting-the-map-view)
* [Lägga till en markör](#adding-a-marker)
* [Lägga till en anpassad markör](#adding-a-custom-marker)
* [Lägga till en polyline](#adding-a-polyline)
* [Lägga till en polygon](#adding-a-polygon)
* [Visa ett informations fönster](#display-an-info-window)
* [Importera en multijson-fil](#import-a-geojson-file)* 
* [Markör kluster](#marker-clustering)
* [Lägg till en värme karta](#add-a-heat-map)
* [Täcka över ett panel lager](#overlay-a-tile-layer)
* [Visa trafikdata](#show-traffic-data)
* [Lägg till ett mark överlägg](#add-a-ground-overlay)
* [Lägg till KML-data till kartan](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Läsa in en karta

Båda SDK: erna har samma steg för att läsa in en karta:

* Lägg till en referens till kart-SDK: n.
* Lägg till en `div` tagg till sidans huvuddel, som fungerar som plats hållare för kartan.
* Skapa en JavaScript-funktion som anropas när sidan har lästs in.
* Skapa en instans av respektive kart klass.

**Vissa viktiga skillnader**

* Google Maps kräver att en konto nyckel anges i skript referensen för API: et. Autentiseringsuppgifter för Azure Maps anges som alternativ för kart klassen. Den här autentiseringsuppgiften kan vara en prenumerations nyckel eller Azure Active Directory information.
* Google Maps accepterar en callback-funktion i skript referensen för API: et, som används för att anropa en initierings funktion för att läsa in kartan. Med Azure Maps ska onLoad-händelsen på sidan användas.
* När du refererar till `div` elementet där kartan ska återges, `Map` kräver klassen i Azure Maps bara `id` värdet medan Google Maps kräver ett `HTMLElement` objekt.
* Koordinater i Azure Maps definieras som positions objekt, som kan anges som en enkel siffer mat ris i formatet `[longitude, latitude]` .
* Zoomnings nivån i Azure Maps är en nivå som är lägre än zoomnings nivån i Google Maps. Den här avvikelsen beror på skillnaden i storleks förändrings systemet för de två plattformarna.
* Azure Maps lägger inte till några navigerings kontroller på kart arbets ytan. Som standard har en karta som standard inte zoomnings knappar och kart stils knappar. Men det finns kontroll alternativ för att lägga till en mappnings-eller zoomnings knappar, kompass-och rotations kontroll och en kanna.
* En händelse hanterare läggs till i Azure Maps för att övervaka `ready` händelsen av kart instansen. Den här händelsen kommer att utlösa när kartan har läst in WebGL-kontexten och alla nödvändiga resurser. Lägg till all kod som du vill köra när kartan är klar med inläsningen till den här händelse hanteraren.

I de grundläggande exemplen nedan används Google Maps för att läsa in en karta som är centrerad över New York vid koordinater. Longitud:-73,985, latitud: 40,747 och kartan är på zoomnings nivån 12.

#### <a name="before-google-maps"></a>Före: Google Maps

Visa en Google-karta som är centrerad och zoomad över en plats.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

![Enkla Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Läs in en karta med samma vy i Azure Maps tillsammans med en kart kontroll och zoomnings knappar.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

![Enkel Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)

Hitta detaljerad dokumentation om hur du konfigurerar och använder Azure Maps kart kontroll i en webbapp genom att klicka [här](how-to-use-map-control.md).

> [!NOTE]
> Till skillnad från Google Maps kräver Azure Maps inte något inledande Center och en zoomnings nivå för att läsa in kartan. Om den här informationen inte anges när du läser in kartan försöker Azure Maps bestämma användarens stad. Den kommer att centrera och zooma kartan där.

**Ytterligare resurser:**

* Azure Maps innehåller också navigerings kontroller för att rotera och stämma av kart visningen, som dokumenteras [här](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalisera kartan

Om din mål grupp är uppdelad i flera länder/regioner eller talar olika språk är det viktigt att lokaliseringen är viktig.

#### <a name="before-google-maps"></a>Före: Google Maps

Om du vill lokalisera Google Maps lägger du till språk-och region parametrar.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Här är ett exempel på Google Maps med språket inställt på "fr-FR".

![Översättning av Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Azure Maps tillhandahåller två olika sätt att ange språk och regional vy för kartan. Det första alternativet är att lägga till den här informationen i namn området för den globala *atlasen* . Det leder till att alla instanser av kart kontroll i appen används som standard för de här inställningarna. Följande ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Det andra alternativet är att skicka den här informationen till kart alternativen när du läser in kartan. Gillar det här:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Med Azure Maps är det möjligt att läsa in flera kart instanser på samma sida med olika språk-och region inställningar. Det är också möjligt att uppdatera inställningarna i kartan när den har lästs in.

En detaljerad lista över [vilka språk som stöds](supported-languages.md) finns i Azure Maps.

Här är ett exempel på Azure Maps med språket "fr" och användar regionen inställt på "fr-FR".

![Azure Maps lokalisering](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>Ställa in Map-vyn

Dynamiska kartor i både Azure och Google Maps kan flyttas program mässigt till nya geografiska platser. Det gör du genom att anropa lämpliga funktioner i Java Script. I exemplen visas hur du gör kartan för att Visa satellit antenn bilder, centrera kartan över en plats och ändra zoomnings nivån till 15 i Google Maps. Följande plats koordinater används: longitud:-111,0225 och latitud: 35,0272.

> [!NOTE]
> Google Maps använder paneler som är 256 pixlar i dimensioner, medan Azure Maps använder en större 512-pixel-panel. Därför kräver Azure Maps mindre antal nätverks begär Anden för att läsa in samma mappnings område som Google Maps. På grund av hur panelbaserade pyramider fungerar i kart kontroller, måste du subtrahera zoomnings nivån som används i Google Maps med siffran en när du använder Azure Maps. Denna aritmetiska åtgärd ser till att större paneler i Azure Maps återger samma mappnings område som i Google Maps,

#### <a name="before-google-maps"></a>Före: Google Maps

Flytta Google Maps Map-kontrollen med hjälp av- `setOptions` metoden. Med den här metoden kan du ange kartans centrum och zoomnings nivå.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Google Maps set-vy](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

I Azure Maps ändrar du kart positionen med hjälp av `setCamera` metoden och ändrar kart formatet med hjälp av `setStyle` metoden. Koordinaterna i Azure Maps är i formatet "longitud, latitud" och värdet för zoomnings nivån subtraheras av ett.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Azure Maps ange vy](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Ytterligare resurser:**

* [Välj ett kartformat](choose-map-style.md)
* [Mappningsformat som stöds](supported-map-styles.md)

### <a name="adding-a-marker"></a>Lägga till en markör

I Azure Maps finns det flera sätt i vilken punkt data kan återges på kartan:

* **HTML-märken** – återger punkter med hjälp av traditionella dom-element. HTML-märken stöder dra.
* **Symbol lagret** – återger punkter med en ikon eller text inom WebGL-kontexten.
* **Bubbeldiagram** – återger punkter som cirklar på kartan. Cirklarnas radier kan skalas utifrån egenskaperna i data.

Återge symbol lager och bubbeldiagram i WebGL-kontexten. Båda lagren kan återge stora uppsättningar av punkter på kartan. Dessa lager kräver att data lagras i en data källa. Data källor och åter givnings lager ska läggas till i kartan efter att `ready` händelsen har utlösts. HTML-märken återges som DOM-element på sidan och de använder inte en data källa. De fler DOM-element som en sida har, desto långsammare blir sidan. Om du återger fler än några hundra punkter på en karta rekommenderar vi att du använder ett av åter givnings lagren i stället.

Nu ska vi lägga till en markör i kartan med siffran 10 som en etikett. Använd longitud:-0,2 och latitud: 51,5.

#### <a name="before-google-maps"></a>Före: Google Maps

Med Google Maps lägger du till markörer i kartan med hjälp av- `google.maps.Marker` klassen och anger kartan som ett av alternativen.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Google Maps-markör](media/migrate-google-maps-web-app/google-maps-marker.png)

**Efter: Azure Maps med HTML-markörer**

I Azure Maps använder du HTML-markörer för att visa en punkt på kartan. HTML-märken rekommenderas för appar som bara behöver visa ett litet antal punkter på kartan. Om du vill använda en HTML-markör skapar du en instans av `atlas.HtmlMarker` klassen. Ange alternativ för text och position och Lägg till markören till kartan med hjälp av `map.markers.add` metoden.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps HTML-markör](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Efter: Azure Maps med ett symbol lager**

För ett symbol lager lägger du till data i en data källa. Koppla data källan till skiktet. Dessutom bör data källan och lagret läggas till i kartan efter att `ready` händelsen har utlösts. Om du vill återge ett unikt text värde ovanför en symbol måste text informationen lagras som en egenskap för data punkten. Egenskapen måste refereras till i `textField` alternativet för skiktet. Den här metoden är lite mer arbete än att använda HTML-markörer, men det ger bättre prestanda.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps symbol skikt](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Ytterligare resurser:**

- [Skapa en datakälla](create-data-source-web-sdk.md)
- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
- [Klusterpunktdata](clustering-point-data-web-sdk.md)
- [Lägg till HTML-markörer](map-add-custom-html.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
- [Ikon alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Text alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML-markör klass](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Alternativ för HTML-markör](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Lägga till en anpassad markör

Du kan använda anpassade avbildningar för att representera punkter på en karta. Kartan nedan använder en anpassad bild för att visa en punkt på kartan. Punkten visas på latitud: 51,5 och longitud:-0,2. Ankar punkten förskjutnings positionen för markören så att ikonen för kartnålen justeras med rätt placering på kartan.

<center>

![gul kartnåls bild](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

#### <a name="before-google-maps"></a>Före: Google Maps

Skapa en anpassad markör genom att ange ett `Icon` objekt som innehåller `url` till bilden. Ange en `anchor` punkt för att justera punkten för kartnåls bilden med koordinaten på kartan. Ankar värdet i Google Maps är i förhållande till bildens övre vänstra hörn.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

![Google Maps-anpassad markör](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Efter: Azure Maps med HTML-markörer**

Om du vill anpassa en HTML-markör skickar du en HTML-kod `string` eller `HTMLElement` till `htmlContent` alternativet för markören. Använd `anchor` alternativet för att ange markörens relativa position i förhållande till positions koordinaten. Tilldela alternativet en av de nio definierade referens punkterna `anchor` . De definierade punkterna är: "Center", "Top", "bottom", "left", "höger", "Top-Left", "Top-Right", "bottom-left", "längst ned till höger". Innehållet fästs längst ned i mitten av HTML-innehållet som standard. För att göra det enklare att migrera kod från Google Maps anger `anchor` du till "överst till vänster" och använder sedan `pixelOffset` alternativet med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning i förskjutningarna i Google Maps. Därför multiplicerar du förskjutningarna med minus en.

> [!TIP]
> Lägg till `pointer-events:none` som ett format på HTML-innehållet för att inaktivera standard beteendet dra i Microsoft Edge, som visar en oönskad ikon.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps anpassad HTML-markör](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Efter: Azure Maps med ett symbol lager**

Symbol lager i Azure Maps stöder anpassade bilder också. Börja med att läsa in avbildningen till kart resurserna och tilldela den ett unikt ID. Referera till bilden i symbol skiktet. Använd `offset` alternativet för att justera bilden till rätt punkt på kartan. Använd `anchor` alternativet för att ange symbolens relativa position i förhållande till positions koordinaterna. Använd en av de nio definierade referens punkterna. Dessa punkter är: "centrera", "överkant", "nederkant", "left", "höger", "Top-Left", "Top-Right", "bottom-left", "längst ned till höger". Innehållet fästs längst ned i mitten av HTML-innehållet som standard. För att göra det enklare att migrera kod från Google Maps anger `anchor` du till "överst till vänster" och använder sedan `offset` alternativet med samma förskjutning som används i Google Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning i förskjutningarna i Google Maps. Därför multiplicerar du förskjutningarna med minus en.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Symbol lager för Azure Maps anpassade symboler](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Om du vill rendera avancerade anpassade punkter använder du flera åter givnings lager tillsammans. Anta till exempel att du vill ha flera kartnålar som har samma ikon på olika färgade cirklar. I stället för att skapa en bunt med bilder för varje färg överlägg, lägger du till ett symbol lager ovanpå ett bubbel lager. Låt kartnålarna referera till samma data källa. Den här metoden är mer effektiv än att skapa och underhålla en massa olika avbildningar.

**Ytterligare resurser:**

- [Skapa en datakälla](create-data-source-web-sdk.md)
- [Lägg till ett symbol lager](map-add-pin.md)
- [Lägg till HTML-markörer](map-add-custom-html.md)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
- [Ikon alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Text alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML-markör klass](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Alternativ för HTML-markör](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Lägga till en polyline

Använd polystreck för att representera en linje eller bana på kartan. Nu ska vi skapa en streckad polyline på kartan.

#### <a name="before-google-maps"></a>Före: Google Maps

Den polyline klassen accepterar en uppsättning alternativ. Skicka en matris med koordinater i `path` alternativet för polylinje.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Google Maps-sammansatt linje](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Polylines kallas `LineString` eller `MultiLineString` objekt. Dessa objekt kan läggas till i en data källa och återges med hjälp av ett linje lager. Lägg till `LineString` i en data källa och Lägg sedan till data källan i en `LineLayer` för att rendera den.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Azure Maps polylinje](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Ytterligare resurser:**

- [Lägg till rader till kartan](map-add-line-layer.md)
- [Alternativ för linje skikt](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Lägga till en polygon

Azure Maps och Google Maps har liknande stöd för polygoner. Polygoner används för att representera ett område på kartan. I följande exempel visas hur du skapar en polygon som utgör en triangel baserat på kartans centrum-koordinat.

#### <a name="before-google-maps"></a>Före: Google Maps

Klassen polygon accepterar en uppsättning alternativ. Skicka en matris med koordinater till `paths` alternativet för polygonen.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Google Maps-polygon](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Lägg till ett `Polygon` eller ett `MultiPolygon` objekt i en data källa. Återge objektet på kartan med hjälp av lager. Återge ytan i en polygon med ett polygon-lager. Och återge konturen för en polygon med ett linje lager.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Azure Maps polygon](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Ytterligare resurser:**

- [Lägg till en polygon till kartan](map-add-shape.md)
- [Lägg till en cirkel till kartan](map-add-shape.md#add-a-circle-to-the-map)
- [Alternativ för polygon-lager](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Alternativ för linje skikt](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Visa ett informations fönster

Ytterligare information för en entitet kan visas på kartan som en `google.maps.InfoWindow` klass i Google Maps. I Azure Maps kan den här funktionen uppnås med hjälp av- `atlas.Popup` klassen. I nästa exempel läggs en markör till i kartan. När användaren klickar på markören visas ett informations fönster eller en popup-meny.

#### <a name="before-google-maps"></a>Före: Google Maps

Instansiera ett informations fönster med `google.maps.InfoWindow` konstruktorn.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

![Popup-fönstret för Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Låt oss använda popup för att visa mer information om platsen. Skicka ett HTML- `string` eller `HTMLElement` objekt till `content` alternativet för popup-fönstret. Om du vill kan popup-fönster visas oberoende av vilken form som helst. Det innebär att popup-fönster kräver `position` att ett värde anges. Ange `position` värdet. Om du vill visa ett popup-fönster anropar du `open` metoden och skickar den där popup-fönstret ska `map` visas.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Azure Maps popup](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> Du kan göra samma sak med ett symbol-, bubbeldiagram-, linje-eller polygon lager genom att skicka det valda skiktet till Maps-händelseloggen i stället för en markör.

**Ytterligare resurser:**

- [Lägg till ett popup-fönster](map-add-popup.md)
- [Popup med medie innehåll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popup-fönster på former](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Återanvända popup med flera PIN-bara](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup-klass](/javascript/api/azure-maps-control/atlas.popup)
- [Popup-alternativ](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Importera en multijson-fil

Google Maps stöder inläsning och dynamisk formatering av geografi-JSON-data via `google.maps.Data` klassen. Funktionerna i den här klassen justerar mycket mer med den data drivna formateringen av Azure Maps. Men det finns en viktig skillnad. Med Google Maps kan du ange en motringnings funktion. Affärs logiken för formatering av varje funktion som bearbetas individuellt i UI-tråden. Men i Azure Maps stöder lager att ange data drivna uttryck som format alternativ. Dessa uttryck bearbetas vid åter givnings tid i en separat tråd. Azure Maps metoden ger bättre åter givnings prestanda. Den här fördelen är att du märker att större data uppsättningar behöver renderas snabbt.

I följande exempel läses ett interjson-flöde över alla jord bävningar under de senaste sju dagarna från USGS DATAUPPSÄTTNINGEN. Jord bävningar-data återges som skalade cirklar på kartan. Färg och skala för varje cirkel baseras på storleken på varje jord bävning, som lagras i `"mag"` egenskapen för varje funktion i data uppsättningen. Om storleken är större än eller lika med fem blir cirkeln röd. Om den är större än eller lika med tre, men mindre än fem, blir cirkeln orange. Om det är mindre än tre blir cirkeln grön. Radien för varje cirkel blir exponenten för den storlek som multipliceras med 0,1.

#### <a name="before-google-maps"></a>Före: Google Maps

Ange en enda callback-funktion i- `map.data.setStyle` metoden. I motringningsfunktionen använder du affärs logik för varje funktion. Läs in det interjson-flödet med `map.data.loadGeoJson` metoden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps-injson](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Interjson är den grundläggande data typen i Azure Maps. Importera den till en data källa med hjälp av- `datasource.importFromUrl` metoden. Använd ett bubbel lager. Bubble-lagret innehåller funktioner för åter givning av skalade cirklar, baserat på egenskaperna för funktionerna i en data källa. I stället för att ha en callback-funktion konverteras affärs logiken till ett uttryck och skickas till format alternativen. Uttryck definierar hur affärs logiken fungerar. Uttryck kan skickas till en annan tråd och utvärderas mot funktions data. Du kan lägga till flera data källor och lager i Azure Maps, var och en med olika affärs logik. Med den här funktionen kan flera data uppsättningar återges på kartan på olika sätt.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps-interjson](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Ytterligare resurser:**

* [Lägg till ett symbol lager](map-add-pin.md)
* [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
* [Klusterpunktdata](clustering-point-data-web-sdk.md)
* [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Markör kluster

När många data punkter på kartan visualiseras, kan punkterna överlappa varandra. Överlappande gör att kartan ser rörig ut och att kartan blir svår att läsa och använda. Kluster plats data är en process för att kombinera data punkter som ligger nära varandra och som representerar dem på kartan som en enda klustrad data punkt. När användaren zoomar in i kartan delas klustren isär i sina enskilda data punkter. Kluster data punkter för att förbättra användar upplevelsen och kart prestanda.

I följande exempel läser koden in en jord bävning data från den senaste veckan och lägger till den i kartan. Kluster återges som skalade och färgade cirklar. Skalan och färgen på cirklarna beror på antalet punkter som de innehåller.

> [!NOTE]
> Google Maps och Azure Maps använder sig av något olika klustrade algoritmer. Därför varierar även punkt fördelningen i klustren.

#### <a name="before-google-maps"></a>Före: Google Maps

Använd MarkerCluster-biblioteket till kluster markörer. Kluster ikonerna är begränsade till bilder, som har talen ett till fem som namn. De finns i samma katalog.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps-klustring](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Lägg till och hantera data i en data källa. Anslut data källor och lager och återge sedan data. `DataSource`Klassen i Azure Maps tillhandahåller flera kluster alternativ.

* `cluster` – Anger data källan till kluster punkt data.
* `clusterRadius` – Radien i pixlar till kluster punkter tillsammans.
* `clusterMaxZoom` – Den högsta zoomnings nivån i vilken klustring sker. Om du zoomar in mer än den här nivån återges alla punkter som symboler.
* `clusterProperties` – Definierar anpassade egenskaper som beräknas med uttryck mot alla punkter i varje kluster och läggs till i egenskaperna för varje kluster punkt.

När klustring är aktiverat skickar data källan klustrade och data punkter som inte är klustrade till lager för åter givning. Data källan kan klustra hundratals tusen data punkter. En klustrad data punkt har följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som kan användas med DataSource `getClusterExpansionZoom` -, `getClusterChildren` -och- `getClusterLeaves` metoder. |
| `point_count`             | antal  | Antalet platser som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som förkortar `point_count` värdet om det är långt. (till exempel 4 000 blir 4K)  |

`DataSource`Klassen har följande hjälp funktion för att få åtkomst till ytterligare information om ett kluster med hjälp av `cluster_id` .

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | &lt;Funktions geometri för Promise array &lt; &lt; , vilken &gt; \| form som helst&gt;&gt; | Hämtar underordnade för det aktuella klustret på nästa zoomnings nivå. Dessa underordnade kan vara en kombination av former och under kluster. Under klustren är funktioner med egenskaper som matchar ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Löftes &lt; nummer&gt; | Beräknar en zoomnings nivå där klustret börjar expandera eller dela upp. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | &lt;Funktions geometri för Promise array &lt; &lt; , vilken &gt; \| form som helst&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` om du vill returnera en delmängd av punkterna och Använd `offset` sidan till för att gå igenom punkterna. |

När du återger klustrade data på kartan är det ofta bäst att använda två eller flera lager. I följande exempel används tre lager. Ett bubbeldiagram för ritning som skalas ut färgade cirklar baserat på kluster storleken. Ett symbol lager som återger kluster storleken som text. Och använder det ett andra symbol lager för att återge de ogrupperade punkterna. Det finns många andra sätt att återge klustrade data på. Mer information finns i dokumentationen till [kluster plats data](clustering-point-data-web-sdk.md) .

Importera inre JSON-data direkt med hjälp av `importDataFromUrl` funktionen i `DataSource` -klassen, inuti Azure Maps kartan.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps klustring](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Ytterligare resurser:**

* [Lägg till ett symbol lager](map-add-pin.md)
* [Lägg till ett bubbel-lager](map-add-bubble-layer.md)
* [Klusterpunktdata](clustering-point-data-web-sdk.md)
* [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Lägg till en värme karta

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering. De används för att representera densiteten för data med hjälp av en uppsättning färger. Och används ofta för att visa data "aktiva punkter" på en karta. Värme kartor är ett bra sätt att återge data uppsättningar med stora punkter.

Följande exempel läser in ett interjson-flöde för alla jord bävningar under den senaste månaden, från USGS DATAUPPSÄTTNINGEN och återger dem som en viktad termisk karta. `"mag"`Egenskapen används som vikt.

#### <a name="before-google-maps"></a>Före: Google Maps

Om du vill skapa en värme karta läser du in biblioteket "visualisering" genom att lägga till `&libraries=visualization` i URL: en för API-skriptet. Värme kart skiktet i Google Maps stöder inte geografi-data direkt. Börja med att hämta data och konvertera dem till en matris med viktade data punkter:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Värme karta för Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Läs in de här data källorna i en data källa och Anslut data källan till ett termiskt kart skikt. Den egenskap som ska användas för vikten kan överföras till `weight` alternativet med ett uttryck. Importera indata från en klass direkt till Azure Maps med hjälp av `importDataFromUrl` funktionen i `DataSource` klassen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps termisk karta](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Ytterligare resurser:**

- [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)
- [Termisk kart skikts klass](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Alternativ för termisk kart skikt](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Täcka över ett panel lager

Panel lager i Azure Maps kallas bild överlägg i Google Maps. Med panel lager kan du täcka över stora bilder som har delats upp i mindre klickbara bilder, som anpassas efter systemet Maps-placering. Den här metoden används ofta för att täcka stora bilder eller stora data mängder.

I följande exempel överlappar ett väder radar panels lager från Iowa Environment-Mesonet för Iowa State University.

#### <a name="before-google-maps"></a>Före: Google Maps

I Google Maps kan panel lager skapas med hjälp av- `google.maps.ImageMapType` klassen.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

![Panel lager för Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Lägg till ett panel lager på kartan på samma sätt som andra lager. Använd en formaterad URL som har x, y, zoomnings plats hållare; `{x}`, `{y}` `{z}`  för att tala om för lagret var du ska få åtkomst till panelerna. Azure Maps panel lager stöder också `{quadkey}` -, `{bbox-epsg-3857}` -och- `{subdomain}` plats hållare.

> [!TIP]
> I Azure Maps lager kan enkelt återges under andra lager, inklusive bas kart skikt. Det är ofta önskvärt att återge panel lager under kart etiketterna så att de är lätta att läsa. `map.layers.add`Metoden tar i en andra parameter, som är ID: t för det lager där det nya lagret ska infogas. Använd den här koden om du vill infoga ett panel lager under kart etiketterna: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Azure Maps panel lager](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> Panel begär Anden kan samlas in med hjälp av `transformRequest` kartans alternativ. Detta gör att du kan ändra eller lägga till rubriker i begäran om det behövs.

**Ytterligare resurser:**

- [Lägga till panelskikt](map-add-tile-layer.md)
- [Panels skikt klass](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Alternativ för panel lager](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Visa trafikdata

Trafik data kan skrivas över både Azure och Google Maps.

#### <a name="before-google-maps"></a>Före: Google Maps

Överlappa trafik data på kartan med hjälp av trafik skiktet.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

![Google Maps-trafik](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Azure Maps tillhandahåller flera olika alternativ för att Visa trafik. Visa trafik incidenter, till exempel väg stängningar och olyckor som ikoner på kartan. Överlappa trafikflödet och färgkodade vägar på kartan. Färgerna kan ändras baserat på den angivna hastighets gränsen, i förhållande till den normala förväntade fördröjningen eller absolut fördröjning. Incident data i Azure Maps uppdateringar varje minut och flödar data uppdateringar var 2: e minut.

Tilldela önskade värden för `setTraffic` alternativ.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Azure Maps trafik](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Om du klickar på en av trafik ikonerna i Azure Maps visas ytterligare information på popup-menyn.

![Azure Maps trafik incident](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Ytterligare resurser:**

* [Visa trafik på kartan](map-show-traffic.md)
* [Alternativ för trafik överlägg](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Lägg till ett mark överlägg

Både Azure och Google Maps stöder överlägg av de refererade bilderna på kartan. De refererade bilderna flyttas och skalas när du panorera och zoomar kartan. I Google Maps kallas de refererade bilderna som bas överlägg när de befinner sig i Azure Maps de kallas för bild lager. De är fantastiska för att skapa vånings planer, täcka gamla kartor eller bilder från en drönare.

#### <a name="before-google-maps"></a>Före: Google Maps

Ange URL: en till den bild som du vill täcka över och en avgränsnings ruta för att binda bilden på kartan. I det här exemplet läggs en kart bild av [Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

![Bild överlägg för Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

Använd `atlas.layer.ImageLayer` klassen för att täcka över refererade bilder. Den här klassen kräver en URL till en bild och en uppsättning koordinater för bildens fyra hörn. Avbildningen måste vara värd för antingen en domän eller ha CORs aktiverat.

> [!TIP]
> Om du bara har Nord-, syd-, väst-och rotations information och du inte har koordinater för varje hörn i bilden kan du använda den statiska [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) metoden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps bild överlägg](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Ytterligare resurser:**

- [Lägga till en bild som överlägg](map-add-image-layer.md)
- [Bild skikts klass](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Lägg till KML-data till kartan

Både Azure och Google Maps kan importera och återge KML-, KMZ-och GeoRSS-data på kartan. Azure Maps stöder också GPX, GML, spatiala CSV-filer, interjson, välkänd text (well), Web-Mapping Services (WMS), Web-Mapping panel tjänster (WMTS) och Web Feature Services (WFS). Azure Maps läser filerna lokalt i minnet och kan i de flesta fall hantera mycket större KML-filer. 

#### <a name="before-google-maps"></a>Före: Google Maps

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

![Google Maps-KML](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Efter: Azure Maps

I Azure Maps är interjson det viktigaste data formatet som används i webb-SDK, och ytterligare avstånd för spatialdata kan enkelt integreras i med hjälp av den [spatiala IO-modulen](/javascript/api/azure-maps-spatial-io/). Den här modulen har funktioner för både läsning och skrivning av spatialdata och innehåller även ett enkelt data lager som enkelt kan återge data från något av dessa avstånds data format. Om du vill läsa data i en spatialdata, skickar du en URL eller rå data som sträng eller BLOB till `atlas.io.read` funktionen. Då returneras alla parsade data från filen som sedan kan läggas till i kartan. KML är lite mer komplex än det mest spatialdata data formatet, eftersom det innehåller mycket mer information om formatering. Klassen har stöd för att `SpatialDataLayer` återge majoriteten av dessa format, men ikoner för bilder måste läsas in i kartan före inläsning av funktions data och bas överlägg måste läggas till som lager till kartan separat. Vid inläsning av data via en URL bör den finnas på en CORs-aktiverad slut punkt, eller också ska en proxyserver skickas som ett alternativ till funktionen Read. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
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
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Ytterligare resurser:**

- [funktionen Atlas. io. Read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Ytterligare kod exempel

Här följer några ytterligare kod exempel som är relaterade till Google Maps-migrering:

* [Ritningsverktyg](map-add-drawing-toolbar.md)
* [Begränsa kartan till två fingrar för panorering](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
* [Begränsa zoom av rullnings hjul](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
* [Skapa en hel skärms kontroll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Terminal**

* [Använda modulen Azure Maps tjänster](how-to-use-services-module.md)
* [Sök efter orienteringspunkter](map-search-location.md)
* [Hämta information från en koordinat (omvänd landskod)](map-get-information-from-coordinate.md)
* [Visa anvisningar från A till B](map-route.md)
* [Sök automatiska förslag med JQuery-användargränssnitt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps v3 till Azure Maps klass mappning för webb SDK

Följande bilaga innehåller en kors referens för de ofta använda klasserna i Google Maps v3 och motsvarande Azure Maps Web SDK.

### <a name="core-classes"></a>Core-klasser

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Tamazight. Mappa](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [Tamazight. Motta](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [Tamazight. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. data. position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [Atlas. data. BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [Tamazight. CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[Tamazight. CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[Tamazight. ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[Tamazight. StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[Tamazight. UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [Tamazight. Stor](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Överläggs klasser

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[Atlas. data. Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[Atlas. Layer. SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[Tamazight. SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[Tamazight. IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[Tamazight. TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[Atlas. Layer. BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[Tamazight. BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [Atlas. data. polygon](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [Tamazight. PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [Tamazight. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [Atlas. data. Lin Est ring](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[Tamazight. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Se [lägga till en cirkel till kartan](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Tamazight. TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [Tamazight. TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[Tamazight. ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Tjänst klasser

Azure Maps Web SDK innehåller en tjänst-modul som kan läsas in separat. Den här modulen radbryter Azure Maps REST-tjänster med ett webb-API och kan användas i Java Script-, TypeScript-och Node.js-program.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [Tamazight. SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[Tamazight. SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[Tamazight. SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[Tamazight. SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[Tamazight. SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[Tamazight. SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[Tamazight. SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[Tamazight. SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[Tamazight. SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[Tamazight. SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [Atlas. service. RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [Tamazight. CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [Atlas. service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Bibliotek

Bibliotek lägger till ytterligare funktioner till kartan. Många av dessa bibliotek finns i kärn SDK för Azure Maps. Här följer några likvärdiga klasser som kan användas i stället för dessa Google Maps-bibliotek

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Ritnings bibliotek       | [Modul för ritningsverktyg](set-drawing-options.md) |
| Geometri bibliotek      | [Atlas. matematik](/javascript/api/azure-maps-control/atlas.math)   |
| Visualiserings bibliotek | [Termiskt kart skikt](map-add-heat-map-layer.md) |

## <a name="clean-up-resources"></a>Rensa resurser

Det gick inte att rensa några resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om att migrera till Azure Maps:

> [!div class="nextstepaction"]
> [Migrera en webbtjänst](migrate-from-google-maps-web-services.md)

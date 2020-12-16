---
title: 'Självstudier: Migrera en webbapp från Bing Maps | Microsoft Azure Maps'
description: Självstudie om hur du migrerar en webbapp från Bing Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 6037deb484ca966ab3a54cc60b0d53ac8299d500
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590009"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>Självstudie – migrera en webbapp från Bing Maps

Webb program som använder Bing Maps använder ofta Bing Maps V8 Java Script SDK. Azure Maps Web SDK är lämplig Azure-baserad SDK för att migrera till. Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder för visning i dina webb-eller mobil program. Den här kontrollen använder WebGL, så att du kan rendera stora datauppsättningar med höga prestanda. Utveckla med det här SDK: t med Java Script eller TypeScript.

Om du migrerar ett befintligt webb program kontrollerar du att det använder ett kart kontroll bibliotek med öppen källkod, till exempel cesium, häfte och openlager. Om det är och du föredrar att fortsätta att använda biblioteket kan du ansluta det till Azure Maps panel[tjänster (satellit paneler i](/rest/api/maps/render/getmaptile) panelens \| [satellit paneler](/rest/api/maps/render/getmapimagerytile)). Länkarna nedan innehåller information om hur du använder Azure Maps i några ofta använda kart kontroll bibliotek med öppen källkod.

-   Cesium – en 3D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentation](https://cesiumjs.org/)
-   Broschyr – förenklad 2D-kart kontroll för webben. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentation](https://leafletjs.com/)
-   OpenLayers – en 2D-kart kontroll för webben som stöder projektioner. [Kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentation](https://openlayers.org/)

Om du utvecklar med ett JavaScript-ramverk kan något av följande projekt med öppen källkod vara användbart:

- [ng – Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -vinkel 10-omslutning runt Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – en Azure Maps blixt komponent.
- [Azure Maps Reakta komponent](https://github.com/WiredSolutions/react-azure-maps) – ett reaktat omslutning för kontrollen Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – en Azure Maps komponent för Vue-program.

## <a name="key-features-support"></a>Viktiga funktioner stöder

I följande tabell visas viktiga API-funktioner i Bing Maps V8 JavaScript SDK och stöd för ett liknande API i Azure Maps Web SDK.

| Bing Maps-funktion        | Stöd för Azure Maps Web SDK                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Kartnålar                 | ✓                                                                                      |
| Kartnål-kluster       | ✓                                                                                      |
| Polylinjer & polygoner     | ✓                                                                                      |
| Mark överlägg          | ✓                                                                                      |
| Värme kartor                | ✓                                                                                      |
| Panel lager              | ✓                                                                                      |
| KML-skikt                | ✓                                                                                      |
| Kon tur lager            | [Exempel](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Data diskretisering-skikt       | [Exempel](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Animerat panel lager      | Ingår i modulen för öppen källkod Azure Maps- [animering](https://github.com/Azure-Samples/azure-maps-animations) |
| Ritningsverktyg            | ✓                                                                                      |
| Netencoder-tjänst         | ✓                                                                                      |
| Vägbeskrivnings tjänst       | ✓                                                                                      |
| Distans mat ris tjänst  | ✓                                                                                      |
| Spatial data tjänst     | Ej tillämpligt                                                                                    |
| Satellit-/flyg bilder | ✓                                                                                      |
| Fåglars ögon bilder         | Planerad                                                                                |
| Streetside bilder       | Planerad                                                                                |
| Stöd för interjson          | ✓                                                                                      |
| GeoXML-stöd           | ✓                                                                                      |
| Stöd för Well-Known text  | ✓                                                                                      |
| Anpassade kart format        | Delvis                                                                                |

Azure Maps också många ytterligare [moduler med öppen källkod för webb-SDK](open-source-projects.md#open-web-sdk-modules) : n som utökar dess funktioner.

## <a name="notable-differences-in-the-web-sdks"></a>Viktiga skillnader i webb-SDK: er

Följande är några av de viktigaste skillnaderna mellan Bing Maps och Azure Maps webb-SDK: er som ska vara medvetna om:

-   Förutom att tillhandahålla en värdbaserad slut punkt för åtkomst till Azure Maps Web SDK är ett NPM-paket också tillgängligt för att bädda in webb-SDK i appar om det är lämpligt. Mer information finns i den här [dokumentationen](./how-to-use-map-control.md) . Det här paketet innehåller även TypeScript-definitioner.
-   Bing Maps innehåller två värdbaserade grenar av sina SDK: er. Lansering och experiment. Experiment grenen kan ta emot flera uppdateringar per dag när en ny utveckling sker. Azure Maps bara är värd för en versions gren, men experimentella funktioner skapas som anpassade moduler i projektet med öppen källkod Azure Maps kod exempel. Bing Maps som används för att ha en frusen gren och som har uppdaterats mindre ofta, vilket minskar risken för att bryta ändringar på grund av en version. I Azure Maps där kan du använda modulen NPM och peka på en tidigare del versions version.

> [!TIP]
> Azure Maps publicerar både minified-och unminified-versioner av SDK: n. Enkel borttagning `.min` från fil namnen. Unminified-versionen är användbar vid fel sökning av problem, men se till att använda minified-versionen i produktion för att dra nytta av den mindre fil storleken.

-   När du har skapat en instans av kart klassen i Azure Maps, ska din kod vänta på att Maps `ready` eller `load` händelsen ska kunna utlösas innan du interagerar med kartan. Dessa händelser säkerställer att alla kart resurser har lästs in och är redo att nås.
-   Båda plattformarna använder ett liknande överlappande system för bas Maps, men panelerna i Bing Maps är 256 pixlar i dimensionen, medan panelerna i Azure Maps är 512 pixlar i dimensionen. För att få samma Map-vy i Azure Maps som Bing Maps måste en zoomnivå som används i Bing Maps subtraheras av en i Azure Maps.
-   Koordinaterna i Bing Maps kallas `latitude, longitude` när Azure Maps används `longitude, latitude` . Det här formatet överensstämmer med standarden `[x, y]` som följs av de flesta GIS-plattformarna.

-   Former i Azure Maps Web SDK baseras på det interjson-schemat. Hjälp klasser exponeras genom [Atlas. data område](/javascript/api/azure-maps-control/atlas.data). Det finns även [atlasen. Shape](/javascript/api/azure-maps-control/atlas.shape) -klass som kan användas för att figursätta INTERjson-objekt och göra dem enkla att uppdatera och underhålla på ett data bindnings sätt.
-   Koordinater i Azure Maps definieras som positions objekt som kan anges som en enkel siffer mat ris i formatet `[longitude, latitude]` eller `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> Positions klassen har en statisk hjälp funktion för att importera koordinater i `latitude, longitude` formatet. Funktionen [Atlas. data. position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position)kan ofta ersätta `new Microsoft.Maps.Location` funktionen i Bing Maps-kod.

-   I stället för att ange formateringsinformation för varje form som läggs till i kartan Azure Maps separerar format från data. Data lagras i data källor och är anslutna till åter givnings skikt som Azure Maps kod använder för att återge data. Den här metoden ger bättre prestanda nytta. Dessutom stöder många lager data driven format där affärs logik kan läggas till i lager stil alternativ som ändrar hur enskilda former återges inom ett lager baserat på egenskaper som definierats i formen.
-   Azure Maps tillhandahåller en mängd användbara, spatiala matematiska funktioner i `atlas.math` namn området, men de skiljer sig åt från de i Bing mapss spatiala matematik-modul. Den främsta skillnaden är att Azure Maps inte tillhandahåller inbyggda funktioner för binära åtgärder, t. ex. union och skärning, eftersom Azure Maps baseras på en icke öppen standard, så finns det många bibliotek med öppen källkod. Ett populärt alternativ som fungerar bra med Azure Maps och ger ett ton med spatiala matematiska funktioner är [Turf JS](http://turfjs.org/).

Se även [Azure Maps ord](./glossary.md) lista för en djupgående lista över termer som är kopplade till Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Webb-SDK sida vid sida-exempel

Följande är en samling kod exempel för varje plattform som används för vanliga användnings fall för att hjälpa dig att migrera ditt webb program från Bing Maps V8 Java Script SDK till Azure Maps Web SDK. Kod exempel som är relaterade till webb program finns i Java Script. Azure Maps även tillhandahålla TypeScript-definitioner som ett ytterligare alternativ genom en [NPM-modul](./how-to-use-map-control.md).

**Ämnen**

- [Läsa in en karta](#load-a-map)
- [Lokalisera kartan](#localizing-the-map)
- [Ställa in Map-vyn](#setting-the-map-view)
- [Lägga till en kartnål](#adding-a-pushpin)
- [Lägga till en anpassad kartnål](#adding-a-custom-pushpin)
- [Lägga till en polyline](#adding-a-polyline)
- [Lägga till en polygon](#adding-a-polygon)
- [Visa en information](#display-an-infobox)
- [Kartnål-kluster](#pushpin-clustering)
- [Lägg till en värme karta](#add-a-heat-map)
- [Täcka över ett panel lager](#overlay-a-tile-layer)
- [Visa trafikdata](#show-traffic-data)
- [Lägg till ett mark överlägg](#add-a-ground-overlay)
- [Lägg till KML-data till kartan](#add-kml-data-to-the-map)
- [Lägg till rit verktyg](#add-drawing-tools)


### <a name="load-a-map"></a>Läsa in en karta

Att läsa in en karta i båda SDK: er följer samma uppsättning steg.

-   Lägg till en referens till kart-SDK: n.
-   Lägg till en `div` tagg i bröd texten på sidan som fungerar som plats hållare för kartan.
-   Skapa en JavaScript-funktion som anropas när sidan har lästs in.
-   Skapa en instans av respektive kart klass.

**Vissa viktiga skillnader**

-   Bing Maps kräver att en konto nyckel anges i skript referensen för API: et eller som ett mappnings alternativ. Autentiseringsuppgifter för Azure Maps anges som alternativ för kart klassen. Detta kan vara en prenumerations nyckel eller Azure Active Directory information.
-   Bing Maps tar i en callback-funktion i skript referensen för API: et som används för att anropa en initierings funktion för att läsa in kartan. Med Azure Maps ska onLoad-händelsen på sidan användas.
-   När du använder ett ID för att referera till `div` elementet som kartan kommer att återges i, använder Bing Maps en HTML-selektor (d.v.s. `#myMap` ), medan Azure Maps endast använder ID-värdet (dvs. `myMap` ).
-   Koordinater i Azure Maps definieras som positions objekt som kan anges som en enkel siffer mat ris i formatet `[longitude, latitude]` .
-   Zoomnings nivån i Azure Maps är en nivå som är lägre än Bing Maps-exemplet på grund av skillnaden i att placeras i system storlek mellan plattformarna.
-   Som standard lägger Azure Maps inte till några navigerings kontroller på kart arbets ytan, till exempel zoomnings knappar och kart stils knappar. Det finns dock kontroller för att lägga till en mappnings väljare, zoomnings knappar, kompass-och rotations kontroll och en kanna-kontroll.
-   En händelse hanterare läggs till i Azure Maps för att övervaka `ready` händelsen av kart instansen. Detta aktive ras när kartan har läst in WebGL-kontexten och alla resurser som behövs. Alla post inläsnings koder kan läggas till i den här händelse hanteraren.

I exemplen nedan visas hur du läser in en grundläggande karta som centreras över New York vid koordinater (longitud:-73,985, latitud: 40,747) och är på zoomnings nivå 12 i Bing Maps.

**Före: Bing Maps**

Följande kod är ett exempel på hur du visar en Bing-karta centrerad och zoomad över en plats.

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

<center>

![Bing Maps-Karta](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

**Efter: Azure Maps**

Följande kod visar hur du läser in en karta med samma vy i Azure Maps tillsammans med en kart kontroll och zoomnings knappar.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
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

<center>

![Azure Maps Karta](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

Detaljerad dokumentation om hur du konfigurerar och använder Azure Maps kart kontroll i en webbapp finns [här](./how-to-use-map-control.md).

> [!TIP]
> Azure Maps publicerar både minified-och unminified-versioner av SDK: n. Ta bort `.min` från fil namnen. Unminified-versionen är användbar vid fel sökning av problem, men se till att använda minified-versionen i produktion för att dra nytta av den mindre fil storleken.

**Ytterligare resurser**

-   Azure Maps innehåller också navigerings kontroller för att rotera och stämma av kart visningen som dokumenteras [här](./map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalisera kartan

Om din mål grupp är uppdelad i flera länder eller talar olika språk är det viktigt att lokaliseringen är viktig.

**Före: Bing Maps**

För att lokalisera Bing Maps, anges språk och region med `setLang` `UR` parametrarna och läggs till i tagg- `<script>` referensen till API: et. Vissa funktioner i Bing Maps är bara tillgängliga på vissa marknader, eftersom användarnas marknads plats anges med hjälp av `setMkt` parametern.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Här är ett exempel på Bing Maps med språket inställt på "fr-FR".

<center>

![Lokaliserad Bing Maps-Karta](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**Efter: Azure Maps**

Azure Maps innehåller bara alternativ för att ställa in språket och den regionala vyn för kartan. En marknads parameter används inte för att begränsa funktionerna. Det finns två olika sätt att ställa in språket och den regionala vyn för kartan. Det första alternativet är att lägga till den här informationen i det globala `atlas` namn området som leder till att alla mappnings kontroll instanser i appen används som standard för de här inställningarna. Följande anger språket till franska ("fr-FR") och vyn region för att `"auto"` :

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Det andra alternativet är att skicka den här informationen till kart alternativen när du läser in kartan som:

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
> Med Azure Maps kan du läsa in flera kart instanser på samma sida med olika språk-och region inställningar. Dessutom är det också möjligt att uppdatera inställningarna i kartan när den har lästs in. En detaljerad lista över språk som stöds i Azure Maps hittar du [här](./supported-languages.md).

Här är ett exempel på Azure Maps med språket "fr" och användar regionen inställt på "fr-FR".

<center>

![Lokaliserad Azure Maps Karta](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>Ställa in Map-vyn

Dynamiska kartor i både Bing och Azure Maps kan program mässigt flyttas till nya geografiska platser genom att anropa lämpliga funktioner i Java Script. I exemplen nedan visas hur du gör kartan för att Visa satellit antenn bilder, centrera kartan över en plats med koordinater (longitud:-111,0225, latitud: 35,0272) och ändra zoomnings nivån till 15 i Bing Maps.

> [!NOTE]
> Bing Maps använder paneler som är 256 pixlar i dimensioner medan Azure Maps använder en större 512-pixel-panel. Detta minskar antalet nätverks begär Anden som krävs av Azure Maps för att läsa in samma mappnings område som Bing Maps. Men på grund av hur panelbaserade pyramider fungerar i kart kontroller, innebär de större panelerna i Azure Maps att du kan uppnå samma visnings Bart område som en karta i Bing Maps. du måste subtrahera zoomnings nivån som används i Bing Maps med 1 när du använder Azure Maps.

**Före: Bing Maps**

Kart kontrollen Bing Maps kan flyttas program mässigt med hjälp av `setView` funktionen som gör att du kan ange kartans centrum och zoomnings nivå.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Bing Maps ange kart visning](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**Efter: Azure Maps**

I Azure Maps kan kart positionen ändras program mässigt med hjälp av `setCamera` kartans funktion och kart stilen kan ändras med hjälp av `setStyle` funktionen. Observera att koordinaterna i Azure Maps är i formatet "longitud, latitud" och värdet för zoomnings nivån subtraheras med 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Azure Maps Ställ in Map-vy](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**Ytterligare resurser**

-   [Välj ett kartformat](./choose-map-style.md)
-   [Mappningsformat som stöds](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Lägga till en kartnål

I Azure Maps finns det flera sätt att återge data på kartan.

-   HTML-märken – återger punkter med hjälp av traditionella DOM-element. HTML-märken stöder dra.
-   Symbol lagret – återger punkter med en ikon och/eller text inom WebGL-kontexten.
-   Bubbeldiagram – återger punkter som cirklar på kartan. Cirklarnas radier kan skalas utifrån egenskaperna i data.

Både symbol-och bubbel lager återges inom WebGL-kontexten och kan återge mycket stora uppsättningar av punkter på kartan. Dessa lager kräver att data lagras i en data källa. Data källor och åter givnings lager ska läggas till i kartan efter att `ready` händelsen har utlösts. HTML-märken återges som DOM-element på sidan och använder inte en data källa. De fler DOM-element som en sida har, desto långsammare blir sidan. Om du återger fler än några hundra punkter på en karta rekommenderar vi att du använder ett av åter givnings lagren i stället.

I exemplen nedan lägger du till en markör på kartan vid (longitud:-0,2, latitud: 51,5) med siffran 10 som en etikett.

**Före: Bing Maps**

Med Bing Maps läggs markörer till i kartan med `Microsoft.Maps.Pushpin` klassen *. Kartnålar läggs sedan till i kartan med hjälp av en av två funktioner.

Den första funktionen är att skapa ett lager, infoga kartnålen i den och sedan lägga till lagret i kart `layers` egenskapen.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

Det andra är att lägga till den med hjälp av Map- `entities` egenskapen. Den här funktionen markeras som föråldrad i dokumentationen för Bing Maps-V8 men har inte varit delvis funktionell för grundläggande scenarier.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Bing Maps-Lägg till puspin](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**Efter: Azure Maps med HTML-markörer**

I Azure Maps kan HTML-markörer användas för att enkelt visa en punkt på kartan och rekommenderas för enkla appar som bara behöver visa ett litet antal punkter på kartan. Om du vill använda en HTML-markör skapar du en instans av `atlas.HtmlMarker` klassen, anger alternativ för text och position och lägger till markören i kartan med hjälp av `map.markers.add` funktionen.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps Lägg till markör](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Efter: Azure Maps med ett symbol lager**

När du använder ett symbol lager måste data läggas till i en data källa och data källan som är kopplad till lagret. Dessutom bör data källan och lagret läggas till i kartan efter att `ready` händelsen har utlösts. Om du vill rendera ett unikt text värde ovanför en symbol måste text informationen lagras som en egenskap för data punkten och den egenskapen som refereras till i `textField` lager alternativet. Detta är lite mer arbete än att använda HTML-märken men ger många prestanda för delar.

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

<center>

![Azure Maps lägga till symbol skikt](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Ytterligare resurser**

-   [Skapa en datakälla](./create-data-source-web-sdk.md)
-   [Lägg till ett symbol lager](./map-add-pin.md)
-   [Lägg till ett bubbel-lager](./map-add-bubble-layer.md)
-   [Klusterpunktdata](./clustering-point-data-web-sdk.md)
-   [Lägg till HTML-markörer](./map-add-custom-html.md)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)
-   [Ikon alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Text alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-markör klass](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Alternativ för HTML-markör](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Lägga till en anpassad kartnål

Anpassade bilder kan användas för att representera punkter på en karta. Följande bild används i exemplen nedan och använder en anpassad bild för att visa en punkt på kartan (latitud: 51,5, longitud:-0,2) och förskjutning av markörens position så att punkten på kartnålen justeras med rätt placering på kartan.

| ![Azure Maps lägga till puspin](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**Före: Bing Maps**

I Bing Maps skapas en anpassad markör genom att skicka en URL till en bild till en `icon` kartnåls alternativ. `anchor`Alternativet används för att justera punkten för den kartnålade bilden med koordinaten på kartan. Ankar värdet i Bing Maps i förhållande till bildens övre vänstra hörn.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Bing Maps Lägg till anpassade puspin](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**Efter: Azure Maps med HTML-markörer**

Så här anpassar du en HTML-markör i Azure Maps en HTML-kod `string` eller `HTMLElement` kan skickas till `htmlContent` alternativet för markören. I Azure Maps används ett `anchor` alternativ för att ange markörens relativa position i förhållande till positions koordinaten med en av nio definierade referens punkter. "centrera", "överkant", "nederkant", "left", "höger", "överst till vänster", "överst till höger", "längst ned till vänster", "längst ned till höger". Innehållet är förankrat och är inställt på "bottom" som standard, vilket är det nedersta mitten av HTML-innehållet. För att göra det enklare att migrera kod från Bing Maps anger du fäst punkten till "överst till vänster" och använder sedan `offset` alternativet med samma förskjutning som används i Bing Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning av Bing Maps, så du multiplicerar dem med minus ett.

> [!TIP]
> Lägg till `pointer-events:none` som ett format på HTML-innehållet för att inaktivera standard beteendet dra i MS Edge som visar en oönskad ikon.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps Lägg till anpassad markör](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**Efter: Azure Maps med ett symbol lager**

Symbol lager i Azure Maps stöder anpassade avbildningar också, men bilden måste läsas in i kart resurserna först och tilldelas ett unikt ID. Symbol lagret kan sedan referera till detta ID. Symbolen kan förskjutas för att justeras till rätt punkt på bilden med hjälp av ikon `offset` alternativet. I Azure Maps används ett `anchor` alternativ för att ange symbolens relativa position i förhållande till positions koordinaten med en av nio definierade referens punkter. "centrera", "överkant", "nederkant", "left", "höger", "överst till vänster", "överst till höger", "längst ned till vänster", "längst ned till höger". Innehållet fästs och anges till "bottom" som standard som är det nedersta mitten av HTML-innehållet. För att göra det enklare att migrera kod från Bing Maps anger du fäst punkten till "överst till vänster" och använder sedan `offset` alternativet med samma förskjutning som används i Bing Maps. Förskjutningarna i Azure Maps flyttas i motsatt riktning av Bing Maps, så du multiplicerar dem med minus ett.

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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

<center>

![Bing Maps Lägg till anpassat symbol lager](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> Om du vill skapa en avancerad anpassad åter givning av punkter använder du flera åter givnings lager tillsammans. Om du till exempel vill ha flera kartnålar som har samma ikon på olika färgade cirklar, i stället för att skapa en bunt med bilder för varje färg överlägg ett symbol lager ovanpå ett bubbeldiagram och låta dem referera till samma data källa. Detta kommer att vara mycket effektivare än att skapa och låta kartan underhålla flera olika avbildningar.

**Ytterligare resurser**

-   [Skapa en datakälla](./create-data-source-web-sdk.md)
-   [Lägg till ett symbol lager](./map-add-pin.md)
-   [Lägg till HTML-markörer](./map-add-custom-html.md)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)
-   [Ikon alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Text alternativ för symbol skikt](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML-markör klass](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Alternativ för HTML-markör](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Lägga till en polyline

Polylinjer används för att representera en linje eller bana på kartan. I exemplen nedan visas hur du skapar en streckad polyline på kartan.

**Före: Bing Maps**

I Bing Maps tar polyline-klassen i en matris med platser och en uppsättning alternativ.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Bing Maps-sammansatt linje](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**Efter: Azure Maps**

I Azure Maps hänvisas polystreck till de vanligaste eller vanliga geospatiala `LineString` `MultiLineString` objekten. Dessa objekt kan läggas till i en data källa och återges med hjälp av ett linje lager. Alternativen linje färg, bredd och streck mat ris är nästan identiska mellan plattformarna.

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

<center>

![Azure Maps rad](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**Ytterligare resurser**

-   [Lägg till rader till kartan](./map-add-line-layer.md)
-   [Alternativ för linje skikt](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Lägga till en polygon

Polygoner används för att representera ett område på kartan. Azure Maps-och Bing Maps ger mycket liknande stöd för polygoner. I exemplen nedan visas hur du skapar en polygon som utgör en triangel baserat på kartans centrum-koordinat.

**Före: Bing Maps**

I Bing Maps tar klassen polygon i en matris med koordinater eller koordinerande ringar och en uppsättning alternativ.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Bing Maps-polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Efter: Azure Maps**

I Azure Maps kan polygon-och multipolygon-objekt läggas till i en data källa och återges på kartan med hjälp av lager. Ytan i en polygon kan återges i ett polygon-lager. Dispositionen för en polygon kan återges med hjälp av ett linje lager.

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

<center>

![Azure Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Ytterligare resurser**

-   [Lägg till en polygon till kartan](./map-add-shape.md#use-a-polygon-layer)
-   [Lägg till en cirkel till kartan](./map-add-shape.md#add-a-circle-to-the-map)
-   [Alternativ för polygon-lager](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Alternativ för linje skikt](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Visa en information

Ytterligare information för en entitet kan visas på kartan som en `Microsoft.Maps.Infobox` klass i Bing Maps, i Azure Maps det kan uppnås med hjälp av `atlas.Popup` klassen. I exemplen nedan lägger du till en kartnål/markör till kartan och när du klickar på visas en informations ruta/popup.

**Före: Bing Maps**

Med Bing Maps skapas en information med hjälp av `Microsoft.Maps.Infobox` konstruktorn.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Information om Bing Maps](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**Efter: Azure Maps**

I Azure Maps kan en popup användas för att visa ytterligare information för en plats. Ett HTML- `string` eller `HTMLElement` objekt kan skickas till `content` alternativet för popup-fönstret. Popup-fönster kan visas oberoende av valfri form om det behövs och du måste `position` Ange ett värde. Om du vill visa ett popup-fönster anropar du `open` funktionen och skickar den karta som popup-fönstret ska visas på.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps popup](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> Om du vill göra samma sak med ett symbol-, bubbeldiagram-, linje-eller polygon-lager skickar du lagret till händelse koden Maps i stället för en markör.

**Ytterligare resurser**

-   [Lägg till ett popup-fönster](./map-add-popup.md)
-   [Popup med medie innehåll](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Popup-fönster på former](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Återanvända popup med flera PIN-bara](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Popup-klass](/javascript/api/azure-maps-control/atlas.popup)
-   [Popup-alternativ](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Kartnål-kluster

När du visualiserar många data punkter på kartan överlappar de varandra, och kartan ser rörig ut och blir svår att se och använda. Klustring av punkt data kan användas för att förbättra användar upplevelsen och även förbättra prestandan. Kluster plats data är en process för att kombinera punkt data som ligger nära varandra och som representerar dem på kartan som en enda klustrad data punkt. När användaren zoomar in i kartan delas klustren isär i sina enskilda data punkter.

Exemplen nedan läser in en jord bävning data från den senaste veckan och lägger till dem till kartan. Kluster återges som skalade och färgade cirklar beroende på antalet punkter som de innehåller.

> [!NOTE]
> Det finns flera olika algoritmer som används för kartnål-kluster. Bing Maps använder en enkel grid-baserad funktion medan Azure Maps använder en mer avancerad och visuellt tilltalande metod för att använda punktbaserade kluster.

**Före: Bing Maps**

I Bing Maps kan du läsa in geografi data med hjälp av geografi-modulen. Kartnålar kan grupperas genom att läsas in i modulen kluster och med hjälp av kluster skiktet som den innehåller.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';   //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing Maps-klustring](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**Efter: Azure Maps**

I Azure Maps läggs data till och hanteras av en data källa. Lager ansluter till data källor och återger data i dem. `DataSource`Klassen i Azure Maps tillhandahåller flera kluster alternativ.

-   `cluster` – Anger data källan till kluster punkt data. 
-   `clusterRadius` – Radien i pixlar till kluster punkter tillsammans.
-   `clusterMaxZoom` – Den maximala zoomnings nivån som klustringen sker. Om du zoomar in mer än så återges alla punkter som symboler.
-   `clusterProperties` – Definierar anpassade egenskaper som beräknas med uttryck mot alla punkter i varje kluster och läggs till i egenskaperna för varje kluster punkt.

När klustring är aktiverat skickar data källan klustrade och data punkter som inte är klustrade till lager för åter givning. Data källan kan klustra hundratals tusen data punkter. En klustrad data punkt har följande egenskaper:

| Egenskapsnamn               | Typ    | Beskrivning                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Anger om funktionen representerar ett kluster.     |
| `cluster_id`                | sträng  | Ett unikt ID för klustret som kan användas med `DataSource` klasserna `getClusterExpansionZoom` , `getClusterChildren` och `getClusterLeaves` . |
| `point_count`               | antal  | Antalet platser som klustret innehåller.     |
| `point_count_abbreviated`   | sträng  | En sträng som förkortar `point_count` värdet om det är långt. (till exempel 4 000 blir 4K) |

`DataSource`Klassen har följande hjälp funktion för att få åtkomst till ytterligare information om ett kluster med hjälp av `cluster_id` .

| Funktion       | Returtyp        | Beskrivning     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Hämtar underordnade för det aktuella klustret på nästa zoomnings nivå. Dessa underordnade kan vara en kombination av former och del kluster. Under klustren är funktioner med egenskaper som matchar kluster egenskaper. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Beräknar en zoomnings nivå som klustret börjar att utöka eller dela upp.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Hämtar alla punkter i ett kluster. Ange `limit` för att returnera en delmängd av punkterna och Använd `offset` sidan till för att gå igenom punkterna.    |

När du återger klustrade data på kartan är det ofta enklast att använda två eller flera lager. Exemplet nedan använder tre lager, ett bubbeldiagram för ritning som skalats ut färgade cirklar baserat på kluster storleken, ett symbol lager för att återge kluster storleken som text och ett andra symbol lager för att återge de ogrupperade punkterna. Det finns många andra sätt att återge klustrade data i Azure Maps markerade i data dokumentationen för [kluster punkten](./clustering-point-data-web-sdk.md) .

Du kan importera data från en data källa direkt i Azure Maps med hjälp av `importDataFromUrl` funktionen i `DataSource` klassen.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

<center>

![Azure Maps klustring](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**Ytterligare resurser**

-   [Lägg till ett symbol lager](./map-add-pin.md)
-   [Lägg till ett bubbel-lager](./map-add-bubble-layer.md)
-   [Klusterpunktdata](./clustering-point-data-web-sdk.md)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Lägg till en värme karta

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering som används för att representera densiteten för data med hjälp av en uppsättning färger. De används ofta för att visa data "frekventa fläckar" på en karta och är ett bra sätt att återge data uppsättningar med stora punkter.

Exemplen nedan läser in ett interjson-flöde för alla jord bävningar under den senaste månaden från USGS DATAUPPSÄTTNINGEN och återger dem som en värme karta.

**Före: Bing Maps**

I Bing Maps kan du skapa en värme karta genom att läsa in modulen termisk karta. På samma sätt är den interjson-modulen inläst för att lägga till stöd för data för interjson.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing Maps-termisk karta](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**Efter: Azure Maps**

I Azure Maps läser du in de interjson-data till en data källa och ansluter data källan till ett termiskt kart skikt. Du kan importera data från en data källa direkt i Azure Maps med hjälp av `importDataFromUrl` funktionen i `DataSource` klassen.

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

<center>

![Azure Maps termisk karta](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**Ytterligare resurser**

-   [Lägga till ett heatmapskikt](./map-add-heat-map-layer.md)
-   [Termisk kart skikts klass](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Alternativ för termisk kart skikt](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Använda datadrivna formatuttryck](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Täcka över ett panel lager

Med panel lager kan du täcka över stora bilder som har delats upp i mindre bild rutor som överensstämmer med Kartornas placerings system. Det här är ett vanligt sätt att täcka stora bilder eller mycket stora data mängder.

I exemplen nedan överlappar ett väderleks panels lager från Iowa Environment-Mesonet för Iowa State University som använder sig av en X, Y, zoomnings schema.

**Före: Bing Maps**

I Bing Maps kan panel lager skapas med hjälp av- `Microsoft.Maps.TileLayer` klassen.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Bing Maps viktade termisk karta](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**Efter: Azure Maps**

I Azure Maps kan ett panel lager läggas till i kartan på ungefär samma sätt som andra lager. En formaterad URL som har i x, y, zoomnings plats hållare; `{x}`, `{y}` `{z}` används för att ange skiktet för att få åtkomst till panelerna. Azure Maps panel lager också stöder `{quadkey}` `{bbox-epsg-3857}` och `{subdomain}` plats hållare.

> [!TIP]
> I Azure Maps lager kan enkelt återges under andra lager, inklusive bas kart skikt. Det är ofta önskvärt att återge panel lager under kart etiketterna så att de är lätta att läsa. `map.layers.add`Funktionen tar i en andra parameter som är ID: t för ett andra lager för att infoga det nya lagret nedan. Följande kod kan användas för att infoga ett panel lager under kart etiketterna:
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps viktad termisk karta](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> Panel begär Anden kan samlas in med hjälp av `transformRequest` kartans alternativ. Detta gör att du kan ändra eller lägga till rubriker i begäran om det behövs.

**Ytterligare resurser**

-   [Lägga till panelskikt](./map-add-tile-layer.md)
-   [Panels skikt klass](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Alternativ för panel lager](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Visa trafikdata

Trafik data kan skrivas över både Bing och Azure Maps.

**Före: Bing Maps**

I Bing Maps kan trafik data överbestämmas med hjälp av Traffic module.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Bing Maps-trafik](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**Efter: Azure Maps**

Azure Maps tillhandahåller flera olika alternativ för att Visa trafik. Trafik incidenter, till exempel väg stängningar och haverier kan visas som ikoner på kartan. Trafikflöde, färgkodade vägar, kan skrivas över på kartan och färgerna kan ändras så att de baseras på den angivna hastighets gränsen, i förhållande till den normala förväntade fördröjningen eller absolut fördröjning. Incident data i Azure Maps uppdateras varje minut och Flow-data var 2: e minut.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps trafik](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

Om du klickar på en av trafik ikonerna i Azure Maps visas ytterligare information på popup-menyn.

<center>

![Popup-Azure Maps trafik](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**Ytterligare resurser**

-   [Visa trafik på kartan](./map-show-traffic.md)
-   [Alternativ för trafik överlägg](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Trafik kontroll](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Lägg till ett mark överlägg

Både Bing och Azure Maps stöder överlägg av de refererade bilderna på kartan så att de flyttas och skalas när du panorerar och zoomar kartan. I Bing-kartor kallas dessa för mark överlägg i Azure Maps de kallas för bild lager. Detta är bra för att skapa vånings planer, täcka gamla kartor eller bilder från en drönare.

**Före: Bing Maps**

När du skapar ett mark överlägg i Bing Maps måste du ange URL: en till bilden som ska överlappa och en avgränsnings ruta för att binda bilden till på kartan. I det här exemplet läggs en kart bild av [Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

<center>

![Bing Maps, mark överlägg](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**Efter: Azure Maps**

I Azure Maps kan de refererade avbildningarna överkonfigureras med hjälp av `atlas.layer.ImageLayer` klassen. Den här klassen kräver en URL till en bild och en uppsättning koordinater för bildens fyra hörn. Avbildningen måste vara värd för antingen en domän eller ha CORs aktiverat.

> [!TIP]
> Om du bara har Nord-, syd-, väst-och rotations information och inte koordinater för varje hörn i bilden, kan du använda funktionen static [Atlas. Layer. ImageLayer. getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) .

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

<center>

![Azure Maps markplan](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**Ytterligare resurser**

-   [Lägga till en bild som överlägg](./map-add-image-layer.md)
-   [Bild skikts klass](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Lägg till KML-data till kartan

Både Azure-och Bing Maps kan importera och återge KML-, KMZ-, GeoRSS-, interjson-och Well-Known text (well) data på kartan. Azure Maps stöder också GPX, GML, spatiala CSV-filer, Web-Mapping Services (WMS), Web-Mapping panel tjänster (WMTS) och Web Feature Services (WFS).

**Före: Bing Maps**

Om den här koden körs i en webbläsare visas en karta som ser ut som på följande bild:

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Bing Maps-KML](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**Efter: Azure Maps**

I Azure Maps är interjson det viktigaste data formatet som används i webb-SDK, och ytterligare avstånd för spatialdata kan enkelt integreras i med hjälp av den [spatiala IO-modulen](/javascript/api/azure-maps-spatial-io/). Den här modulen har funktioner för både läsning och skrivning av spatialdata och innehåller även ett enkelt data lager som enkelt kan återge data från något av dessa avstånds data format. Om du vill läsa data i en spatialdata, skickar du en URL eller rå data som sträng eller BLOB till `atlas.io.read` funktionen. Då returneras alla parsade data från filen som sedan kan läggas till i kartan. KML är lite mer komplex än det mest spatialdata data formatet, eftersom det innehåller mycket mer information om formatering. Klassen har stöd för att `SpatialDataLayer` återge majoriteten av dessa format, men ikoner för bilder måste läsas in i kartan före inläsning av funktions data och bas överlägg måste läggas till som lager till kartan separat. Vid inläsning av data via en URL bör den finnas på en CORs-aktiverad slut punkt, eller också ska en proxyserver skickas som ett alternativ till funktionen Read.

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                atlas.io.read('myKMLFile.kml').then(async r => {
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

<center>

![Azure Maps KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**Ytterligare resurser**

-   [funktionen Atlas. io. Read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Lägg till rit verktyg

Både Bing och Azure Maps ger en modul som lägger till möjligheten för användaren att rita och redigera former på kartan med musen eller någon annan inmatnings enhet. Båda har stöd för att rita kartnålar, linjer och polygoner. Azure Maps innehåller också alternativ för att rita cirklar och rektanglar.

**Före: Bing Maps**

I Bing Maps `DrawingTools` läses modulen in med hjälp av `Microsoft.Maps.loadModule` funktionen. När den har lästs in kan en instans av klassen DrawingTools skapas och `showDrawingManager` funktionen anropas Lägg till ett verktygsfält till kartan.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Rit verktyg för Bing Maps](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**Efter: Azure Maps**

I Azure Maps modulen för rit verktyg måste läsas in genom att läsa in JavaScript-och CSS-filerna måste refereras till i appen. När kartan har lästs in kan en instans av `DrawingManager` klassen skapas och en `DrawingToolbar` instans är ansluten.

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Azure Maps rit verktyg](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> I Azure Maps lager kan du använda rit verktygen på flera sätt som användarna kan rita former. När du till exempel ritar en polygon kan användaren klicka för att lägga till varje punkt, eller hålla ned vänster musknapp och dra musen för att rita en bana. Detta kan ändras med hjälp `interactionType` av alternativet `DrawingManager` .

**Ytterligare resurser**

-   [Dokumentation](./set-drawing-options.md)
-   [Kodexempel](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>Nästa steg

Ta en titt på [Azure Maps Web SDK-modulerna med öppen källkod](open-source-projects.md#open-web-sdk-modules). Dessa moduler ger en ton av ytterligare funktioner och är helt anpassningsbara.

Granska kod exempel relaterad migrering av andra Bing Maps-funktioner:

**Datavisualiseringar**

> [!div class="nextstepaction"]
> [Kon tur lager](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Data diskretisering](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Tjänster**

> [!div class="nextstepaction"]
> [Använda modulen Azure Maps tjänster](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Sök efter orienteringspunkter](./map-search-location.md)

> [!div class="nextstepaction"]
> [Hämta information från en koordinat (omvänd landskod)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)

> [!div class="nextstepaction"]
> [Sök automatiska förslag med JQuery-användargränssnitt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Läs mer om Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Använda kart kontrollen](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Använda modulen tjänster](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Använda modulen verktyg för ritning](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Dokumentation om Azure Maps Web SDK-tjänstens API-referens](/javascript/api/azure-maps-control/)
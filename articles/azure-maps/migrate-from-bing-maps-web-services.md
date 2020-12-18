---
title: 'Självstudie: Migrera webb tjänster från Bing Maps | Microsoft Azure Maps'
description: Själv studie kurs om hur du migrerar webb tjänster från Bing Maps till Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679075"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>Självstudie: Migrera webb tjänsten från Bing Maps

Både Azure-och Bing Maps ger till gång till spatiala API: er via REST-webbtjänster. API-gränssnitten för dessa plattformar utför liknande funktioner men använder olika namngivnings konventioner och svars objekt. I den här självstudien får du lära dig hur man:

> * Forward och omvända polykodning
> * Söka efter platser av intresse
> * Beräkna vägar och vägbeskrivningar
> * Hämta en kart bild
> * Beräkna en avstånds mat ris
> * Hämta tids zons information

Följande tabell innehåller API: er för Azure Maps tjänsten som tillhandahåller liknande funktioner för API: er för Bing Maps-tjänsten.

| API för Bing Maps-tjänsten                 | API för Azure Maps tjänsten      |
|---------------------------------------|-----------------------------|
| Automatiska förslag                           | [Sök](/rest/api/maps/search)     |
| Vägvisningar (inklusive Truck)          | [Vägvisningar](/rest/api/maps/route/getroutedirections)                          |
| Avstånds mat ris                       | [Väg mat ris](/rest/api/maps/route/postroutematrixpreview)                          |
| Bilder – statisk karta                  | [Rendering](/rest/api/maps/render/getmapimage)                                   |
| ISO Kron                            | [Flödes intervall](/rest/api/maps/route/getrouterange)                                    |
| Lokala insikter                        | [Sök](/rest/api/maps/search)  +  [Flödes intervall](/rest/api/maps/route/getrouterange)    |
| Lokal sökning                          | [Sök](/rest/api/maps/search)     |
| Plats igenkänning (POI: er)           | [Sök](/rest/api/maps/search)     |
| Platser (omvänt/omvända landskod) | [Sök](/rest/api/maps/search)                                               |
| Fäst vid väg                          | [Riktning efter väg](/rest/api/maps/route/postroutedirections)                         |
| Avstånds Data Services (SDS)           | [Sök](/rest/api/maps/search)  +  [Routning](/rest/api/maps/route) och andra Azure-tjänster |
| Tidszon                             | [Tidszon](/rest/api/maps/timezone)  |
| Trafik incidenter                     | [Information om trafik incident](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Höjning                             | [Höjning (förhands granskning)](/rest/api/maps/elevation)

Följande tjänst-API: er är för närvarande inte tillgängliga i Azure Maps:

-   Optimerade res vägar – planerat. Azure Maps väg-API: et stöder rese optimering av säljprogram för ett enda fordon.
-   Bilder metadata – används främst för att hämta panel-URL: er i Bing Maps. Azure Maps har en fristående tjänst för direkt åtkomst till kart paneler.

Azure Maps har flera andra REST-webbtjänster som kan vara av intresse,

-   [Azure Maps Creator (för hands version) ](./creator-indoor-maps.md) – skapa en anpassad privat Digital byggnad av byggnader och utrymmen.
-   [Spatialdata](/rest/api/maps/spatial) – avlastning av komplexa spatiala beräkningar och åtgärder, t. ex. polystaket, i en tjänst.
-   [Kart paneler](/rest/api/maps/render/getmaptile) – åtkomst till väg-och bilder-paneler från Azure Maps som raster-och vektor paneler.
-   [Batchbearbetning](/rest/api/maps/route/postroutedirectionsbatchpreview) – tillåter upp till 1 000 flödes begär Anden som görs i en enda batch under en viss tids period. Vägar beräknas parallellt på servern för snabbare bearbetning.
-   [Trafik](/rest/api/maps/traffic) Flow – åtkomst till trafik flödes data i real tid som både raster och vektor paneler.
-   [API för geolokalisering (för hands version)](/rest/api/maps/geolocation/getiptolocationpreview) – hämta platsen för en IP-adress.
-   [Väder tjänster](/rest/api/maps/weather) – få till gång till väder data i real tid och prognoser.

Se även följande guider för bästa praxis:

-   [Metodtips för sökning](./how-to-use-best-practices-for-search.md)
-   [Metod tips för routning](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Adresser för att koda

Kod för att omvandla en adress (till exempel `"1 Microsoft way, Redmond, WA"` ) till en koordinat (t. ex. longitud:-122,1298, latitud: 47,64005). Koordinater används ofta för att placera en kartnål på en karta eller centrera en karta.

Azure Maps tillhandahåller flera metoder för att koda adresser.

-   [Kod för fri Forms adress](/rest/api/maps/search/getsearchaddress): Ange en enskild adress sträng (som `"1 Microsoft way, Redmond, WA"` ) och bearbeta begäran omedelbart. Den här tjänsten rekommenderas om du snabbt behöver koda enskilda adresser.
-   [Kodning av strukturerad adress](/rest/api/maps/search/getsearchaddressstructured): Ange delar av en enskild adress, till exempel gatu namn, stad, land och post nummer och bearbeta begäran omedelbart. Den här tjänsten rekommenderas om du snabbt behöver koda enskilda adresser och data redan har tolkats i sina enskilda adress delar.
-   [Kodning av batch-adresser](/rest/api/maps/search/postsearchaddressbatchpreview): skapa en begäran som innehåller upp till 10 000 adresser och behandla dem under en viss tids period. Alla adresser kommer att kodas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned. Den här tjänsten rekommenderas för att kunna koda stora data mängder.
-   [Fuzzy-sökning](/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Det här API: et tar i en sträng med fri form som kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori och bearbeta begäran omedelbart. Detta API rekommenderas för program där användare kan söka efter adresser eller intressanta punkter från samma text ruta.
-   [Fuzzy batch-sökning](/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkter och låta dem bearbetas under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.

I följande tabeller sker en kors referens till Bing Maps API-parametrar med de jämförbara API-parametrarna i Azure Maps för strukturerad och kostnads fri Forms kodning.

**Plats efter adress (strukturerad adress)**

| API-parameter för Bing Maps              | Jämförbar Azure Maps API-parameter                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber``streetName`eller`crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` och `countryCode`                     |
| `locality`                         | `municipality` eller `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | Ej tillämpligt – returneras alltid av Azure Maps om det är tillgängligt.   |
| `include` (`incl`)               | N/A – lands ISO2 kod returneras alltid av Azure Maps. |
| `key`                              | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)                  | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) . |
| `userRegion` (`ur`)              | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

Azure Maps stöder också;

* `countrySecondarySubdivision` – County, distrikt
* `countryTertiarySubdivision` -Namngivna områden; boroughs, cantons, communes
* `ofs` – Sida genom resultaten i kombination med `maxResults` parameter.

**Plats efter fråga (adress sträng med fri form)**

| API-parameter för Bing Maps              | Jämförbar Azure Maps API-parameter      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | Ej tillämpligt – returneras alltid av Azure Maps om det är tillgängligt.  |
| `include` (`incl`)               | N/A – lands ISO2 kod returneras alltid av Azure Maps.  |
| `key`                              | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)                  | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .  |
| `userRegion` (`ur`)              | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

Azure Maps stöder också;

* `typeahead` – Arter om frågan kommer att tolkas som en del Indatatyp och sökningen kommer att ange förutsägande läge (autoföreslå/komplettera automatiskt).
* `countrySet` – En kommaavgränsad lista över koder för ISO2 länder där du kan begränsa sökningen till.
* `lat`/`lon`, `topLeft` / `btmRight` , `radius` – Ange användarens plats och område för att göra resultaten mer lokalt relevanta.
* `ofs` – Sida genom resultaten i kombination med `maxResults` parameter.

Ett exempel på hur du använder Sök tjänsten finns dokumenterat [här](./how-to-search-for-address.md). Se till att gå igenom [metod tipsen för Sök efter](./how-to-use-best-practices-for-search.md) dokumentation.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Omvändly-kod a-koordinat (hitta en plats efter punkt)

Omvänd polykodning är processen att konvertera geografiska koordinater (t. ex. longitud:-122,1298, latitud: 47,64005) till dess ungefärliga adress (t `"1 Microsoft way, Redmond, WA"` . ex.).

Azure Maps ger flera omvända Omkodnings metoder;

-   [Omvänd landskod](/rest/api/maps/search/getsearchaddressreverse): Ange en enda geografisk koordinat för att få den ungefärliga adressen och bearbeta begäran omedelbart.
-   [Kors gatan omvänd landskod](/rest/api/maps/search/getsearchaddressreversecrossstreet): Ange en enda geografisk koordinat för att få närliggande kors gatu information (till exempel 1st & main) och bearbeta begäran omedelbart.
-   [Omvänd landskod för batch-adress](/rest/api/maps/search/postsearchaddressreversebatchpreview): skapa en begäran som innehåller upp till 10 000 koordinater och har bearbetats under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps              | Jämförbar Azure Maps API-parameter       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Se jämförelse tabell för entitetstyp nedan.    |
| `includeNeighborhood` (`inclnb`)     | Ej tillämpligt – returneras alltid av Azure Maps om det är tillgängligt.         |
| `include` (`incl`)                   | N/A – lands ISO2 kod returneras alltid av Azure Maps.    |
| `key`                                | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)                      | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .   |
| `userRegion` (`ur`)                  | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

Se till att gå igenom [metod tipsen för Sök efter](./how-to-use-best-practices-for-search.md) dokumentation.

API: et för omväntitet i Azure Maps har vissa ytterligare funktioner som inte är tillgängliga i Bing Maps som kan vara användbara för att integrera när du migrerar appen:

* Hämta data för hastighets begränsning.
* Hämta information om användning av resande; lokal väg, arterial, begränsad åtkomst, ramp osv.
* Den sida på gatan som koordinaten infaller på.

**Jämförelse tabell över entitetstyp**

Följande tabell refererar till värdena för Bing Maps-enhets typen till motsvarande egenskaps namn i Azure Maps.

| Enhets typ för Bing Maps | Typ av jämförbar Azure Maps enhet               | Beskrivning                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adress*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Stadsdel*                             |
| `PopulatedPlace`      | `Municipality` eller `MunicipalitySubdivision`     | *Stad*, *stad eller sub* eller *Super City*     |
| `Postcode1`           | `PostalCodeArea`                                | *Post nummer* eller *post* nummer                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Region*                       |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Län* eller *distrikt*                    |
| `CountryRegion`       | `Country`                                       | *Lands namn*                             |
|                       | `CountryTertiarySubdivision`                    | *Boroughs*, *cantons*, *communes*          |

## <a name="get-location-suggestions-autosuggest"></a>Hämta förslag på platser (automatiska förslag)

Flera av API: erna för Azure Maps Search-API: et stöder förutsägbart läge som kan användas för automatiska förslags scenarier. API Azure Maps för automatiska förslag för Bing Maps är det mest precis som [Bing Maps API](/rest/api/maps/search/getsearchfuzzy) för automatiska förslag. Följande API: er har också stöd för förutsägande läge, Lägg till `&typeahead=true` i frågan.

-   [Kod för fri Forms adress](/rest/api/maps/search/getsearchaddress): Ange en enskild adress sträng (som `"1 Microsoft way, Redmond, WA"` ) och bearbeta begäran omedelbart. Den här tjänsten rekommenderas om du snabbt behöver koda enskilda adresser.
-   [Fuzzy-sökning](/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Det här API: et tar i en sträng med fri form som kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori och bearbeta begäran omedelbart. Detta API rekommenderas för program där användare kan söka efter adresser eller intressanta punkter från samma text ruta.
-   [POI sökning](/rest/api/maps/search/getsearchpoi): Sök efter intressanta platser efter namn. Exempel: `"starbucks"`.
-   [POI kategoris ökning](/rest/api/maps/search/getsearchpoicategory): Sök efter intressanta platser efter kategori. Exempel: "restaurang".

## <a name="calculate-routes-and-directions"></a>Beräkna vägar och vägbeskrivningar

Azure Maps kan användas för att beräkna vägar och riktningar. Azure Maps har många av samma funktioner som Bing Maps-routningstjänsten, till exempel;

* tider för införsel och avresa
* vägar i real tids-och förutsägande baserade trafik
* olika transport sätt; framförande, promenad, Last bil
* waypoint order optimering (rese försäljare)

> [!NOTE]
> Azure Maps kräver att alla waypoints är koordinater. Adresser måste vara kodade först.

Tjänsten Azure Maps routning innehåller följande API: er för att beräkna vägar.

-   [Beräkna väg](/rest/api/maps/route/getroutedirections): beräkna en väg och behandla begäran omedelbart. Detta API stöder både GET-och POST-förfrågningar. POST-begäranden rekommenderas när du anger ett stort antal waypoints eller när du använder flera väg alternativ för att säkerställa att URL-begäran inte blir för lång och orsaka problem.
-   [Batch-väg](/rest/api/maps/route/postroutedirectionsbatchpreview): skapa en begäran som innehåller upp till 1 000 cirkulations förfrågan och behandla dem under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.
-   [Mobilitets tjänster (för hands version) ](/rest/api/maps/mobility): beräkna vägar och vägbeskrivning med hjälp av offentlig överföring.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps                                    | Jämförbar Azure Maps API-parameter               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` eller `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | Saknas                                               |
| `distanceUnit` (`du`)                                      | Ej tillämpligt – Azure Maps endast använder Metric-systemet.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` , `minDeviationDistance` , och `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` och `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` eller `arriveAt`                          |
| `tolerances` (`tl`)                                        | Saknas                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` ( `wp.n` ) eller `viaWaypoint.n` (`vwp.n`)         | `query` – koordinater i formatet `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)                                            | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) . |
| `userRegion` (`ur`)                                        | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

API för Azure Maps routning stöder också Truck-routning inom samma API. I följande tabell beskrivs de ytterligare Bing Maps-parametrarna för Truck-routning med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps                  | Jämförbar Azure Maps API-parameter        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | Ej tillämpligt – dimensioner i meter stöds bara. |
| `weightUnit` (`wu`)                      | Ej tillämpligt – vikt i kilogram stöds bara. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **Saknas**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **Saknas**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **Saknas**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **Saknas**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **Saknas**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Som standard returnerar API för Azure Maps väg bara en sammanfattning (avstånd och tider) och koordinaterna för vägens sökväg. Använd `instructionsType` parametern för att hämta inaktiverade instruktioner. `routeRepresentation`Parametern kan användas för att filtrera ut sammanfattningen och vägens sökväg.

Se också till att du läser igenom de [bästa metoderna för routning av](./how-to-use-best-practices-for-routing.md) dokumentation.

API för Azure Maps routning har många ytterligare funktioner som inte är tillgängliga i Bing Maps som kan vara användbara att integrera när du migrerar appen:

* Stöd för väg typ: kortaste, snabbast, trilling och mest bränsle effektiv.
* Stöd för ytterligare rese lägen: cykel, buss, motorcykel, taxi, Truck och van.
* Stöd för 150 waypoints.
* Beräkna flera rese tider i en enskild begäran. historisk trafik, direkt trafik, ingen trafik.
* Undvik ytterligare väg typer: Carpool-vägar, unpaved-vägar, redan använda vägar.
* Motor specifikation-baserad routning. Beräkna vägar för förbrännings-eller elmotor fordon baserat på deras återstående bränsle-/avgifts-och motor specifikationer.
* Ange högsta fordons hastighet.

## <a name="snap-coordinates-to-road"></a>Fäst koordinater på väg

Det finns flera sätt att fästa koordinater på vägar i Azure Maps.

* Använd väg riktnings-API: et för att fästa koordinater mot en logisk väg i vägtrafik nätverket.
* Använd Azure Maps Web SDK för att fästa enskilda koordinater mot närmaste väg i vektor panelerna.
* Använd Azure Maps vektor paneler direkt för att fästa enskilda koordinater.

**Använda väg riktnings-API för att fästa koordinater**

Azure Maps kan fästa koordinater mot vägar genom att använda [väg riktnings](/rest/api/maps/route/postroutedirections) -API: et. Den här tjänsten kan användas för att rekonstruera en logisk väg mellan en uppsättning koordinater och är jämförbar med Bing Maps snap till Road API.

Det finns två olika sätt att använda väg riktnings-API: et för att fästa koordinater på vägar.

* Om det finns 150 koordinater eller mindre, kan de skickas som waypoints i API för att hämta väg riktningar. Med den här metoden kan två olika typer av fästa data hämtas. flödes instruktioner kommer att innehålla de enskilda fästa waypoints, medan väg Sök vägen har en interpolerad uppsättning koordinater som fyller den fullständiga sökvägen mellan koordinaterna.
* Om det finns fler än 150 koordinater kan du använda API: et för POST vägs riktningar. Koordinaternas start-och slut koordinater måste skickas till Frågeparametern, men alla koordinater kan skickas till- `supportingPoints` parametern i postens brödtext och formaterats som en geometrisk samling av punkter i en geometrisk JSON-samling. De enda fästa data som är tillgängliga med den här metoden är väg Sök vägen som är en interpolerad uppsättning koordinater som fyller den fullständiga sökvägen mellan koordinaterna. [Här är ett exempel](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) på den här metoden med modulen tjänster i Azure Maps Web SDK.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps    | Jämförbar Azure Maps API-parameter                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – skicka dessa punkter till bröd texten i post-begäran  |
| `interpolate`              | Saknas                                                                 |
| `includeSpeedLimit`        | Saknas                                                                 |
| `includeTruckSpeedLimit`   | Saknas                                                                 |
| `speedUnit`                | Saknas                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)            | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .   |
| `userRegion` (`ur`)        | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) .   |

API: et för Azure Maps routning stöder också en väg för Truck i samma API för att se till att logiska sökvägar beräknas. I följande tabell beskrivs de ytterligare Bing Maps-parametrarna för Truck-routning med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps                 | Jämförbar Azure Maps API-parameter        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | Ej tillämpligt – dimensioner i meter stöds bara. |
| `weightUnit` (`wu`)                     | Ej tillämpligt – vikt i kilogram stöds bara. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **Saknas**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **Saknas**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **Saknas**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **Saknas**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **Saknas**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Eftersom den här metoden använder väg riktnings-API: et kan en fullständig uppsättning alternativ i den tjänsten användas för att anpassa den logik som används för att fästa koordinaten på vägar. Om du till exempel anger en avgångs tid skulle det leda till att historiska trafik data beaktas.

API: et för Azure Maps väg riktningar returnerar för närvarande inte hastighets begränsnings data, men de kan hämtas med hjälp av Azure Maps omvänte-API för omkodning.

**Använda webb-SDK för att fästa koordinater**

Azure Maps Web SDK använder vektor paneler för att återge Maps. Dessa vektor paneler innehåller information om den obehandlade vägens geometri och kan användas för att beräkna närmaste väg till en koordinat för enkel fästning av enskilda koordinater. Detta är användbart när du vill att koordinaterna visuellt ska visas över vägar och du redan använder Azure Maps Web SDK för att visualisera data.

Den här metoden fäster dock endast mot väg segment som läses in i kart visningen. När du zoomar ut på land-nivå kan det finnas ingen väg information, så det går inte att fästa på den zoomnings nivån, men på den zoomnings nivån kan en enda pixel representera arean av flera stads block, så att fästfunktionen inte behövs. För att åtgärda detta kan Fästnings logiken tillämpas varje gång som kartan har flyttats. [Här är ett kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) som visar detta.

**Använda Azure Maps vektor paneler direkt till fästa koordinater**

Azure Maps vektor panelerna innehåller geometriska geometriska data som kan användas för att beräkna närmaste punkt på en väg till en koordinat för att utföra grundläggande fästning av enskilda koordinater. Alla väg segment visas i sektorerna på zoomnings nivå 15, så du kommer att vilja hämta paneler därifrån. Sedan kan du använda [quadtree-panelens Pyramid matematik](./zoom-levels-and-tile-grid.md) för att fastställa att paneler behövs och konvertera panelerna till Geometries. Härifrån kan du använda ett överliggande matematiskt bibliotek, till exempel [Turf JS](http://turfjs.org/) eller [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) , för att beräkna de närmaste linje segmenten.

## <a name="retrieve-a-map-image-static-map"></a>Hämta en kart bild (statisk karta)

Azure Maps tillhandahåller ett API för att återge statiska kart bilder med data som har överanvänds. API: t för [rendering av Azure Maps Map-avbildningen](/rest/api/maps/render/getmapimagerytile) är jämförbar med API: t för statisk mappning i Bing Maps.

> [!NOTE]
> Azure Maps kräver att Center, alla kartnålar och Sök vägs platser är koordinater i `longitude,latitude` formatet, medan Bing Maps använder `latitude,longitude` formatet.</p>
<p>Adresser måste vara kodade först.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps  | Jämförbar Azure Maps API-parameter            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – anges som en del av URL-sökvägen. För närvarande stöds endast PNG.  |
| `heading`                | Ej tillämpligt – Streetside stöds inte.                |
| `imagerySet`             | `layer` och `style` – se dokumentation om [mappnings format som stöds](./supported-map-styles.md) .   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | Saknas                                            |
| `mapSize` (`ms`)         | `width` och `height` – kan vara upp till 8192x8192 i storlek. |
| `declutterPins` (`dcl`)  | Saknas                                            |
| `dpi`                    | Saknas                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | Saknas                                            |
| `pitch`                  | Ej tillämpligt – Streetside stöds inte.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A – centrera eller markerings ram måste användas.     |
| `highlightEntity` (`he`) | Saknas                                            |
| `style`                  | Saknas                                            |
| flödes parametrar         | Saknas                                            |
| `key`                    | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)          | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .   |
| `userRegion` (`ur`)      | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

> [!NOTE]
> Azure Maps använder ett panel system med paneler som är två gånger så stora som de kart paneler som används i Bing Maps. Det innebär att värdet för zoomnings nivå i Azure Maps visas på en zoomnivå närmare Azure Maps jämfört med Bing Maps. Minska zoomnings nivån i de begär Anden som du migrerar med 1 för att kompensera för detta.

Mer information finns i [instruktions guiden om kart bild åter givning](./how-to-render-custom-data.md) .

Förutom att kunna generera en statisk kart bild ger tjänsten Azure Maps åter givning också möjlighet att direkt komma åt kart paneler i raster (PNG) och vektor format.

-   [Kart panel](/rest/api/maps/render/getmaptile) – Hämta raster (png) och vektor paneler för bas Maps (vägar, gränser, bakgrund).
-   [Kart bilder panel](/rest/api/maps/render/getmapimagerytile) – Hämta paneler för flyg-och satellit bilder.

### <a name="pushpin-url-parameter-format-comparison"></a>Jämförelse av URL-parameter format för kartnål

**Före: Bing Maps**

I Bing Maps kan kartnålar läggas till i en statisk kart bild genom att använda- `pushpin` parametern i URL: en. `pushpin`Parametern tar på en plats i `latitude,longitude` formatet, en ikon stil och text etikett (upp till tre tecken) som visas nedan:

> `&pushpin=latitude,longitude;iconStyle;label`

Ytterligare kartnålar kan läggas till genom att lägga till ytterligare `pushpin` parametrar till URL: en med en annan uppsättning värden. Ikon formaten för kartnål är begränsade till en av de fördefinierade formaten i Bing Maps-API: et.

I Bing Maps kan till exempel en röd kartnål med etiketten "AB" läggas till i kartan vid koordinater (longitud:-110, latitud: 45) med följande URL-parameter:

> `&pushpin=45,-110;7;AB`

![PIN-kod för Bing Maps statisk mappning](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**Efter: Azure Maps**

I Azure Maps kan kartnålar också läggas till i en statisk kart bild genom att ange `pins` parametern i URL: en. Kartnålar i Azure Maps definieras genom att ange ett ikon format och en lista över platser som använder det ikon formatet. Den här informationen skickas sedan till `pins` parametern kan anges flera gånger för att stödja kartnålar med olika format.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Ytterligare format kan användas genom att lägga till ytterligare `pins` parametrar till URL: en med ett annat format och en uppsättning med platser.

När det kommer till att fästa platser måste Azure Maps ha koordinaterna i `longitude latitude` formatet, medan Bing Maps använder `latitude,longitude` format. Observera också att **det finns ett blank steg, inte en kommaavgränsad** longitud och latitud i Azure Maps.

`iconType`Värdet anger typen av PIN-kod som ska skapas och kan ha följande värden:

* `default` – Standard ikonen för PIN-kod.
* `none` – Ingen ikon visas, endast etiketter kommer att återges.
* `custom` – Anger att en anpassad ikon ska användas. En URL som pekar på ikon bilden kan läggas till i slutet av `pins` parametern efter PIN-kodens plats information.
* `{udid}` – Ett unikt data-ID (UDID) för en ikon som lagras i Azure Maps data lagrings plattform.

PIN-format i Azure Maps läggs till med formatet `optionNameValue` , med flera format åtskiljda med pipe ( `|` )-tecken som detta `iconType|optionName1Value1|optionName2Value2` . Observera att alternativ namn och värden inte separeras. Följande format alternativ namn kan användas för att formatera kartnålar i Azure Maps:

* `al` – Anger kartnålens opacitet (alfa). Kan vara ett tal mellan 0 och 1.
* `an` – Anger fäst punkten. X-och y-pixelvärdena anges i formatet `x y` .
* `co` – PIN-kodens färg. Måste vara 24-bitars hex-färg: `000000` till `FFFFFF` .
* `la` – Anger etikettens ankare. X-och y-pixelvärdena anges i formatet `x y` .
* `lc` – Etikettens färg. Måste vara 24-men hex-färg: `000000` till `FFFFFF` .
* `ls` – Etikettens storlek i bild punkter. Kan vara ett tal som är större än 0.
* `ro` – Ett värde i grader för att rotera ikonen. Kan vara ett tal mellan-360 och 360.
* `sc` – Ett skalnings värde för PIN-ikonen. Kan vara ett tal som är större än 0.

Etikett värden anges för varje PIN-plats i stället för att ha ett enda etikett värde som gäller för alla kartnålar i listan över platser. Etikett svärdet kan vara en sträng med flera tecken och måste omslutas med enkla citat tecken för att säkerställa att den inte är förväxlad som ett format eller ett plats värde.

I Azure Maps lägger du till exempel till en röd ( `FF0000` ) standard ikon med etiketten "utrymmes nål" placerad under (15 50) ikonen vid koordinater (longitud:-122,349300, latitud: 47,620180) kan göras med följande URL-parameter:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![PIN-kod för Azure Maps statisk mappning](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

I följande exempel läggs tre PIN-koder till med etikett värden "1", "2" och "3":

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Azure Maps statisk karta flera PIN-bara](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>& Rita kurva URL-parameter format jämförelse

**Före: Bing Maps**

I Bing Maps, rader och polygoner kan läggas till i en statisk kart bild genom att använda- `drawCurve` parametern i URL: en. `drawCurve`Parametern tar i en formtyp, en typ av typ och en lista över platser som ska återges på kartan enligt nedan:

> `&drawCurve=shapeType,styleType,location1,location2...`

Ytterligare format kan användas genom att lägga till ytterligare `drawCurve` parametrar till URL: en med ett annat format och en uppsättning med platser.

Platser i Bing Maps anges med formatet `latitude1,longitude1_latitude2,longitude2_…` . Platser kan också kodas.

Form typer i Bing Maps innehåller linjer, polygoner, cirkel och kurva. Format typerna är linje färg, linje tjock lek, kon tur färg, fyllnings färg, kon tur och cirkulär radie.

I Bing Maps kan till exempel en blå linje med 50% opacitet och en tjocklek på fyra pixlar läggas till i kartan mellan koordinater (longitud:-110, latitud: 45 och longitud:-100, latitud: 50) med följande URL-parameter:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Statisk kart linje i Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**Efter: Azure Maps**

I Azure Maps kan linjer och polygoner också läggas till i en statisk kart bild genom att ange parametern *Path* i URL: en. Precis som Bing Maps kan en stil och en lista med platser anges i den här parametern och parametern *Path* kan anges flera gånger för att återge flera cirklar, linjer och polygoner med olika format.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

När den kommer till sökvägar måste Azure Maps ha koordinaterna i `longitude latitude` formatet medan Bing Maps använder `latitude,longitude` format. Observera också att **det finns ett blank steg, inte en kommaavgränsad** longitud och latitud i Azure Maps. Azure Maps stöder för närvarande inte kodade sökvägar. Större data uppsättningar kan laddas upp som en interjson-fyllning i Azure Maps data lagrings-API: et som dokumenteras [här](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Sök vägs format i Azure Maps läggs till med formatet `optionNameValue` , med flera format åtskiljda med pipe ( `|` )-tecken som detta `optionName1Value1|optionName2Value2` . Observera att alternativ namn och värden inte separeras. Följande format alternativ namn kan användas för att formatera sökvägar i Azure Maps:

* `fa` – Fyllnings färg opaciteten (alpha) som används vid åter givning av polygoner. Kan vara ett tal mellan 0 och 1.
* `fc` – Fyllnings färgen som används för att återge ytan i en polygon.
* `la` – Linje färg opaciteten (alfa) som används vid åter givning av linjer och konturen av polygoner. Kan vara ett tal mellan 0 och 1.
* `lc` – Linje färgen som används för att återge linjer och konturen för polygoner.
* `lw` – Bredden på linjen i bild punkter.
* `ra` – Anger en cirkel-radie i meter.

I Azure Maps kan till exempel en blå linje med 50% opacitet och en tjocklek på fyra pixlar läggas till i kartan mellan koordinaterna (longitud:-110, latitud: 45 och longitud:-100, latitud: 50) med följande URL-parameter:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Azure Maps statisk kart linje](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>Beräkna en avstånds mat ris

Azure Maps innehåller ett API för att beräkna res tiderna och avståndet mellan en uppsättning platser som en distans mat ris. API: et för Azure Maps avstånds mat ris är jämförbar med API för distans mat ris i Bing Maps.

-   [Väg mat ris](/rest/api/maps/route/postroutematrixpreview): beräknar asynkrona res tider och avstånd för en uppsättning ursprung och mål. Upp till 700 celler per begäran stöds (antalet ursprung multiplicerat med antalet destinationer). Med den begränsningen i åtanke är exempel på möjliga mat ris dimensioner: `700x1` , `50x10` ,, `10x10` `28x25` , `10x70` .

> [!NOTE]
> En begäran till distans mat ris API: et kan bara göras med en POST-begäran med information om ursprung och mål i bröd texten i begäran.</p>
<p>Dessutom kräver Azure Maps att alla ursprung och mål måste vara koordinater. Adresser måste vara kodade först.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps | Jämförbar Azure Maps API-parameter                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – Ange i POSTens brödtext som en JSON.    |
| `destinations`          | `destination` – Ange i POSTens brödtext som en JSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | Saknas                                                         |
| `distanceUnit`          | Ej tillämpligt – alla avstånd i meter.                              |
| `timeUnit`              | Ej tillämpligt – alla tider på några sekunder.                                 |
| `key`                   | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)         | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .  |
| `userRegion` (`ur`)     | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) .     |

> [!TIP]
> Alla avancerade vägvals alternativ som är tillgängliga i Azure Maps routnings-API: t (Truck-routning, motor-specifikationer, undvika...) stöds i API för Azure Maps avstånds mat ris.

## <a name="calculate-an-isochrone"></a>Beräkna en isochrone

Azure Maps tillhandahåller ett API för att beräkna en isochrone, en polygon som täcker ett utrymme som kan skickas till i vilken riktning som helst från en ursprungs plats inom en angiven tids period eller mängd bränsle/avgift. API: et för Azure Maps väg intervall är jämförbar med isochrone-API: et i Bing Maps.

-   [Dirigera](/rest/api/maps/route/getrouterange) Intervall * *: beräkna en polygon som täcker ett område som kan överföras till i någon riktning från en ursprungs punkt inom en angiven tids period, avstånd eller mängd bränsle/kostnad tillgängligt.

> [!NOTE]
> Azure Maps kräver att frågans ursprung är en koordinat. Adresser måste vara kodade först.</p>
<p>Bing Maps kan dessutom beräkna ISO Kron baserat på tid eller avstånd, medan Azure Maps kan beräkna ISO Kron baserat på tid, avstånd eller mängd bränsle/kostnad som är tillgängliga.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps      | Jämförbar Azure Maps API-parameter            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | Ej tillämpligt – alla tider på några sekunder.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | Ej tillämpligt – alla avstånd i meter.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)              | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .  |
| `userRegion` (`ur`)          | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

> [!TIP]
> Alla avancerade vägvals alternativ som är tillgängliga i Azure Maps routnings-API: t (Truck-routning, motor-specifikationer, Undvik...) stöds i API: et för Azure Maps isochrone.

## <a name="search-for-points-of-interest"></a>Söka efter platser av intresse

Punkt för intressanta data kan sökas i Bing Maps med hjälp av följande API: er:

-   **Lokal sökning:** Söker efter intressanta punkter i närheten (radiell sökning), efter namn eller enhets typ (kategori). Den Azure Maps [POI Sök](/rest/api/maps/search/getsearchpoi) -och [POI för kategori sökning](/rest/api/maps/search/getsearchpoicategory) är mest likt detta API.
-   **Plats igenkänning**: söker efter intressanta punkter som ligger inom ett visst avstånd från en plats. [Söknings](/rest/api/maps/search/getsearchnearby) -API: t i närheten Azure Maps i närheten liknar det här API: et.
-   **Lokala insikter:** Söker efter ansvars punkter som ligger inom den angivna maximala kör tiden eller avståndet från en specifik koordinat. Detta är möjligt med Azure Maps genom att först beräkna en isochrone och sedan skicka den till [sökningen i Geometry](/rest/api/maps/search/postsearchinsidegeometry) -API.

Azure Maps tillhandahåller flera Sök-API: er för intressanta punkter:

-   [POI sökning](/rest/api/maps/search/getsearchpoi): Sök efter intressanta platser efter namn. Exempel: `"starbucks"`.
-   [POI kategoris ökning](/rest/api/maps/search/getsearchpoicategory): Sök efter intressanta platser efter kategori. Exempel: "restaurang".
-   [Närliggande sökning](/rest/api/maps/search/getsearchnearby): söker efter anslags punkter som ligger inom ett visst avstånd från en plats.
-   [Fuzzy-sökning](/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Det här API: et tar i en sträng med fri form som kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori och bearbeta begäran omedelbart. Detta API rekommenderas för program där användare kan söka efter adresser eller intressanta punkter från samma text ruta.
-   [Sök i geometrin](/rest/api/maps/search/postsearchinsidegeometry): Sök efter anslags punkter som är inom en angiven geometri (Polygon).
-   [Sök längs väg](/rest/api/maps/search/postsearchalongroute): Sök efter anslags punkter som är utmed en angiven väg Sök väg.
-   [Fuzzy batch-sökning](/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkter och låta dem bearbetas under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.

Se till att gå igenom [metod tipsen för Sök efter](./how-to-use-best-practices-for-search.md) dokumentation.

## <a name="get-traffic-incidents"></a>Hämta trafik incidenter

Azure Maps innehåller flera API: er för att hämta trafik data. Det finns två typer av trafik data tillgängliga.

* **Flödes data** – innehåller mått för trafik flödet i vägarnas avdelningar. Detta används ofta för att färgkoda vägar. Dessa data uppdateras var 2: e minut.
* **Incident data** – tillhandahåller data om konstruktion, väg stängning, olyckor och andra incidenter som kan påverka trafiken. Dessa data uppdateras varje minut.

Bing Maps innehåller trafik flödes-och incident data i sina interaktiva kart kontroller och gör också incident data tillgängliga som en tjänst.

Trafik data integreras också i Azure Maps interaktiva kart kontroller. Azure Maps tillhandahåller även följande API: er för trafik tjänster

-   [Trafik flödes segment](/rest/api/maps/traffic/gettrafficflowsegment): ger information om hastigheterna och res tiderna för det väg fragment som ligger närmast de koordinaterna.
-   [Förbindelse paneler i trafikflödet](/rest/api/maps/traffic/gettrafficflowtile): innehåller raster och vektor paneler som innehåller trafikflödes data. Dessa kan användas med Azure Maps kontroller eller i kart kontroller från tredje part, till exempel broschyr. Vektor panelerna kan också användas för avancerad data analys.
-   [Information om trafik incidenter](/rest/api/maps/traffic/gettrafficincidentdetail): innehåller information om trafik incidenter i en avgränsnings ruta, zoomnings nivå och trafik modell.
-   [Panel för trafik incidenter](/rest/api/maps/traffic/gettrafficincidenttile): innehåller raster och vektor paneler som innehåller trafik incident data.
-   [Visnings område för trafik incident](/rest/api/maps/traffic/gettrafficincidentviewport): hämtar den juridiska och tekniska informationen för visnings området som beskrivs i begäran, till exempel ID för trafik modell.

I följande tabell beskrivs API-parametrarna för Bing Maps-trafik med information om jämförbar trafik incidenter i Azure Maps.

| API-parameter för Bing Maps  | Jämförbar Azure Maps API-parameter   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` och `boundingZoom`      |
| `includeLocationCodes`   | Saknas                                   |
| `severity` (`s`)         | Ej tillämpligt – alla returnerade data               |
| `type` (`t`)             | Ej tillämpligt – alla returnerade data               |
| `key`                    | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)          | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) . |
| `userRegion` (`ur`)      | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) . |

## <a name="get-a-time-zone"></a>Hämta en tidszon

Azure Maps tillhandahåller ett API för att hämta tids zonen som en koordinat är i. Azure Maps tidszons-API: et är jämförbar med tids zons-API: et i Bing Maps.

-   [Tidszon per koordinat](/rest/api/maps/timezone/gettimezonebycoordinates): Ange en koordinat och hämta information om tids zonen som den tillhör.

Följande tabell innehåller en kors referens till API-parametrarna Bing Maps med de jämförbara API-parametrarna i Azure Maps.

| API-parameter för Bing Maps | Jämförbar Azure Maps API-parameter          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/A-platser måste vara omkodade först.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | Ej tillämpligt – inkludera alltid som svar av Azure Maps. |
| `key`                   | `subscription-key` – Se även [autentiseringen med Azure Maps](./azure-maps-authentication.md) -dokumentationen. |
| `culture` (`c`)         | `language` – Se dokumentation om [språk som stöds](./supported-languages.md) .  |
| `userRegion` (`ur`)     | `view` – Se dokumentation om [vilka vyer som stöds](./supported-languages.md#azure-maps-supported-views) .  |

Förutom denna Azure Mapss plattform innehåller också ett antal ytterligare tids zons-API: er som hjälper till med konvertering med tids zons namn och-ID: n.

-   [Tidszon med ID](/rest/api/maps/timezone/gettimezonebyid): returnerar aktuell, historisk och framtida tids zons information för angivet ID för IANA-tidszon.
-   [Tids zons uppräknings-IANA](/rest/api/maps/timezone/gettimezoneenumiana): returnerar en fullständig lista över IANA-tidszonens ID. Uppdateringar av tjänsten IANA visas i systemet inom en dag. 
-   [Enum för tidszon Windows](/rest/api/maps/timezone/gettimezoneenumwindows): returnerar en fullständig lista över Windows tids zons-ID: n.
-   [Version för tids zonens IANA](/rest/api/maps/timezone/gettimezoneianaversion): returnerar det aktuella IANA-versions numret som används av Azure Maps. 
-   [Time Zone Windows till IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): returnerar ett motsvarande IANA-ID, med ett giltigt Windows-tidszons-ID. Flera IANA-ID: n kan returneras för ett enda Windows-ID.

## <a name="spatial-data-services-sds"></a>Avstånds Data Services (SDS)

De spatiala data tjänsterna i Bing Maps innehåller tre viktiga funktioner:

* Batchbokföring av batch – bearbeta en stor grupp av adressens landskod med en enskild begäran.
* Hämta administrativa avgränsnings data – Använd en koordinat och hämta en överlappande gränser för en angiven entitetstyp.
* Vara värd för och fråga efter rums affärs data – Ladda upp en enkel 2D-tabell med data och få åtkomst till den med några enkla avstånds frågor.

### <a name="batch-geocode-data"></a>Information om batch-landskod

Batchbearbetning av batch är en process som tar ett stort antal adresser eller platser, skickar dem i en enda begäran till en tjänst och har alla som är hårdkodade parallella och resultaten som returneras i ett enda svar.

Bing Maps gör det möjligt för upp till 200 000 adresser att skickas i en enda batch-webbdelsbegäran. Den här begäran hamnar i en kö och bearbetar vanligt vis under en viss tids period, var som helst från några minuter till några timmar, beroende på storleken på data uppsättningen och belastningen på tjänsten. Varje adress i begäran genererade en transaktion.

Azure Maps har en batch-tjänst för att skapa en grupp, men det tillåter att upp till 10 000 adresser skickas i en enskild begäran och bearbetas över några sekunder till några minuter beroende på storleken på data uppsättningen och belastningen på tjänsten. Varje adress i begäran genererade en transaktion. I Azure Maps är batch-tjänsten för att användas endast som S1-nivå.

Ett annat alternativ för att ange ett stort antal adresser med Azure Maps är att göra parallella förfrågningar till standard Sök-API: erna. De här tjänsterna accepterar bara en enskild adress per begäran, men kan användas med S0-nivån som även ger kostnads fria användnings gränser. S0-nivån tillåter upp till 50 begär Anden per sekund till Azure Maps plattform från ett enda konto. Så om du bearbetar begränsa dessa så att de ligger inom den gränsen, är det möjligt att hårdkoda upp till 180 000-adressen per timme. S1-nivån har ingen dokumenterad gräns för antalet frågor per sekund som kan göras från ett konto, så att mycket mer data kan bearbetas snabbare när du använder den pris nivån, men med hjälp av batch-tjänsten för att använda funktionen för att använda tjänster för att minska den totala mängden data som överförs drastiskt minskar nätverks trafiken.

-   [Kod för fri Forms adress](/rest/api/maps/search/getsearchaddress): Ange en enskild adress sträng (som `"1 Microsoft way, Redmond, WA"` ) och bearbeta begäran omedelbart. Den här tjänsten rekommenderas om du snabbt behöver koda enskilda adresser.
-   [Kodning av strukturerad adress](/rest/api/maps/search/getsearchaddressstructured): Ange delar av en enskild adress, till exempel gatu namn, stad, land och post nummer och bearbeta begäran omedelbart. Den här tjänsten rekommenderas om du snabbt behöver koda enskilda adresser och data redan har tolkats i sina enskilda adress delar.
-   [Kodning av batch-adresser](/rest/api/maps/search/postsearchaddressbatchpreview): skapa en begäran som innehåller upp till 10 000 adresser och behandla dem under en viss tids period. Alla adresser kommer att kodas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned. Den här tjänsten rekommenderas för att kunna koda stora data mängder.
-   [Fuzzy-sökning](/rest/api/maps/search/getsearchfuzzy): det här API: et kombinerar adressens kod för att söka efter orienterings punkter. Det här API: et tar i en sträng med fri form som kan vara en adress, plats, landmärke, orienterings punkt eller kategori av intressant kategori och bearbeta begäran omedelbart. Detta API rekommenderas för program där användare kan söka efter adresser eller intressanta punkter från samma text ruta.
-   [Fuzzy batch-sökning](/rest/api/maps/search/postsearchfuzzybatchpreview): skapa en begäran som innehåller upp till 10 000 adresser, platser, landmärken eller anslags punkter och låta dem bearbetas under en viss tids period. Alla data kommer att bearbetas parallellt på servern och när du har slutfört den fullständiga resultat uppsättningen kan laddas ned.

### <a name="get-administrative-boundary-data"></a>Hämta information om administrations gränser

I Bing Maps görs administrativa gränser för länder, stater, regioner, städer och post nummer tillgängliga via API: et för data. Detta API tar antingen i en koordinat eller fråga till en landskod. Om en fråga skickas i kodas den och koordinaterna från det första resultatet används. Detta API tar koordinaterna och hämtar gränserna för den angivna entitetstypen som överlappar koordinaten. Observera att detta API inte nödvändigt vis returnerar gränserna för den fråga som skickades. Om en fråga för `"Seattle, WA"` skickas i, men värdet för enhets typen är inställt på land, returneras gränserna för USA.

Azure Maps ger också till gång till administrativa gränser (länder, stater, regioner, städer och post nummer). Om du vill hämta en gränser måste du fråga en av Sök-API: erna för den önskade gränser (d.v.s. `Seattle, WA` ). Om Sök resultatet har en associerad kant linje anges ett geometri-ID i resultat svaret. API: et för search-polygon kan sedan användas för att hämta de exakta gränserna för ett eller flera geometri-ID: n. Detta är lite annat än Bing Maps som Azure Maps returnerar gränserna för det som söktes efter, medan Bing Maps returnerar en kant linje för en angiven entitetstyp i en angiven koordinat. Dessutom är de avgränsnings data som returneras av Azure Maps i formatet interjson.

Till Sammanfattning:

1.  Skicka en fråga för den gränser som du vill ta emot till någon av följande Sök-API: er.
    -   [Kod för fri Forms adress](/rest/api/maps/search/getsearchaddress)
    -   [Kodning av strukturerad adress](/rest/api/maps/search/getsearchaddressstructured)
    -   [Kodning av batch-adress](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Fuzzy-sökning](/rest/api/maps/search/getsearchfuzzy)
    -   [Fuzzy batch-sökning](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Om önskade resultat har ett Geometry-ID, skickar du det till API: et för Sök- [polygon](/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Värd och fråga för rums affärs data

De spatiala data tjänsterna i Bing Maps ger en enkel lagrings lösning för data lagring som är värd för affärs data och exponerar den som en spatial REST-tjänst. Den här tjänsten innehåller fyra huvud frågor. Sök efter egenskap, Sök i närheten, Sök i markerings rutan och hitta med 1 mil på väg. Många företag som använder den här tjänsten har redan redan lagrat sina affärs data i en databas någonstans och har laddat upp en liten del av den till den här tjänsten för att driva program som Store-positionerare. Eftersom den nyckelbaserade autentiseringen ger grundläggande säkerhet, har den rekommenderats att den här tjänsten endast används med offentliga data.

De flesta affärs plats data startar i en databas. Därför rekommenderar vi att du använder befintliga Azure Storage-lösningar som Azure SQL eller Azure PostgreSQL (med PostGIS-plugin-programmet). Båda dessa lagrings lösningar stöder spatialdata och ger en omfattande uppsättning funktioner för spatial fråga. När dina data är i en lämplig lagrings lösning kan de integreras i ditt program genom att skapa en anpassad webb tjänst eller genom att använda ett ramverk som ASP.NET eller Entity Framework. Med den här metoden får du fler fråge funktioner och även mycket högre säkerhets alternativ.

Azure Cosmos DB tillhandahåller också en begränsad uppsättning spatiala funktioner som, beroende på ditt scenario, kan vara tillräckligt.

Här är några användbara resurser som är värd för och frågar om spatialdata i Azure.

-   [Översikt över data typer för Azure SQL-spatialdata](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL-spatialdata – fråga närmaste granne](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Översikt över Azure Cosmos DB geospatiala funktioner](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Klientbibliotek

Azure Maps tillhandahåller klient bibliotek för följande programmeringsspråk.

-   Java Script, typescript, Node.js – [dokumentation](./how-to-use-services-module.md) \| [NPM-paket](https://www.npmjs.com/package/azure-maps-rest)

Klient bibliotek med öppen källkod för andra programmeringsspråk.

* .Net standard 2,0 – [GitHub Project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-paket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Rensa resurser

Det gick inte att rensa några resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps REST-tjänsterna.

> [!div class="nextstepaction"]
> [Metod tips för att använda Sök tjänsten](how-to-use-best-practices-for-search.md)

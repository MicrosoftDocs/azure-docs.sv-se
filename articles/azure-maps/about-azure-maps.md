---
title: Översikt över Microsoft Azure Maps
description: Lär dig mer om tjänster och funktioner i Microsoft Azure Maps och hur du använder dem i dina program.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: b53af0fda8bb24bf03d0e02f61ce7d195ae69503
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680818"
---
# <a name="what-is-azure-maps"></a>Vad är Azure Maps?

Azure Maps är en samling geospatiala tjänster och SDK: er som använder nya mappnings data för att tillhandahålla geografisk kontext till webb-och mobil program. Azure Maps tillhandahåller:

* REST API: er för rendera vektor-och raster kartor i flera format och satellit-bilder.
* Creator Services (för hands version) för att skapa och återge kartor baserat på privata data från kartan.
* Sök efter tjänster för att hitta adresser, platser och intressanta platser i världen.
* Olika alternativ för routning; till exempel punkt-till-punkt, Multipoint, Multipoint-optimering, isochrone, elektriska fordon, nytto fordon, trafik påverkan och mat ris vägar.
* Vyn trafikflödes läge och incidenter för program som kräver information om trafik i real tid.
* Mobilitets tjänster (för hands version) för att begära offentlig överförings information, planera vägar genom att blanda olika rese lägen och i real tid.
* Tids zons-och geolokalisering (för hands version)-tjänster.
* Höjnings tjänster (för hands version) med digital höjnings modell
* Att serva och mappa data lagring med plats information som finns i Azure.
* Plats information via Geospatial analys.

Dessutom är Azure Maps Services tillgängliga via Web SDK och Android SDK. De här verktygen hjälper utvecklare att snabbt utveckla och skala lösningar som integrerar plats information i Azure-lösningar.

Du kan registrera dig för ett kostnads fritt [Azure Maps-konto](https://azure.microsoft.com/services/azure-maps/) och börja utveckla.

I följande video beskriver vi Azure Maps mer ingående:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Kartkontroller

### <a name="web-sdk"></a>Web SDK

Med Azure Maps Web SDK kan du anpassa interaktiva kartor med ditt eget innehåll och bilder. Du kan använda den här interaktiva kartan för både dina webb program eller mobila program. Kart kontrollen använder WebGL, vilket innebär att du kan återge stora data mängder med hög prestanda. Du kan utveckla med SDK: n med hjälp av Java Script eller TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Exempel karta över populations ändringar som skapats med hjälp av Azure Maps Web SDK":::

### <a name="android-sdk"></a>Android SDK

Använd Azure Maps Android SDK för att skapa appar för mobil mappning.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Kart exempel på en mobil enhet":::

## <a name="services-in-azure-maps"></a>Tjänster i Azure Maps

Azure Maps består av följande tjänster som kan tillhandahålla geografisk kontext till dina Azure-program.

### <a name="data-service-preview"></a>Data tjänst (för hands version)

Data är absolut nödvändigt för Maps. Använd data tjänsten för att ladda upp och lagra geospatiala data som ska användas med spatiala åtgärder eller bild kompositioner.  Att ta med kund information närmare Azure Maps tjänsten minskar svars tiden, ökar produktiviteten och skapar nya scenarier i dina program. Mer information om den här tjänsten finns i [dokumentationen för data service](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Geolokalisering (för hands version)

Använd tjänsten för geolokalisering för att förhandsgranska den hämtade lands-och region koden för två bokstäver för en IP-adress. Den här tjänsten kan hjälpa dig att förbättra användar upplevelsen genom att tillhandahålla anpassat program innehåll baserat på geografisk plats.

Mer information finns i dokumentationen om [geolokalisering](/rest/api/maps/geolocation).

### <a name="mobility-services-preview"></a>Mobilitets tjänster (för hands version) 

Azure Maps mobilitets tjänster förbättrar utvecklings tiden för program med funktioner för offentlig överföring, t. ex. transit Routning och sökning efter närliggande offentliga överförings stopp. Användare kan hämta detaljerad information om överförings stopp, rader och scheman. Mobilitets tjänsten gör det också möjligt för användare att hämta stopp-och rad-Geometries, aviseringar för stopp, rader och tjänst områden samt mottagna real tids-och tjänst aviseringar. Dessutom tillhandahåller mobilitets tjänsterna routningsfunktioner med multimodal. Multimodal rese planering införlivar, alternativ för att dirigera och välja offentlig överföring, allt i en resa. Användare kan också få åtkomst till detaljerade multimodal steg-för-steg-färd vägar.

Mer information om tjänsten finns i [dokumentationen för mobilitets tjänsten](/rest/api/maps/mobility).

### <a name="render-service"></a>Renderingstjänst

[Rendering service v2 (för hands version)](/rest/api/maps/renderv2) introducerar en ny version av [Get Map panel v2-API: et](/rest/api/maps/renderv2/getmaptilepreview). Med hjälp av API: et för kart panel v2 kan kunder begära Azure Maps väg paneler, väder paneler eller kart paneler som skapats med hjälp av Azure Maps Creator. Vi rekommenderar att du använder den nya API: et för Get Map panel v2.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Exempel på en karta från rendering service v2":::

Mer information finns i dokumentationen för [rendering service v2](/rest/api/maps/renderv2).

Mer information om åter givnings tjänsten v1 som är i GA (allmän tillgänglighet) finns i dokumentationen för [rendering service v1](/rest/api/maps/render).  

### <a name="route-service"></a>Routningstjänst

Route Services kan användas för att beräkna uppskattade ankomst tider (ETAs) för varje begärd väg. Väg-API: er kan vara faktorer, till exempel information om trafik i real tid och historiska trafikdata, till exempel vanliga väg hastigheter på den begärda dagen i veckan och tid på dagen. API: erna returnerar de kortaste eller snabbaste vägarna som är tillgängliga för flera mål i taget i följd eller i optimerad ordning, baserat på tid eller avstånd. Tjänsten gör det möjligt för utvecklare att beräkna vägvisningar i flera rese lägen, till exempel bil, Last bil, cykel eller promenad och elektriska fordon. Tjänsten anser också indata, till exempel sändnings tid, vikt begränsningar eller transport av farligt material.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Exempel på en karta från Route service":::

Route service har avancerade uppsättnings funktioner, till exempel:

* Batchbearbetning av flera väg begär Anden.
* Matriser med res tid och avstånd mellan en uppsättning ursprung och mål.
* Hitta vägar eller avstånd som användarna kan färdas utifrån tid-eller bränsle krav.

Mer information om routningsfunktioner finns i [dokumentationen om Route service](/rest/api/maps/route).

### <a name="search-service"></a>Söktjänst

Sök tjänsten hjälper utvecklare att söka efter adresser, platser, företags listor efter namn eller kategori och annan geografisk information. Dessutom kan tjänster [omvända koder](https://en.wikipedia.org/wiki/Reverse_geocoding) för hår koder och korsade gator utifrån latitud och longitud.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Exempel på en sökning på en karta":::

Sök tjänsten innehåller även avancerade funktioner som:

* Sök längs en väg.
* Sök i ett större område.
* Gruppera en grupp med Sök begär Anden.
* Sök efter ett elektriskt fordons uttags stationer och POI-data efter märkes namn.

Läs [igenom dokumentationen för Sök tjänsten](/rest/api/maps/search)om du vill ha mer information om Sök funktioner.

### <a name="spatial-service"></a>Rumslig tjänst

Den spatiala tjänsten analyserar snabbt plats information för att hjälpa till att informera kunder om pågående händelser i tid och utrymme. Den möjliggör analys i nära real tid och förutsägande modellering av händelser.

Tjänsten gör det möjligt för kunderna att förbättra sin plats information med ett bibliotek med vanliga geospatiala matematiska beräkningar. Vanliga beräkningar är närmast punkt, fantastisk cirkel avstånd och buffertar. Läs mer om tjänsten och de olika funktionerna i den [spatiala tjänst dokumentationen](/rest/api/maps/spatial).

### <a name="timezone-service"></a>Tidszonstjänst

Med tids zons tjänsten kan du fråga aktuell, historisk och framtida tids zons information. Du kan använda antingen latitud-och longitud-par eller ett [IANA-ID](https://www.iana.org/) som inmatade. Tids zons tjänsten tillåter också:

* Konvertera Microsoft Windows Time-Zone-ID till IANA-tidszoner.
* Hämtar en tids zons förskjutning till UTC.
* Hämtar aktuell tid i en vald tidszon.

Ett typiskt JSON-svar för en fråga till tids zons tjänsten ser ut som i följande exempel:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Mer information om den här tjänsten finns i [dokumentationen till tids zons tjänsten](/rest/api/maps/timezone).

### <a name="traffic-service"></a>Trafiktjänst

Traffic service är en uppsättning webb tjänster som utvecklare kan använda för webb program eller mobilappar som kräver trafik information. Tjänsten tillhandahåller två typer av data:

* Trafikflöde: real tids observerade hastigheter och res tider för alla nyckel vägar i nätverket.
* Trafik incidenter: en uppdaterad vy över trafiken sylt och incidenter runt vägtrafik nätverket.

![Exempel på en karta med trafik information](media/about-azure-maps/intro_traffic.png)

Mer information finns i dokumentationen för [Traffic service](/rest/api/maps/traffic).

### <a name="weather-services-preview"></a>Väder tjänster (för hands version) 

Väder tjänster erbjuder API: er som utvecklare kan använda för att hämta väder information för en viss plats. Informationen innehåller information som observations datum och tid, kort beskrivning av väder förhållanden, väder ikonen, utfällning av indikator flaggor, temperatur och information om vridnings hastigheten. Ytterligare information som RealFeel™ temperatur och UV-index returneras också.

Utvecklare kan använda [API: t get väder utmed väg](/rest/api/maps/weather/getweatheralongroutepreview) för att hämta väder information tillsammans med en viss väg. Tjänsten har också stöd för att skapa väder meddelanden för waypoints som påverkas av väder risker, till exempel översvämning eller tung regn.

Med [hjälp av API: et för kart panel v2](/rest/api/maps/renderv2/getmaptilepreview) kan du begära tidigare, aktuella och framtida radar-och satellit paneler.

![Exempel på kartor med real tids väder radar paneler](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Maps Creator service (för hands version) 

Maps Creator service är en uppsättning webb tjänster som utvecklare kan använda för att skapa program med kart funktioner baserade på insamlings data från kartan.

Maps Creator har tre kärn tjänster:

* [Data uppsättnings tjänst](/rest/api/maps/dataset). Använd data uppsättnings tjänsten för att skapa en data uppsättning från ett konverterat ritnings paket data. Information om krav för ritnings paket finns i krav för ritnings paket.

* [Konverterings tjänst](/rest/api/maps/dataset). Använd konverterings tjänsten för att konvertera en DWG-design fil till ritnings paket data för inomhus kartor.

* [TILESET-tjänst](/rest/api/maps/tileset). Använd TILESET-tjänsten för att skapa en vektorbaserad representation av en data uppsättning. Program kan använda en TILESET för att presentera en visuell panel baserad vy av data uppsättningen.

* [Funktions tillstånds tjänst](/rest/api/maps/featurestate). Använd funktions tillstånds tjänsten för att stödja dynamisk Map-formatering. Med dynamisk karta kan program avspegla real tids händelser på utrymmen som tillhandahålls av IoT-system.

* [WFS-tjänst](/rest/api/maps/featurestate). Använd WFS-tjänsten för att fråga dina indata från kartan. WFS-tjänsten följer [Open GEOSPATIAL CONSORTIUM API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) -standarder för att skicka frågor till en enda data uppsättning.

### <a name="elevation-service-preview"></a>Höjnings tjänst (för hands version)

Tjänsten Azure Maps höjning är en webb tjänst som utvecklare kan använda för att hämta höjnings data från var som helst på jordens yta.

Med upphöjnings tjänsten kan du hämta höjnings data i två format:

* **Geotiff raster format**. Använd [rendering v2 – Hämta API för kart panel](/rest/api/maps/renderv2) för att hämta höjnings data i panel format.

* **INTERjson-format**. Använd [aviserings-API: er](/rest/api/maps/elevation) för att begära exempel på höjnings data utmed sökvägar, inom en definierad avgränsnings ruta eller vid vissa koordinater. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Exempel på karta med höjnings data":::


## <a name="programming-model"></a>Programmeringsmodell

Azure Maps har skapats för mobilitet och kan hjälpa dig att utveckla plattforms oberoende program. Den använder en programmerings modell som är språk oberoende och har stöd för JSON-utdata via [REST-API: er](/rest/api/maps/).

Azure Maps erbjuder också en praktisk [JavaScript-mappning](/javascript/api/azure-maps-control) med en enkel programmerings modell. Utvecklingen är snabb och enkel för både webb-och mobil program.





## <a name="power-bi-visual"></a>Visuellt Power BI-objekt

Azure Maps visualiseringen för Power BI ger en omfattande uppsättning data visualiseringar för spatialdata ovanpå en karta. Det uppskattas att över 80% av affärs data har ett plats sammanhang. Azure Maps Visual erbjuder en lösning utan kod för att få insikter om hur den här plats kontexten relaterar till och påverkar dina affärs data.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI Station ära datorer med Azure Maps visuell visning av affärs data":::

Mer information finns i avsnittet komma igång med [Azure Maps Power BI visuell](power-bi-visual-getting-started.md) dokumentation.

## <a name="usage"></a>Användning

Du kommer åt Azure Maps Services genom att gå till [Azure Portal](https://portal.azure.com) och skapa ett Azure Maps-konto.

I Azure Maps används ett schema för nyckelbaserad autentisering. När du skapar ditt konto skapas två nycklar. Du kan använda någon av nycklarna för att autentisera Azure Maps tjänster.

Obs! Azure Maps delar kund adress/plats frågor ("frågor") med TomTom från tredje part för att mappa funktionaliteten. Frågorna är inte länkade till någon kund eller slutanvändare när de delas med TomTom och kan inte användas för att identifiera individer. Mobilitets-och väder tjänster, inklusive integrering med Moovit, och AccuWeather är för närvarande en för [hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft håller på att lägga till TomTom, Moovit och AccuWeather i listan över online tjänster.

## <a name="supported-regions"></a>Regioner som stöds

Azure Maps tjänster är för närvarande tillgängliga utom i följande länder/regioner:

* Kina
* Sydkorea

Kontrol lera att platsen för din aktuella IP-adress finns i ett land/region som stöds.

## <a name="next-steps"></a>Nästa steg

Prova en exempel app som demonstrerar Azure Maps:

[Snabb start: skapa en webbapp](quick-demo-map-app.md)

Håll dig uppdaterad om Azure Maps:

[Azure Maps blogg](https://azure.microsoft.com/blog/topics/azure-maps/)
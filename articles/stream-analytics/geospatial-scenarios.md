---
title: Polystaket och geospatial agg regering med Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics för polystaket och geospatial agg regering.
author: krishna0815
ms.author: krishmam
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 7f83fe3a8443ac1b9339a0ddb91ee95414eef437
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016311"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Polystaket och geospatiala agg regerings scenarier med Azure Stream Analytics

Med inbyggda geospatiala funktioner kan du använda Azure Stream Analytics för att skapa program för scenarier som hantering av flottan, delning av delning, anslutna bilar och till gångs spårning.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics stöder real tids beräkningar i real tid i molnet och på IoT Edge Runtime.

### <a name="geofencing-scenario"></a>Scenario för polystaket

Ett tillverknings företag måste spåra till gångar på sina byggnader. De förser varje enhet med en GPS och vill ta emot meddelanden om en enhet lämnar ett visst utrymme.

Referens data som används i det här exemplet har avgränsnings information för byggnader och de enheter som tillåts i varje byggnad. Kom ihåg att referens data kan vara antingen statiska eller långsamma att ändra. Statiska referens data används för det här scenariot. En data ström genererar kontinuerligt enhets-ID och dess aktuella position.

### <a name="define-geofences-in-reference-data"></a>Definiera avgränsningar i referens data

Ett geografiskt avgränsnings tecken kan definieras med hjälp av ett geografiskt JSON-objekt. För jobb med kompatibilitet version 1,2 och högre kan även avgränsningar definieras med välkänd text (well) som `NVARCHAR(MAX)` . WELL är en Open Geospatial Consortium (OGC) standard som används för att representera spatialdata i ett text format.

De inbyggda geospatiala funktionerna kan använda definierade gränser för att ta reda på om ett element ligger inom eller ut ur en viss kors avgränsnings polygon.

Följande tabell är ett exempel på referens data som kan lagras i Azure Blob Storage eller en Azure SQL-tabell. Varje plats representeras av en Geospatial polygon och varje enhet är associerad med ett tillåtet plats-ID.

|SiteID|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond, bygge 41"|"POLYGON ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|T|
|2|"Redmond, bygge 40"|"POLYGON ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|En|
|3|"Redmond, byggnad 22"|"POLYGON ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|C|

### <a name="generate-alerts-with-geofence"></a>Generera aviseringar med en avgränsning

Enheter kan generera sitt ID och sin plats varje minut genom en data ström som kallas `DeviceStreamInput` . Följande tabell är en Stream med indata.

|DeviceID|Delposition|
|--------|-----------|
|En|"PUNKT (-122.13292341559497 47.636318374032726)"|
|T|"PUNKT (-122.13338475554553 47.63743531308874)"|
|C|"PUNKT (-122.13354001095752 47.63627622505007)"|

Du kan skriva en fråga som ansluter enhets strömmen med referens data för avgränsning och genererar en avisering varje gång en enhet är utanför en tillåten byggnad.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Följande bild representerar de båda gränserna. Du kan se var enheterna är i överensstämmelse med strömmens data indata.

![Skapa gränser](./media/geospatial-scenarios/building-geofences.png)

Enheten "C" finns inuti Byggnads-ID 2, vilket inte tillåts enligt referens data. Den här enheten bör finnas i Byggnads-ID 3. Om du kör det här jobbet skapas en avisering för denna överträdelse.

### <a name="site-with-multiple-allowed-devices"></a>Webbplats med flera tillåtna enheter

Om en plats tillåter flera enheter kan en matris med enhets-ID: n definieras i `AllowedDeviceID` och en User-Defined funktion kan användas i `WHERE` -satsen för att kontrol lera om ström enhets-ID: t matchar alla enhets-ID: n i listan. Mer information finns i själv studie kursen om [JavaScript-skript](stream-analytics-javascript-user-defined-functions.md) för moln jobb och [C# UDF](stream-analytics-edge-csharp-udf.md) -självstudie för Edge-jobb.

## <a name="geospatial-aggregation"></a>Geospatial agg regering

Azure Stream Analytics stöder geospatiala geospatiala agg regeringar i real tid i molnet och på IoT Edge Runtime.

### <a name="geospatial-aggregation-scenario"></a>Geospatialt samlings scenario

Ett CAB-företag vill bygga ett real tids program för att vägleda sina CAB-drivrutiner som söker efter att gå till de områden i städerna som för närvarande har högre efter frågan.

Företaget lagrar logiska regioner i staden som referens data. Varje region definieras av ett RegionID, RegionName och ett geografiskt avgränsnings tecken.

### <a name="define-the-geofences"></a>Definiera de gränser

Följande tabell är ett exempel på referens data som kan lagras i Azure Blob Storage eller en Azure SQL-tabell. Varje region representeras av en Geospatial polygon, som används för att korrelera med de begär Anden som kommer från strömmande data.

Dessa polygoner är endast till för referens och representerar inte faktiska logiska eller fysiska avgränsningar för stad.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|Mindre|"POLYGON ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))"|
|2|"Chinatown"|"POLYGON ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Samla data över en tids period

Följande tabell innehåller strömmande data för "Overrides".

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|En|"PUNKT (-74.00726861389182 40.71610611981975)"|"PUNKT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|T|"PUNKT (-74.00249841021645 40.723827238895666)"|"PUNKT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|C|"PUNKT (-73.99680120565864 40.716439898624024)"|"PUNKT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|Styr|"PUNKT (-74.00741090068288 40.71615626755086)"|"PUNKT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Följande fråga ansluter enhets strömmen med referens data för avgränsning och beräknar antalet förfrågningar per region i ett tids period på 15 minuter varje minut.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Den här frågan utvärderar antalet begär Anden varje minut under de senaste 15 minuterna per region i staden. Den här informationen kan enkelt visas på Power BI instrument panel eller skickas till alla driv rutiner som SMS-textmeddelanden via integrering med tjänster som Azure Functions.

Bilden nedan visar utdata från frågan för att Power BI instrument panelen. 

![Resultat av utdata på Power BI instrument panel](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics geospatiala funktioner](stream-analytics-geospatial-functions.md)
* [GeoSpatiala funktioner (Azure Stream Analytics)](/stream-analytics-query/geospatial-functions)
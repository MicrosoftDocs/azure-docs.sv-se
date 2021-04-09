---
title: Uppspelning av inspelningar – Azure
description: Du kan använda video analys i real tid för IoT Edge för kontinuerlig videoinspelning, där du kan spela in video i molnet för veckor eller månader. Du kan också begränsa inspelningen till klipp som är av intresse, via Event-baserad inspelning. Den här artikeln handlar om hur du spelas upp inspelningar.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99490903"
---
# <a name="playback-of-recordings"></a>Uppspelning av inspelningar 

## <a name="pre-read"></a>För läsning  

* [Videouppspelning](video-playback-concept.md)
* [Kontinuerlig videoinspelning](continuous-video-recording-concept.md)
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)

## <a name="background"></a>Bakgrund  

Du kan använda video analys i real tid för IoT Edge för [kontinuerlig videoinspelning](continuous-video-recording-concept.md) (CVR), där du kan spela in video i molnet för veckor eller månader. Du kan också begränsa inspelningen till klipp som är av intresse, via [Event-baserad videoinspelning](event-based-video-recording-concept.md) (EVR). 

Ditt medie tjänst konto är länkat till ett Azure Storage konto och när du spelar in video i molnet skrivs innehållet till en [medie tjänst till gång](terminology.md#asset). Med Media Services kan du [strömma innehållet](terminology.md#streaming), antingen efter att inspelningen har slutförts eller medan inspelningen pågår. Media Services ger dig de funktioner som krävs för att leverera strömmar via HLS-eller MPEG-tank-protokoll för uppspelning av enheter (klienter). Mer information finns i artikeln [video uppspelning](video-playback-concept.md) . Du använder media service-API: er för att generera nödvändiga strömnings-URL: er – som används av klienter för att spela upp video & ljud. Information om hur du skapar strömmande URL för en till gång finns i [skapa en strömmande plats och skapa URL: er](../latest/create-streaming-locator-build-url.md). När strömnings-URL: en har skapats för en till gång kan du och lagra associationen för URL: en med video källan (det vill säga kamera) i innehålls hanterings systemet.

Till exempel skulle uppspelnings-URL: en se ut när den strömmas via HLS `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . För MPEG-streck skulle det se ut `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Detta returnerar en manifest fil, som bland annat beskriver den totala varaktigheten för den data ström som levereras och om den representerar förregistrerade innehåll eller det pågående Live-flödet.

### <a name="live-vs-vod"></a>Live vs. VoD  

Strömmande protokoll som HLS och MPEG-streck skrevs för att hantera scenarier som strömning av Live-videor, samt strömning av innehåll på begäran/inspelat innehåll, t. ex. TV-program och filmer. För Live-videor är HLS-och MPEG-streck-klienter utformade för att börja spelas från den senaste tiden. Med hjälp av filmer förväntas dock läsarna att kunna starta från början och hoppa runt om de väljer att. HLS-och MPEG-streck-manifesten har flaggor som visar om videon representerar en Live-ström eller om den är i förväg inspelat innehåll.
Det här konceptet gäller även för HLS-och MPEG-streck-strömmar från till gångar som innehåller video som registrerats med hjälp av real tids analys på IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Bläddring och selektiv uppspelning av inspelningar  

Tänk dig ett scenario där du har använt Live Video Analytics på IoT Edge för att spela in video endast från 8.00 till 10 dagar när en skola var öppen, under hela akademiska året. Eller så kanske du bara spelar in video från 7AM till 19:00 på nationella helgdagar. I något av dessa två fall, vid försök att bläddra och visa din videoinspelning, behöver du:

* Ett sätt att avgöra vilka datum/timmar video du har i en inspelning.
* Ett sätt att välja en del (t. ex. 9 till kl 11 på nya år) för den inspelningen till uppspelning.

Media Services ger dig ett fråge-API (availableMedia) som tar itu med det första problemet, och tids intervall filter (StartTime, slut tid) för att hantera det andra.

## <a name="query-api"></a>Fråge-API 

När du använder CVR kan uppspelnings enheter (klienter) inte begära ett manifest som täcker uppspelningen av hela inspelningen.  En inspelning på flera år skulle skapa en manifest fil som var för stor för uppspelning och den skulle bli svårhanterligt att parsa till användbara delar på klient sidan.  Klienten måste veta vilka datetime-intervall som har data i inspelningen så att de kan göra giltiga förfrågningar utan mycket gissnings arbete. Det här är det nya fråge-API: t som finns i – klienter kan nu använda detta API för Server sidan för att identifiera innehåll.

Om precision svärdet kan vara ett av: år, månad, dag eller fullständig (se nedan). 

|Precision|år|månad|day|fullständig|
|---|---|---|---|---|
|Fråga|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Svarsåtgärder|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Svar med fullständig åter givning. Om det inte fanns några luckor alls skulle starten vara StartTime och end skulle vara slut tid.|
|Villkor|&#x2022;StartTime <= slut tid<br/>&#x2022;båda ska vara i formatet åååå, annars returnerar fel.<br/>&#x2022;värden kan vara vilket som helst av flera års mellanrum.<br/>&#x2022;värden inkluderas.|&#x2022;StartTime <= slut tid<br/>&#x2022;båda ska vara i formatet ÅÅÅÅ-MM, annars returnerar fel.<br/>&#x2022;värden kan vara högst 12 månader isär.<br/>&#x2022;värden inkluderas.|&#x2022;StartTime <= slut tid<br/>&#x2022;båda ska vara i formatet ÅÅÅÅ-MM-DD, annars returnerar fel.<br/>&#x2022;värden kan vara högst 31 dagar isär.<br/>Värdena är inkluderade.|&#x2022;StartTime < slut tid<br/>&#x2022;värden kan vara högst 25 timmar isär.<br/>&#x2022;värden inkluderas.|

#### <a name="additional-request-format-considerations"></a>Ytterligare överväganden vid formatering av begäran

* Alla tider är i UTC
* Parametern precision Query sträng måste anges.  
* Fråge sträng parametrarna för StartTime och slut tid krävs för värdena för månad, dag och fullständig precision.  
* Fråge sträng parametrarna för StartTime och slut tid är valfria för värdet år precision (ingen, antingen eller båda stöds).  

    * Om StartTime utelämnas antas det vara det första året i registreringen.
    * Om slut tiden utelämnas antas det vara det sista året i inspelningen.
    * Till exempel, om inspelningen startade i 2011 och har fortsatt till 2020, gör du följande:

        * /availableMedia? precision = år är samma som/availableMedia? precision = år&StartTime = 2011&slut tid = 2020
        * /availableMedia? precision = Year&StartTime = 2015 är samma som/availableMedia? precision = Year&StartTime = 2015&slut tid = 2020
        * /availableMedia? precision = år&slut tid = 2018 är samma som/availableMedia? precision = år&StartTime = 2011&slut tid = 2018

Om inga medie data är tillgängliga för tidsintervallen returneras en tom lista.

Om till gången inte innehåller någon inspelning från ett medie diagram returneras ett HTTP 400-svar med ett fel meddelande som förklarar att den här funktionen endast är tillgänglig för innehåll som registrerats via ett medie diagram.

Om tids längden som anges av parametrarna är större än vad som tillåts av det maximala tidsintervallet för en viss frågetyp, returneras en HTTP 400 med ett fel meddelande som förklarar det högsta tillåtna tidsintervallet för den begärda typen av fråga.

Förutsatt att tidsintervallet är giltigt för de parametrar som anges returneras inga fel för frågor som ligger utanför tids perioden för data i inspelningen.  Det innebär att om inspelningen startade 7 timmar sedan men kunden ber om tillgängliga medier från {UtcNow – 24 timmar} till UtcNow, returnerar vi bara {UtcNow – 7} data timmar.

### <a name="response-format"></a>Svars format  

AvailableMedia-anropet returnerar en uppsättning tids värden.

Svaret är en JSON-brödtext med timeRanges-värden som en matris med ISO 8601 UTC-datum för året (i åååå-format), månad (i ÅÅÅÅ-MM-format) eller dag (ÅÅÅÅ-MM-DD) beroende på den precision som begärs.  Den fullständiga timeRanges innehåller både ett start-och slutvärde som är formaterat som en ISO 8601 UTC-datum tid (i ÅÅÅÅ-MM-DDThh: mm: SS. sssZ-format).

För availableMedia-frågan kommer API: et att logga ut från video tids linjen. Eventuella discontinuities i tids linjen visas som luckor i svaret.

#### <a name="response-example-for-availablemedia"></a>Svars exempel för availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Exempel 1: direkt inspelning utan luckor

Här är ett svar som visar alla media som är tillgängliga för 21 december 2019, där inspelningen var 100% slutförd. Svaret har bara ett start-/end-par.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exempel 2: direkt registrering med ett mellanrum på 2 sekunder

Anta att det av någon anledning inte gick att skicka en giltig video för ett intervall på 2 sekunder på en dag. Här är ett svar som visar alla tillgängliga medier för 21 december 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exempel 3: direkt registrering med 8 timmars mellanrum

Anta att kameran och/eller den lokala anläggningen har förlorat ström under en 8-timmarsperiod under dagen, från 4 AM UTC till 12.00 UTC och att det inte fanns någon säkerhets kopierings ström källa. Här är ett svar som visar alla tillgängliga medier för en sådan dag

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Exempel 4: direktsänd inspelning där ingen video spelas in över semester semestrar

Anta att du bara spelade in video när skolan var i sessionen och inspelningen stoppades från den 17 juni till och med 6 september. En fråga för tillgängliga månader skulle se ut så här:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Om du sedan tillfrågas om tillgängliga dagar i juni ser du:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Exempel 5: direktsänd inspelning där ingen video spelas in över helger eller helgdagar

Anta att du bara spelade in video under kontors tid. En fråga om tillgängliga dagar skulle se ut

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Tids intervalls filter

Som vi nämnt ovan hjälper dessa filter dig att välja delar av din inspelning (till exempel från 9 till kl 11 på nya år) för uppspelning. Vid strömning via HLS skulle strömnings-URL: en se ut `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . För att kunna välja en del av din inspelning lägger du till en StartTime-och en slut tid-parameter, till exempel: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Därför är tids intervalls filter URL-modifierare som används för att beskriva den del av inspelningens tids linje som ingår i strömnings manifestet:

* `starttime` är en DateTime-stämpel i ISO 8601 som beskriver den önskade start tiden för video tids linjen i det returnerade manifestet.
* `endtime` är en DateTime-stämpel i ISO 8601 som beskriver den önskade slut tiden för video tids linjen som returneras i manifestet.

Den maximala längden (i tid) för ett sådant manifest får inte överstiga 24 timmar.

Här följer begränsningarna för filtren.

|startTime| endTime |Resultat|
|---|---|---|
|Use |Use |Returnerar den senaste delen av videon i till gången, upp till en maximal längd på 4 timmar.<br/><br/>Om till gången inte har skrivits till (inga nya video data kommer in), returneras ett manifest på begäran (VoD). Annars returneras ett direkt manifest.|
|Finner|Use|    Returnera ett manifest med vilken video som är tillgänglig, som är nyare än StartTime, om manifestet skulle vara kortare än 24 timmar.<br/>Returnera ett fel om Manifestets längd skulle överskrida 24 timmar.<br/>Om till gången inte har skrivits till (inga nya video data kommer in), returneras ett manifest på begäran (VoD). Annars returneras ett direkt manifest.
|Use|Finner |Fel – om StartTime är närvarande är slut tiden obligatorisk.|
|Finner|Finner|Returnera ett VoD-manifest med vilken video som är tillgänglig mellan StartTime och slut tid.<br/>Intervallet (StartTime, slut tid) får inte överskrida 24 timmar.|

### <a name="response-examples"></a>Svars exempel  

#### <a name="example-1-live-recording-with-no-gaps"></a>Exempel 1: direkt inspelning utan luckor

Här är ett svar som visar alla media som är tillgängliga för 21 december 2019, där inspelningen var 100% slutförd. Svaret har bara ett start-/end-par.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

När inspelningen är kontinuerlig kan du begära HLS eller streck manifest för valfri del av tiden inom det start-/slut paret – så länge som intervallet är 24 timmar eller mindre. Ett exempel på en HLS-manifest förfrågan på 4 timmar för ovanstående skulle vara:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exempel 2: direkt registrering med ett mellanrum på 2 sekunder

Anta att det av någon anledning inte gick att skicka en giltig video för ett intervall på 2 sekunder på en dag. Här är ett svar som visar det tillgängliga mediet för 21 december 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Med en inspelning, till exempel ovan, kan du begära HLS-och bindestreck-manifest med eventuella StartTime-/slut tid-par, förutsatt att intervallet var under 24 timmar. Om dessa värden ligger i linje med följande värden: 01:01:08AM UTC, skulle det returnerade manifestet att signalera diskontinuiteten i medie tids linjen, enligt relevanta specifikationer för dessa protokoll.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exempel 3: direkt registrering med ett mellanrum på 8 timmar

Anta att kameran och/eller den lokala anläggningen har förlorat ström under en 8-timmarsperiod under dagen, från 4 AM UTC till 12.00 UTC och att det inte fanns någon säkerhets kopierings ström källa. Här är ett svar som visar alla tillgängliga medier för en sådan dag.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Med en sådan inspelning:

* Om du begär ett manifest där både StartTime/slut tid intervall filter fanns inuti de "tillgängliga" delar av tids linjen, nämligen från midnatt till 4AM eller 12.00 till midnatt, returnerar tjänsten ett manifest som täcker tiden från StartTime till slut tid, till exempel:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Om du begär ett manifest där StartTime och slut tid fanns inuti "hål" i mitten – från 8.00 till 10 UTC fungerar tjänsten på samma sätt som om ett till gångs filter skulle resultera i ett tomt resultat.

    [Detta är en begäran som får ett tomt svar] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Om du begär ett manifest där endast en av StartTime eller slut tid är inuti "hål", innehåller det returnerade manifestet endast en del av det tidsintervallet. Det skulle fästa värdet för StartTime eller slut tid på närmast giltiga gränser. Om du t. ex. har bett om en data ström på 3 timmar från 10 till 1PM skulle svaret innehålla 1 – timmes värt medium under 12 middag till 1PM

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Returnerat manifest börjar på 2019-12-21T12:00:00.000 Z, även om förfrågan begärde en start av 10. När du skapar ett video hanterings system med ett Player-plugin-program bör du vara noga med att signalera detta till visnings programmet. En identifierad visnings program vara förväxlad till varför uppspelnings tids linjen påbörjades klockan 12.00 och inte 10 enligt önskemål

## <a name="recording-and-playback-latencies"></a>Fördröjning av inspelning och uppspelning

När du använder real tids analys på IoT Edge att registrera till en till gång, anger du en segmentLength-egenskap som instruerar modulen att sammanställa en minimal video varaktighet (i sekunder) innan den registreras i molnet. Om segmentLength till exempel är inställt på 300, kommer modulen att ta upp 5 minuter video innan du laddar upp 1 5 minuter "segment". gå sedan in i ackumulerings läge under de närmaste 5 minuterna och ladda upp igen. Om du ökar segmentLength har du nytta av att sänka dina Azure Storage transaktionskostnader, eftersom antalet läsningar och skrivningar inte blir mer frekvent än en gång var segmentLength sekund.

Därför kommer strömmande av videon från Media Services att förskjutas med minst den tiden. 

En annan faktor som avgör uppspelnings fördröjningen (fördröjningen mellan tiden som en händelse inträffar framför kameran, till den tid som den kan visas på en uppspelnings enhet) är den [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) varaktigheten för grupp-till-bilder. Att [minska fördröjningen hos Live-strömmar med tre enkla tekniker](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) förklarar, längre GOP varaktighet, längre svars tid. Det är vanligt att ha IP-kameror som används i övervaknings-och säkerhets scenarier som kon figurer ATS för att använda GOPs som är längre än 30 sekunder. Detta har stor inverkan på den totala svars tiden.

## <a name="next-steps"></a>Nästa steg

[Självstudie för kontinuerlig video inspelning](continuous-video-recording-tutorial.md)

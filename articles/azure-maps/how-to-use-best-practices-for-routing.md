---
title: Metod tips för Azure Maps Route Service i Microsoft Azure Maps
description: Lär dig hur du dirigerar fordon med hjälp av Route Service från Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051659"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Metod tips för Azure Maps Route service

API: er för väg riktning och väg mat ris i Azure Maps [Route service](/rest/api/maps/route) kan användas för att beräkna uppskattade ankomst tider (ETAs) för varje begärd väg. Väg-API: er kan se faktorer som information om trafik i real tid och historiska trafikdata, till exempel vanliga väg hastigheter på den begärda dagen i veckan och tid på dagen. API: erna returnerar de kortaste eller snabbaste vägarna som är tillgängliga för flera mål i taget i följd eller i optimerad ordning, baserat på tid eller avstånd. Användare kan också begära särskilda vägar och information för avvisare, cyklister och kommersiella bilar som Last bilar. I den här artikeln ska vi dela de bästa metoderna för att anropa Azure Maps [Route service](/rest/api/maps/route)och du får lära dig att:

 * Välj mellan API: er för väg riktningar och mat ris Dirigerings-API: et
 * Begära historiska och förväntade restider baserat på trafikdata i realtid och historiska trafikdata
 * Begär flödes information, t. ex. tid och avstånd, för hela vägen och varje steg i vägen
 * Begär routning för ett kommersiellt fordon, som en Last bil
 * Begär trafik information längs en väg, t. ex. sylt och avgifts information
 * Begär en väg som består av ett eller flera stopp (waypoints)
 * Optimera en väg av ett eller flera stopp för att få den bästa ordningen för att besöka varje stopp (waypoint)
 * Optimera alternativa vägar med hjälp av stöd punkter. Du kan till exempel erbjuda alternativa vägar som passerar en station med elektrisk fordons debitering.
 * Använd [Route service](/rest/api/maps/route) med Azure Maps webb-SDK

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

Mer information om omfattningen av Route Service finns i [Operationsföljd täckning](routing-coverage.md).

I den här artikeln används [Postman-appen](https://www.postman.com/downloads/) för att bygga rest-anrop, men du kan välja vilken API utvecklings miljö som helst.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Välj mellan väg riktningar och mat ris routning

API: er för väg riktningar returnerar-instruktioner, inklusive res tiden och koordinaterna för en väg Sök väg. Med väg mat ris API: et kan du beräkna res tiden och avstånden för en uppsättning vägar som definieras av ursprungs-och mål platserna. För varje angivet ursprung beräknar mat ris-API: n kostnaden (res tid och avstånd) för routning från det ursprunget till varje angivet mål. Med alla dessa API: er kan du ange parametrar som den önskade avgångs tiden, införsel tiden och fordons typen, t. ex. bil eller Last bil. Alla använder data i real tid eller förutsägande trafik för att returnera de mest optimala vägarna.

Överväg att anropa väg riktnings-API: er om ditt scenario är att:

* Begär den kortaste eller snabbaste vägen mellan två eller flera kända platser för att få exakt införsel tid för dina leverans bilar.
* Begär detaljerad väg vägledning, inklusive väg geometri, för att visualisera vägar på kartan
* Med en lista över kund platser beräknar du kortast möjliga väg för att besöka varje kund plats och återgår till ursprunget. Det här scenariot kallas ofta för resor med säljman-problem. Du kan skicka upp till 150 waypoints (stoppas) i en begäran.
* Skicka batchar av frågor till batch-API för väg riktningar med bara ett enda API-anrop.

Överväg att anropa API för mat ris routning om ditt scenario är att:

* Beräkna res tiden eller avståndet mellan en uppsättning ursprung och mål. Du har till exempel 12 driv rutiner och du måste hitta den närmast tillgängliga driv rutinen för att hämta mat leveransen från restaurang tjänsten.
* Sortera potentiella vägar efter deras faktiska rese avstånd eller tid. Mat ris-API: et returnerar bara res tider och avstånd för varje kombination av ursprung och mål.
* Kluster data baserat på res tid eller avstånd. Företaget har till exempel 50 anställda och du hittar alla anställda som bor inom 20 minuters enhets tid från kontoret.

Här är en jämförelse för att visa vissa funktioner i väg riktningarna och mat ris-API: er:

| Azure Maps-API | Maximalt antal frågor i begäran | Undvik områden | Routning av Last bilar och elektriska fordon | Waypoints och Traveling Salesman-optimering | Stöd punkter |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Hämta väg riktningar | 1 | | ✔ | ✔ | |
| Riktning efter väg | 1 | ✔ | ✔ | ✔ | ✔ |
| Batch för post vägs riktningar | 700 | | ✔ | ✔ | |
| Publicera väg mat ris | 700 | | ✔ | | |

Om du vill veta mer om routningsfunktioner för elektriska bilar, se vår självstudie om hur du [dirigerar elektriska fordon med hjälp av Azure Notebooks med python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Begär historiska och real tids data

Som standard förutsätter väg tjänsten att rese läget är en bil och sändnings tiden är nu. Den returnerar routning baserat på trafik förhållanden i real tid om inte en begäran om flödes beräkning anger något annat. Fasta trafik begränsningar för tids beroende, t. ex. "left" är inte tillåtna mellan 4:00 PM och 6:00 PM "och kommer att beaktas av routningstjänsten. Väg stängningar, t. ex. roadworks, kommer att övervägas om du inte specifikt begär en väg som ignorerar den aktuella Live-trafiken. Om du vill ignorera den aktuella trafiken ställer `traffic` du in på `false` i din API-begäran.

Värdet för **travelTimeInSeconds** för flödes beräkning inkluderar fördröjningen på grund av trafik. Den genereras genom att använda aktuella och historiska res tids data när avgångs tiden är inställd på nu. Om din sändnings tid anges i framtiden, returnerar API: erna förväntade res tider baserat på historiska data.

Om du inkluderar parametern **computeTravelTimeFor = all** i din begäran, kommer sammanfattnings elementet i svaret att ha följande ytterligare fält, inklusive historiska trafik villkor:

| Element | Beskrivning|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Beräknad res tid beräknad som om det inte finns några fördröjningar i vägen på grund av trafik förhållanden, till exempel på grund av överbelastning |
| historicTrafficTravelTimeInSeconds | Beräknad res tid beräknad med tids beroende historisk trafik information |
| liveTrafficIncidentsTravelTimeInSeconds | Beräknad res tid beräknad med real tids hastighets data |

I nästa avsnitt demonstreras hur du anropar API: er med hjälp av de diskuterade parametrarna.

### <a name="sample-query"></a>Exempelfråga

I det första exemplet under sändnings tiden anges till framtiden vid tidpunkten för skrivning.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Svaret innehåller ett sammanfattnings element, som det som visas nedan. Eftersom sändnings tiden anges till framtiden är **trafficDelayInSeconds** -värdet noll. **TravelTimeInSeconds** -värdet beräknas med tids beroende historisk trafik data. I det här fallet är **travelTimeInSeconds** -värdet lika med värdet för **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Exempelfråga

I det andra exemplet nedan har vi en operationsföljd förfrågan i real tid, där avgångs tiden är nu. Den anges inte uttryckligen i URL: en eftersom det är standardvärdet.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Svaret innehåller en sammanfattning som visas nedan. På grund av överbelastningar är **trafficDelaysInSeconds** -värdet större än noll. Det är också större än **historicTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Begär Routning och information om ben

Som standard returnerar Route-tjänsten en matris med koordinater. Svaret innehåller koordinaterna som utgör sökvägen till en lista med namnet `points` . Route Response inkluderar även avståndet från början av vägen och den uppskattade tid som förflutit. Dessa värden kan användas för att beräkna den genomsnittliga hastigheten för hela vägen.

Följande bild visar- `points` elementet.

![Punkt element](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Expandera `point` elementet om du vill se en lista över koordinater för sökvägen:

![Element för utökade punkter](media/how-to-use-best-practices-for-routing/points-list-img.png)

API: er för väg riktningar stöder olika format för instruktioner som kan användas genom att ange parametern **instructionsType** . Använd **instructionsType = Codet** för att formatera instruktioner för enkel dator bearbetning. Använd **instructionsType = taggade** för att visa instruktioner som text för användaren. Dessutom kan instruktioner formateras som text där vissa element i anvisningarna är markerade och instruktionen visas med särskild formatering. Mer information finns i [listan över instruktions typer som stöds](/rest/api/maps/route/postroutedirections#routeinstructionstype).

När instruktioner begärs returnerar svaret ett nytt element med namnet `guidance` . `guidance`Elementet innehåller två delar av information: vägbeskrivningar och sammanfattande instruktioner.

![Typ av anvisningar](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions`-Elementet innehåller instruktioner för tur och retur för resan och `instructionGroups` har sammanfattande instruktioner. Varje instruktions Sammanfattning omfattar ett segment av resan som kan omfatta flera vägar. API: erna kan returnera information om avsnitt i en väg. till exempel koordineras intervallet för en trafiks sylt eller den aktuella trafik hastigheten.

![Aktivera instruktioner](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Sammanfattande instruktioner](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Begära en väg för ett kommersiellt fordon

Azure Maps routnings-API: er stöder kommersiell fordons routning, som omfattar routning av kommersiella truckar. API: erna anser angivna gränser. Till exempel, fordonets höjd och vikt och om fordonet bär farligt gods. Om ett fordon till exempel är brandfarligt, undvikr routningstjänsten vissa tunnlar som ligger nära bostads områden.

### <a name="sample-query"></a>Exempelfråga

Exempel förfrågningen nedan frågar en väg för en kommersiell Truck. Trucken har transport av farligt avfall av klass 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Route API returnerar vägvisningar som hanterar Last bils och det farliga avfallet. Du kan läsa flödes instruktionerna genom att expandera- `guidance` elementet.

![Truck med klass 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Exempelfråga

Om du ändrar US Hazmat-klassen från ovanstående fråga leder det till en annan väg för den här ändringen.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Svaret nedan är för en Last bil som bär ett farligt material i klass 9, vilket är mindre farligt än ett farligt material av klass 1. När du expanderar `guidance` elementet för att läsa anvisningarna ser du att vägvisningar inte är samma. Det finns mer flödes instruktioner för trucken som bär klass 1 farligt material.



![Truck med klass 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Begära trafik information längs en väg

Med Azure Maps väg riktnings-API: er kan utvecklare begära information för varje typ av avsnitt genom att inkludera `sectionType` parametern i begäran. Du kan till exempel begära hastighets information för varje segment för stopp av trafik. I [listan med värden för nyckeln sectionType](/rest/api/maps/route/getroutedirections#sectiontype) kan du läsa mer om de olika uppgifterna som du kan begära.

### <a name="sample-query"></a>Exempelfråga

Följande fråga anges `sectionType` till `traffic` . Den begär de avsnitt som innehåller trafik information från Seattle till San-Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Svaret innehåller de avsnitt som är lämpliga för trafik längs de koordinater som anges.

![Trafik avsnitt](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Det här alternativet kan användas för att färga avsnitten när du återger kartan, som i bilden nedan: 

![Färgade avsnitt som återges på kartan](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Beräkna och optimera rutt med flera stopp

Azure Maps tillhandahåller för närvarande två former av väg optimeringar:

* Optimeringar baserat på den begärda cirkulations typen, utan att ändra ordningen på waypoints. Du kan hitta de [flödes typer som stöds här](/rest/api/maps/route/postroutedirections#routetype)

* Traveling Salesman-optimering, som ändrar ordningen på waypoints för att få den bästa ordningen för att besöka varje stopp

För multi-Stop-routning kan upp till 150 waypoints anges i en enda Route-begäran. Start-och slut koordinaterna kan vara desamma, som skulle vara fallet med en tur och retur. Men du måste ange minst en ytterligare waypoint för att utföra väg beräkningen. Waypoints kan läggas till i frågan i-mellan koordinaterna för ursprung och mål.

Om du vill optimera den bästa ordningen för att besöka den aktuella waypoints måste du ange **computeBestOrder = True**. Det här scenariot kallas även för problem med att optimera säljman-optimering.

### <a name="sample-query"></a>Exempelfråga

Följande fråga begär sökvägen för sex waypoints med `computeBestOrder` parametern inställd på `false` . Det är också standardvärdet för `computeBestOrder` parametern.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Svaret beskriver sökvägen till 140 851 meter och det skulle ta 9 991 sekunder att överföra sökvägen.

![Icke-optimerat svar](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

Bilden nedan visar den sökväg som skapas från den här frågan. Den här sökvägen är en möjlig väg. Det är inte den optimala sökvägen baserat på tid eller avstånd.

![Icke-optimerad avbildning](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Den här Route waypoint-ordningen är: 0, 1, 2, 3, 4, 5 och 6.

### <a name="sample-query"></a>Exempelfråga

Följande fråga begär sökvägen för samma sex waypoints, som i exemplet ovan. Den här gången har `computeBestOrder` parametern angetts till `true` (Travel-Sales-optimeringen).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Svaret beskriver sökvägen till 91 814 meter och det skulle ta 7 797 sekunder att överföra sökvägen. Rese avståndet och res tiden är båda lägre eftersom API: et returnerade den optimerade vägen.

![Optimerat svar](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

Bilden nedan visar den sökväg som skapas från den här frågan.

![Optimerad bild](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

Den optimala vägen har följande waypoint: 0, 5, 1, 2, 4, 3 och 6.

>[!TIP]
> Den optimerade waypoint beställnings information från routningstjänsten innehåller en uppsättning index. Dessa utesluter ursprung och mål index. Du måste öka de här värdena med 1 för att kunna använda ursprunget. Lägg sedan till målet i slutet för att hämta hela den sorterade waypoint-listan.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Beräkna och prioritera alternativa vägar med hjälp av support punkter

Du kan ha situationer där du vill rekonstruera en väg för att beräkna noll eller fler alternativa vägar för en referens väg. Du kanske till exempel vill visa alternativa vägar till kunder som skickar in din butik. I så fall måste du prioritera en plats med hjälp av support punkter. Här följer stegen för att prioritera en plats:

1. Beräkna en väg som den är och hämta sökvägen från väg svaret
2. Använd väg Sök vägen för att hitta önskade platser längs eller nära väg Sök vägen. Du kan till exempel använda Azure Maps [orienterings punktens API](/rest/api/maps/search/getsearchpoi) eller fråga dina egna data i databasen.  
3. Ordna platserna baserat på avståndet från vägens början
4. Lägg till dessa platser som stöd punkter i en ny värdväg till API: et för att [publicera väg riktningar](/rest/api/maps/route/postroutedirections). Om du vill veta mer om de stödda punkterna kan du läsa [API-dokumentationen för post riktningar](/rest/api/maps/route/postroutedirections#supportingpoints). 

När du anropar [API: er för post vägs riktningar](/rest/api/maps/route/postroutedirections)kan du ange den minsta avvikelse tiden eller avstånds begränsningarna, tillsammans med stöd punkterna. Använd de här parametrarna om du vill erbjuda alternativa vägar, men du vill även begränsa res tiden. När dessa villkor används följer de alternativa vägarna referens vägen från ursprungs punkten för den givna tiden eller det givna avståndet. De andra vägarna avviker från referens vägen enligt de begränsningar som anges.

Bilden nedan är ett exempel på hur du kan återge alternativa vägar med angivna avvikelse gränser för tid och avstånd.

![Alternativa vägar](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Använda routningstjänsten i en webbapp

Azure Maps Web SDK tillhandahåller en [service-modul](/javascript/api/azure-maps-rest/). Den här modulen är ett hjälp bibliotek som gör det enkelt att använda Azure Maps REST-API: er i webb-eller Node.js program med hjälp av Java Script eller TypeScript. Service-modulen kan användas för att återge de returnerade vägarna på kartan. Modulen avgör automatiskt vilket API som ska användas med GET-och POST-förfrågningar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

> [!div class="nextstepaction"]
> [Tjänsten Route i Azure Maps](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Använda Service-modulen](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Visa väg på kartan](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM-paket](https://www.npmjs.com/package/azure-maps-rest  )

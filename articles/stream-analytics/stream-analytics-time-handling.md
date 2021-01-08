---
title: Förstå tids hantering i Azure Stream Analytics
description: Lär dig hur du väljer den bästa start tiden, hanterar sena och tidiga händelser och om tids hanterings mått i Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: f9dbdb3907b376df8de988730c6c48ed01bfccd0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019949"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Förstå tids hantering i Azure Stream Analytics

I den här artikeln får du lära dig hur du kan göra design val för att lösa praktiska tids hanterings problem i Azure Stream Analytics jobb. Design beslut för tids hantering är nära relaterade till händelse ordnings faktorer.

## <a name="background-time-concepts"></a>Koncept för bakgrunds tid

För att skapa en bättre bild av diskussionen, ska vi definiera vissa bakgrunds begrepp:

- **Händelse tid**: den tidpunkt då den ursprungliga händelsen inträffade. Till exempel när en rörlig bil på motorväg närmar sig en väg på väg.

- **Bearbetnings tid**: den tidpunkt då händelsen når bearbetnings systemet och observeras. Till exempel när en väg givare ser bilen och dator systemet tar en stund att bearbeta data.

- **Vattenstämpel**: en händelse tids markör som visar upp till vilka punkt händelser som har inträffat för strömnings processorn. Vattenstämplar gör att systemet visar klart förloppet för att mata in händelser. När data strömmar är av typen stoppa stoppas inkommande händelse data aldrig, så vattenstämplar anger förloppet till en viss punkt i data strömmen.

   Begreppet vattenstämpel är viktigt. Med vattenstämplar kan Stream Analytics fastställa när systemet kan producera fullständiga, korrekta och upprepnings bara resultat som inte behöver dras tillbaka. Bearbetningen kan göras på ett förutsägbart och repeterbart sätt. Om till exempel en omräkning måste göras för vissa fel hanterings villkor är vattenstämplar säkra start-och slut punkter.

Ytterligare resurser för det här ämnet finns i Tyler Akidaus blogg inlägg [strömma 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) och [strömmande 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Välj den bästa start tiden

Stream Analytics ger användare två valmöjligheter för att välja händelse tid: tid för införsel och program tid.

### <a name="arrival-time"></a>Införsel tid

Införsel tiden tilldelas till Indatakällan när händelsen når källan. Du kan komma åt ankomst tiden genom att använda egenskapen **EventEnqueuedUtcTime** för Event Hubs Indatatyp, egenskapen **IoTHub. EnqueuedTime** för IoT Hub indatamängd och egenskapen **BlobProperties. LastModified** för BLOB-Indatatyp.

Införsel tiden används som standard och används bäst för data Arkiv scenarier där temporal logik inte behövs.

### <a name="application-time-also-named-event-time"></a>Tillämpnings tid (även namngiven händelse tid)

Programmets tid tilldelas när händelsen genereras och är en del av händelse nytto lasten. Om du vill bearbeta händelser efter program tid använder du **timestamp by** -satsen i Select-frågan. Om **timestamp by** saknas bearbetas händelser efter införsel tid.

Det är viktigt att använda en tidstämpel i nytto lasten när Temporal Logic är involverad för att beakta fördröjningar i käll systemet eller i nätverket. Tiden som tilldelats en händelse är tillgänglig i [systemet. Tidsstämpel](/stream-analytics-query/system-timestamp-stream-analytics).

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hur lång tid i Azure Stream Analytics

När du använder program tid baseras tiden på inkommande händelser. Det är svårt för data ström bearbetnings systemet att veta om det inte finns några händelser eller om händelserna är fördröjda. Därför genererar Azure Stream Analytics heuristiska vattenstämplar på följande sätt för varje partition:

* När det finns inkommande händelser är vattenstämpeln den största händelse tiden Stream Analytics har visats hittills, vilket innebär att storleken på tolerans perioden är för lång.

* När det inte finns någon inkommande händelse är vattenstämpeln den aktuella uppskattade införsel tiden minus fönstret sent införsel tolerans. Den uppskattade införsel tiden är den tid som har förflutit från den senaste gången en indatamängds händelse visades och den inkommande händelsens ankomst tid.

   Införsel tiden kan bara uppskattas eftersom den faktiska tiden för införsel skapas i händelse koordinatorn för indata, t. ex. Event Hubs, eller på den Azure Stream Analytics virtuella datorn som bearbetar händelserna.

Designen har två andra andra syfte än att skapa vattenstämplar:

1. Systemet genererar resultat i tid med eller utan inkommande händelser.

   Du styr hur lång tid du vill se resultatet av utdata. I Azure Portal, på sidan **händelse ordning** i Stream Analytics jobb, kan du konfigurera inställningen för slut föringen av **händelser** . När du konfigurerar den inställningen bör du överväga att använda tids linjen med tolerans för händelser som inte är i följd i händelse strömmen.

   Fönstret sent införsel tolerans är nödvändigt för att fortsätta att skapa vattenstämplar, även om det inte finns några inkommande händelser. Ibland kan det finnas en period där inga inkommande händelser kommer, till exempel när en händelse data ström är sparse. Det här problemet är exacerbated genom att använda flera partitioner i händelse koordinatorn för ingångs händelser.

   Strömmande data behandlings system utan en period för sent införsel, kan bli lidande för fördröjda utdata när indata är glesa och flera partitioner används.

2. System beteendet måste vara repeterbart. Repeterbarhet är en viktig egenskap i ett system för strömning av data behandling.

   Vattenstämpeln härleds från ankomst tiden och program tiden. Båda är bestående av händelse koordinatorn och kan därför upprepas. När en införsel tid beräknas i frånvaro av händelser, Azure Stream Analytics journaler som den uppskattade tiden för repeterbarhet under repetitionen för haveri återställning.

När du väljer att använda **tid för införsel** som tidpunkt för händelsen, behöver du inte konfigurera frånvaro tolerans och sent införsel tolerans. Eftersom **införsel tiden** är garanterad att öka i händelse koordinatorn för inloggen, Azure Stream Analytics bara att ignorera konfigurationerna.

## <a name="late-arriving-events"></a>Sena inkommande händelser

Efter definition av fönstret sent införsel tolerans, för varje inkommande händelse, Azure Stream Analytics jämför **händelse tiden** med **införsel tiden**. Om händelse tiden ligger utanför tolerans fönstret kan du konfigurera systemet för att släppa händelsen eller justera händelsens tid så att den ligger inom toleransen.

När vattenstämplarna har genererats kan tjänsten potentiellt ta emot händelser med en händelse tid som är lägre än vattenstämpeln. Du kan konfigurera tjänsten att antingen **släppa** dessa händelser eller **Justera** händelsens tid till värdet för vatten märket.

Som en del av justeringen anges händelsens **system. timestamp** till det nya värdet, men fältet för **händelse tid** ändras inte. Den här justeringen är den enda situationen där en händelses **system. timestamp** kan skilja sig från värdet i fältet händelse tid och kan orsaka att oväntade resultat genereras.

## <a name="handle-time-variation-with-substreams"></a>Hantera tids variation med under strömmar

Den mekanism för generering av heuristisk vattenstämpel som beskrivs fungerar bra i de flesta fall där tiden oftast är synkroniserad mellan olika händelse avsändare. I real tid, särskilt i många IoT-scenarier, har systemet emellertid lite kontroll över klockan på händelse avsändarna. Händelse avsändare kan vara alla typer av enheter i fältet, t. ex. olika versioner av maskin vara och program vara.

I stället för att använda en vattenstämpel som är global till alla händelser i en indataparameter, Stream Analytics har en annan mekanism som kallas under **strömmar**. Du kan använda under strömmar i jobbet genom att skriva en jobb fråga som använder [**timestamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) -satsen och nyckelordet **över**. Om du vill ange under data strömmen anger du ett nyckel kolumn namn efter **över** -nyckelordet, t. ex. ett `deviceid` , så att systemet använder tids principer för den kolumnen. Varje under data ström får sin egen oberoende vattenstämpel. Den här mekanismen är användbar för att tillåta generering av utdata i rätt tid vid hantering av stora klockor eller nätverks fördröjningar mellan händelse avsändare.

Under strömmar är en unik lösning som tillhandahålls av Azure Stream Analytics och som inte erbjuds av andra data behandlings system för strömning.

När du använder under strömmar Stream Analytics använder fönstret sent införsel tolerans för inkommande händelser. Den sena införsel toleransen bestämmer den maximala mängd med vilken olika del strömmar kan skilja sig från varandra. Om till exempel enhet 1 är vid tidsstämpel 1 och enhet 2 är vid tidsstämpel 2, är den mest sena införsel toleransen tidsstämpel 2 minus tidsstämpel 1. Standardvärdet är 5 sekunder och är troligen för litet för enheter med Divergent-tidsstämpel. Vi rekommenderar att du börjar med 5 minuter och gör justeringar enligt mönster för enhets klock skevning.

## <a name="early-arriving-events"></a>Tidig inkommande händelser

Du kanske har lagt märke till ett annat koncept som kallas tidig ankomst fönstret som ser ut som motsatsen till fönstret för sent införsel tolerans. Det här fönstret är fast i 5 minuter och har ett annat syfte från fönstret sent införsel tolerans.

Eftersom Azure Stream Analytics garanterar fullständiga resultat kan du bara ange **jobbets start tid** som den första utgångs tiden för jobbet, inte indata-tiden. Jobbets start tid måste anges så att hela fönstret bearbetas, inte bara från mitten av fönstret.

Stream Analytics härleder start tiden från fråge specifikationen. Men eftersom indatamängden för inloggning endast indexeras efter införsel tid, måste systemet översätta start tid till ankomst tid. Systemet kan starta bearbetning av händelser från den punkten i händelse koordinatorn för indata. Med den tidigaste ingångs perioden kan översättningen vara enkel: start tid för händelsen minus 5 minuters tidigt ingångs period. Den här beräkningen innebär också att systemet släpper alla händelser som visas som en händelse tid 5 minuter tidigare än införsel tiden. [Måttet tidig ingångs händelser](stream-analytics-monitoring.md) ökar när händelserna släpps.

Det här konceptet används för att se till att bearbetningen går att upprepa oavsett var du börjar skriva ut från. Utan sådan mekanism skulle det inte vara möjligt att garantera repeterbarhet, eftersom många andra strömmande system anspråk.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Sido effekter av tids toleranser för händelse ordning

Stream Analytics-jobb har flera alternativ för **händelse ordning** . Två kan konfigureras i Azure Portal: inställningen för **slut för ställnings händelser** (out-of-order-tolerans) och de **händelser som inkommer** till den senaste inställningen (sent införsel tolerans). Den **tidigaste införsel** toleransen är fast och kan inte justeras. Dessa tids principer används av Stream Analytics för att tillhandahålla starka garantier. Dessa inställningar har dock vissa ibland oväntade konsekvenser:

1. Sändning av händelser som är för tidigt.

   Tidiga händelser ska inte anges som vanligt. Det är möjligt att tidiga händelser skickas till utdata om avsändarens klocka körs för snabbt. Alla händelser som har tagits fram ignoreras, så du kan inte se något av dem från utdata.

2. Skicka gamla händelser till Event Hubs som ska bearbetas av Azure Stream Analytics.

   Även om gamla händelser kan verka ofarliga på grund av tillämpningen av den sena införsel toleransen kan de gamla händelserna släppas. Om händelserna är för gamla ändras **system. timestamp** -värdet under händelse inmatningen. På grund av det här beteendet är Azure Stream Analytics mer lämpligt för scenarier med händelse bearbetning i nästan real tid, i stället för historiska händelse bearbetnings scenarier. Du kan ställa in **händelser som når sent** tid till det största möjliga värdet (20 dagar) för att undvika det här beteendet i vissa fall.

3. Utdata verkar vara fördröjda.

   Den första vattenstämpeln genereras vid den beräknade tiden: den **längsta händelse tiden** som systemet har observerat hittills, minus storleken på den föråldrade tolerans perioden. Som standard är standard toleransen inställd på noll (00 minuter och 00 sekunder). När du ställer in det på ett högre värde, som inte är noll, fördröjs strömmande jobbets första utdata av det tiden (eller senare) på grund av den första vattenstämpelns tid som beräknas.

4. Indata är sparse.

   När det inte finns någon inmatad partition på en specifik partition, beräknas vattenstämpelns tid som **införsel tid** minus fönstret sent införsel tolerans. Det innebär att om indata-händelser är ovanliga och sparse kan utdata fördröjas med den tiden. Standard **händelser som når sent** värde är 5 sekunder. Du bör förvänta dig att se en fördröjning när du skickar inmatade händelser en i taget, till exempel. Fördröjningen kan bli sämre om du ställer in **händelser som når sent** fönster till ett stort värde.

5. **System. timestamp** -värdet skiljer sig från tiden i fältet **händelse tid** .

   Som tidigare har beskrivits justerar systemet händelse tiden genom de inaktuella toleranserna eller de sena inställnings Fönstren. **System. timestamp** -värdet för händelsen justeras, men inte tids fältet för **händelse** . Detta kan användas för att identifiera vilka händelser som tidsstämplarna justerats. Om systemet ändrade tidsstämpeln på grund av en av toleranserna, är det vanligt vis samma.

## <a name="metrics-to-observe"></a>Mått att Observera

Du kan se ett antal tids toleranss effekter för händelse ordning genom [Stream Analytics jobb mått](stream-analytics-monitoring.md). Följande mått är relevanta:

|Mått  | Beskrivning  |
|---------|---------|
| **Händelser som inte är i ordning** | Anger antalet händelser som tagits emot i fel ordning, som antingen släpptes eller fått en justerad tidsstämpel. Det här måttet påverkas direkt av konfigurationen av inställningen för **out of order-händelser** på sidan **händelse ordning** på jobbet i Azure Portal. |
| **Sena ingångs händelser** | Anger antalet händelser som anländer sent från källan. Måttet inkluderar händelser som har släppts eller har haft tidsstämpeln ändrats. Det här måttet påverkas direkt av konfigurationen av de **händelser som anländer** till den senaste inställningen på sidan **händelse ordning** på jobbet i Azure Portal. |
| **Tidiga ingångs händelser** | Anger antalet händelser som inkommer tidigt från källan som antingen har släppts, eller om deras tidsstämpel har justerats om de ligger utanför 5 minuter tidigt. |
| **Fördröjning för vattenstämpel** | Anger fördröjningen för bearbetnings jobbet för strömmande data. Mer information finns i följande avsnitt.|

## <a name="watermark-delay-details"></a>Fördröjnings information för vattenstämpel

**Fördröjnings** måttet för vattenstämpeln beräknas som den tid då den bearbetade nodens klock klocka minus den största vatten märket har sett hittills. Mer information finns i [blogg inlägget för vattenstämpelns fördröjning](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Det kan finnas flera orsaker till att Metric-värdet är större än 0 under normal drift:

1. Potentiell bearbetnings fördröjning för den strömmande pipelinen. Normalt är den här fördröjningen nominell.

2. Tolerans fönstret är inställt, eftersom vattenstämpeln minskas med storleken på tolerans perioden.

3. I fönstret sent införsel introducerades fördröjningen, eftersom vattenstämpeln minskas med storleks fönstret tolerans.

4. Klock skevning för den bearbetnings nod som genererar måttet.

Det finns ett antal andra resurs begränsningar som kan orsaka strömnings pipelinen att saktas ned. Fördröjnings måttet för vattenstämpeln kan orsaka på grund av:

1. Det finns inte tillräckligt med bearbetnings resurser i Stream Analytics för att hantera mängden indata-händelser. Information om hur du skalar upp resurser finns i [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md).

2. Det finns inte tillräckligt med data flöde i insamlarna för indata-händelser, så de är begränsade. Möjliga lösningar finns i [skala upp Azure Event Hubs data flödes enheter automatiskt](../event-hubs/event-hubs-auto-inflate.md).

3. Utgående mottagare är inte etablerade med tillräckligt med kapacitet, så de är begränsade. De möjliga lösningarna varierar kraftigt beroende på vilken smak av output service som används.

## <a name="output-event-frequency"></a>Händelse frekvens för utdata

Azure Stream Analytics använder vattenstämpeln som enda utlösare för att skapa utdata-händelser. Eftersom vattenstämpeln härleds från indata kan den upprepas under haveri återställningen och även vid ombearbetning av användare. När du använder Windows- [mängder](stream-analytics-window-functions.md)ger tjänsten bara utdata i slutet av Windows. I vissa fall kanske användarna vill se del mängder som genererats från Windows. Partiella mängder stöds inte för närvarande i Azure Stream Analytics.

I andra strömnings lösningar kan utmatnings händelser materialiseras vid olika utlösnings punkter, beroende på externa förhållanden. Det är möjligt att vissa lösningar på utdata för en viss tids period genereras flera gånger. När indatavärdena är förfinade blir de sammansatta resultaten mer exakta. Händelser kan vara spekulationde vid första och revideras över tid. Till exempel, när en viss enhet är offline från nätverket, kan ett uppskattat värde användas av ett system. Senare kommer samma enhet att vara online till nätverket. Sedan kan den faktiska händelse informationen inkluderas i indataströmmen. Resultatet från bearbetningen av tids perioden ger mer exakta utdata.

## <a name="illustrated-example-of-watermarks"></a>Illustrera exempel på vattenstämplar

Följande bilder visar hur vattenstämplar fortskrider under olika förhållanden.

I den här tabellen visas exempel data som visas i diagrammet nedan. Observera att händelse tiden och tiden för införseln varierar, ibland matchning och ibland inte.

| Tid för händelsen | Införsel tid | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

I den här bilden används följande toleranser:

- Tidig införsel av Windows är 5 minuter
- Fönstret sent tillkommer är 5 minuter
- Omordnings fönstret är 2 minuter

1. Bild av vattenstämpelns förlopp genom följande händelser:

   ![Bild av Azure Stream Analytics vattenstämpel](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Viktiga processer som illustreras i föregående bild:

   1. Den första händelsen (device1) och den andra händelsen (device2) har justerade tider och bearbetas utan justeringar. Vattenstämpeln förloppet för varje händelse.

   2. När den tredje händelsen (device1) bearbetas kommer ankomst tiden (12:11) före händelse tiden (12:17). Händelsen anlände 6 minuter tidigt, så händelsen släpps på grund av toleransen på 5 minuter för tidig införsel.

      Vattenstämpeln fortskrider inte i det här fallet en tidig händelse.

   3. Den fjärde händelsen (device3) och femte händelsen (device1) har justerade tider och bearbetas utan justering. Vattenstämpeln förloppet för varje händelse.

   4. När den sjätte händelsen (device3) bearbetas, är ankomst tiden (12:17) och händelse tiden (12:12) under vatten märkes nivån. Händelse tiden justeras mot vatten märkes nivån (12:17).

   5. När den tolfte händelsen (device3) bearbetas, är ankomst tiden (12:27) 6 minuter före händelse tiden (12:21). Principen för sen införsel tillämpas. Händelse tiden justeras (12:22), som är ovanför vattenstämpeln (12:21), så att ingen ytterligare justering tillämpas.

2. Den andra illustrationen av vattenstämpeln förloppet utan en tidig införsel princip:

   ![Bild av Azure Stream Analytics ingen tidigare princip](media/stream-analytics-time-handling/watermark-graph-2.png)

   I det här exemplet tillämpas ingen tidig införsel princip. Avvikare-händelser som når tidig ökning av vattenstämpeln nämnvärt. Observera att den tredje händelsen (deviceId1 vid tid 12:11) inte släpps i det här scenariot och att vattenstämpeln upphöjs till 12:15. Den fjärde händelse tiden justeras framåt 7 minuter (12:08 till 12:15) som ett resultat.

3. I den sista bilden används under strömmar (över DeviceId). Flera vattenstämplar spåras, en per data ström. Det finns färre händelser när tiderna justeras som ett resultat.

   ![Bild av vattenstämpeln för Azure Stream Analytics under strömmar](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Nästa steg

- [Överväganden för Azure Stream Analytics händelse ordning]()
- [Stream Analytics jobb mått](stream-analytics-monitoring.md)
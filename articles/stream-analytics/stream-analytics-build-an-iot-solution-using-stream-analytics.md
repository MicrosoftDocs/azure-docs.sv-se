---
title: Bygg en IoT-lösning med hjälp av Azure Stream Analytics
description: Vägledning för att komma igång med Stream Analytics IoT-lösning i ett Tollbooth-scenario
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ddec53b18cd6f374a5665298b43b46122bcfa143
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016158"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Bygg en IoT-lösning med hjälp av Stream Analytics

## <a name="introduction"></a>Introduktion
I den här lösningen får du lära dig hur du använder Azure Stream Analytics för att få insikter i real tid från dina data. Utvecklare kan enkelt kombinera data strömmar, till exempel klicka-strömmar, loggar och enhets genererade händelser, med historiska poster eller referens data för att få affärs insikter. Som en fullständigt hanterad beräknings tjänst i real tid som är värd för Microsoft Azure, Azure Stream Analytics tillhandahålla inbyggd återhämtning, låg latens och skalbarhet för att komma igång på några minuter.

När du har slutfört den här lösningen kan du:

* Bekanta dig med Azure Stream Analytics-portalen.
* Konfigurera och distribuera ett strömmande jobb.
* Tydliggör verkliga problem och lös dem med hjälp av Stream Analytics frågespråk.
* Utveckla strömmande lösningar för dina kunder med hjälp av Stream Analytics med förtroende.
* Använd övervaknings-och loggnings upplevelsen för att felsöka problem.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande krav för att slutföra den här lösningen:
* En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenario Introduktion: "Hej, avgiftsbelagt!"
En avgifts Station är ett vanligt fenomen. Du stöter på dem på många Expressways, bryggor och tunnlar över hela världen. Varje avgifts Station har flera väg nummer. Vid manuella monter upphör du att betala avgift till en deltagare. Vid automatiserade monter, söker en sensor ovanpå varje Monte ett RFID-kort som är fäst på windshield på ditt fordon när du överför väg mätar hytten. Det är enkelt att visualisera fordonets passage genom dessa väg trafikstationer som en händelse ström där intressanta åtgärder kan utföras.

![Bild av bilar på väg vagnar](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Inkommande data
Den här lösningen fungerar med två data strömmar. Sensorer som är installerade i ingångs-och utgången av väg tull stationerna genererar den första strömmen. Den andra data strömmen är en statisk söknings data uppsättning som har registrerings data för fordonet.

### <a name="entry-data-stream"></a>Data ström för post
Data strömmen i posten innehåller information om bilar när de ansätts till ett avgifts stationer. Händelsen Avsluta data strömmas direkt till en Event Hub-kö från en webbapp som ingår i exempel appen.

| TollID | EntryTime | LicensePlate | Tillstånd | Modell | Modell | VehicleType | VehicleWeight | Kostnads | Tagga |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| TollID |Den väg-ID för väg som unikt identifierar en väg-Monte |
| EntryTime |Datum och tid för när fordonet anträtt till avgifts hytten i UTC |
| LicensePlate |Fordonets licens skylt |
| Tillstånd |Ett tillstånd i USA |
| Modell |Tillverkaren av bilen |
| Modell |Den mobila enhetens modell nummer |
| VehicleType |Antingen 1 för passagerar fordon eller 2 för kommersiella fordon |
| WeightType |Fordons vikt i ton. 0 för personbils fordon |
| Kostnads |Värdet för avgiftsbelagt i USD |
| Tagga |E-tag gen på den bil som automatiserar betalningen. tomt där betalningen utfördes manuellt |

### <a name="exit-data-stream"></a>Avsluta data ström
Slut data strömmen innehåller information om bilar som lämnar avgifts stationen. Händelsen Avsluta data strömmas direkt till en Event Hub-kö från en webbapp som ingår i exempel appen.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| TollID |Den väg-ID för väg som unikt identifierar en väg-Monte |
| ExitTime |Datum och tid då fordonet avslutades från väg in hytt i UTC |
| LicensePlate |Fordonets licens skylt |

### <a name="commercial-vehicle-registration-data"></a>Registrerings data för nytto fordon
Lösningen använder en statisk ögonblicks bild av en databas för handels registrering. Dessa data sparas som en JSON-fil i Azure Blob Storage, som ingår i exemplet.

| LicensePlate | RegistrationId | Upphörd |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| TBK 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| LicensePlate |Fordonets licens skylt |
| RegistrationId |Fordonets registrerings-ID |
| Upphörd |Fordonets registrerings status: 0 om registrering av fordonet är aktiv, 1 om registreringen har upphört att gälla |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurera miljön för Azure Stream Analytics
Du måste ha en Microsoft Azure-prenumeration för att kunna slutföra den här lösningen. Om du inte har ett Azure-konto kan du [begära en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/).

Se till att följa stegen i avsnittet "rensa ditt Azure-konto" i slutet av den här artikeln så att du kan använda din Azure-kredit på bästa sätt.

## <a name="deploy-the-sample"></a>Distribuera exemplet
Det finns flera resurser som enkelt kan distribueras i en resurs grupp tillsammans med några få klick. Lösnings definitionen finns i GitHub-lagringsplatsen på [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Distribuera TollApp-mallen i Azure Portal
1. Använd den här länken för att distribuera [TollApp Azure-mallen](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)för att distribuera TollApp-miljön till Azure.

2. Logga in på Azure Portal om du uppmanas att göra det.

3. Välj den prenumeration där de olika resurserna faktureras.

4. Ange en ny resurs grupp med ett unikt namn, till exempel `MyTollBooth` .

5. Välj en Azure-plats.

6. Ange ett **intervall** som ett antal sekunder. Det här värdet används i exempel-webbappen för hur ofta sändning av data ska skickas till Händelsehubben.

7. **Markera** om du vill godkänna de allmänna villkoren.

8. Välj **Fäst på instrument panelen** så att du enkelt kan hitta resurserna senare.

9. Välj **köp** för att distribuera exempel mal len.

10. Efter en liten stund visas ett meddelande för att bekräfta att **distributionen har slutförts**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Granska Azure Stream Analytics TollApp-resurser

1. Logga in på Azure Portal

2. Leta upp resurs gruppen som du har namngett i föregående avsnitt.

3. Kontrol lera att följande resurser finns med i resurs gruppen:
   - Ett Cosmos DB konto
   - Ett Azure Stream Analytics jobb
   - Ett Azure Storage konto
   - En Azure Event Hub
   - Två Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Granska exempel TollApp-jobbet
1. Från resurs gruppen i föregående avsnitt väljer du Stream Analytics strömnings jobb som börjar med namnet **tollapp** (namnet innehåller slumpmässiga tecken för unikhet).

2. På sidan **Översikt** för jobbet, Lägg märke till rutan **fråga** om du vill visa frågesyntaxen.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   För att parafrasera avsikten med frågan ska vi säga att du måste räkna antalet fordon som anger en väg på väg. Eftersom en väg avgifts hytt har en kontinuerlig ström av fordon som anges, är de ingångs händelser analoga till en ström som aldrig stoppar. Om du vill kvantifiera strömmen måste du definiera en "tids period" för att mäta över. Nu ska vi förfina frågan ytterligare till "hur många fordon kan du ange en avgifts Monte var tredje minut?" Detta kallas vanligt vis för antalet rullande.

   Som du kan se använder Azure Stream Analytics ett frågespråk som är som SQL och lägger till några tillägg för att ange tidsrelaterade aspekter av frågan.  Mer information finns i om [tids hantering](/stream-analytics-query/time-management-azure-stream-analytics) och [fönster](/stream-analytics-query/windowing-azure-stream-analytics) konstruktioner som används i frågan.

3. Granska indata för TollApp-exempel jobbet. Endast EntryStream-indatamängden används i den aktuella frågan.
   - **EntryStream** -indata är en Event Hub-anslutning som köar data som representerar varje gång en bil inträder i en Tollbooth på väg. En webbapp som är en del av exemplet skapar händelserna och data placeras i kö i den här händelsehubben. Observera att den här indatan frågas i from-satsen i direkt uppspelnings frågan.
   - **ExitStream** -indata är en Event Hub-anslutning som köar data som representerar varje gång en bil avslutar en Tollbooth på motorväg. Den här strömmande indata används i senare varianter av frågesyntaxen.
   - **Registrerings** indata är en Azure Blob Storage-anslutning, som pekar på en statisk registration.jspå fil, som används för sökningar efter behov. Denna referens data inmatning används i senare varianter av frågesyntaxen.

4. Granska utdata för TollApp-exempel jobbet.
   - **Cosmos DB** -utdata är en Cosmos-databas behållare som tar emot utgående Sink-händelser. Observera att dessa utdata används i INTO-satsen i direkt uppspelnings frågan.

## <a name="start-the-tollapp-streaming-job"></a>Starta TollApp streaming-jobbet
Följ de här stegen för att starta direkt uppspelnings jobbet:

1. På **översikts** sidan för jobbet väljer du **Start**.

2. I fönstret **starta jobb** väljer du **nu**.

3. Efter en liten stund visas **övervaknings** diagrammet på sidan **Översikt** för strömnings jobbet när jobbet körs. Grafen bör visa flera tusen inmatnings händelser och massor av utdata.

## <a name="review-the-cosmosdb-output-data"></a>Granska CosmosDB utgående data
1. Leta upp resurs gruppen som innehåller TollApp-resurserna.

2. Välj Azure Cosmos DB kontot med namn mönstret **tollapp \<random\> -Cosmos**.

3. Välj **datautforskaren** rubriken för att öppna sidan datautforskaren.

4. Expandera **tollAppDatabase**  >  **tollAppCollection**-  >  **dokument**.

5. I listan med ID: n visas flera dokument när utdata är tillgängliga.

6. Välj varje ID för att granska JSON-dokumentet. Lägg märke till varje tollid, windowend tid och antalet bilar från fönstret.

7. Efter ytterligare tre minuter är en annan uppsättning av fyra dokument tillgängliga, ett dokument per tollid.


## <a name="report-total-time-for-each-car"></a>Rapport total tid för varje bil
Den genomsnittliga tid som krävs för att en bil ska gå via avgifts tjänsten hjälper till att bedöma effektiviteten hos processen och kund upplevelsen.

Du hittar den totala tiden genom att ansluta till EntryTime-dataströmmen med ExitTime-dataströmmen. Gå med i de två indata strömmarna i kolumnerna samma matchande TollId och LicencePlate. Med operatorn **Anslut** måste du ange temporal Leeway som beskriver den acceptabla tids skillnaden mellan de anslutna händelserna. Använd funktionen **DateDiff** för att ange att händelser inte ska vara längre än 15 minuter från varandra. Använd även funktionen **DateDiff** för att avsluta och ange tider för att beräkna den faktiska tiden som en bil tillbringar på avgifts stationen. Observera skillnaden i användningen av **DateDiff** när den används i ett **Select** -uttryck i stället för ett **kopplings** villkor.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Så här uppdaterar du TollApp-strömnings jobbets frågesyntax:

1. På **översikts** sidan för jobbet väljer du **stoppa**.

2. Vänta en stund för meddelandet att jobbet har stoppats.

3. Under rubriken jobb TOPOLOGY väljer du **< > fråga**

4. Klistra in den justerade strömmande SQL-frågan.

5. Välj **Spara** för att spara frågan. Bekräfta **Ja** för att spara ändringarna.

6. På **översikts** sidan för jobbet väljer du **Start**.

7. I fönstret **starta jobb** väljer du **nu**.

### <a name="review-the-total-time-in-the-output"></a>Granska den totala tiden i utdata
Upprepa stegen i föregående avsnitt om du vill granska CosmosDB-utdata från direkt uppspelnings jobbet. Granska de senaste JSON-dokumenten.

I det här dokumentet visas till exempel ett exempel på en bil med en viss licens skylt, EntryTime och slut tid och det beräknade beräknade durationinminutes-fältet visar längden för avgifts monter i två minuter:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Rapportera fordon med utgånget registrering
Azure Stream Analytics kan använda statiska ögonblicks bilder av referens data för att ansluta till temporala data strömmar. Använd följande exempel fråga för att demonstrera den här funktionen. Registrerings indata är en statisk BLOB-JSON-fil som visar förfallo datum för licens taggar. Genom att ansluta till licens skylten jämförs referens data med varje fordon som passerar genom varje avgift.

Om ett kommersiellt fordon är registrerat hos avgifts företaget kan det passera genom väg den utan att ha stoppats för inspektion. Använd uppslags tabellen för registrering för att identifiera alla kommersiella fordon som har utgångna registreringar.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Upprepa stegen i föregående avsnitt om du vill uppdatera TollApp strömnings jobbets frågesyntax.

2. Upprepa stegen i föregående avsnitt om du vill granska CosmosDB-utdata från direkt uppspelnings jobbet.

Exempel på utdata:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Skala ut jobbet
Azure Stream Analytics är utformad för elastisk skalning så att den kan hantera stora mängder data. Den Azure Stream Analytics frågan kan använda en **partition by** -sats för att tala om för systemet att det här steget skalar ut. **PartitionID** är en särskild kolumn som systemet lägger till för att matcha partitions-ID: t för indatamängden (Event Hub).

För att skala ut frågan till partitioner, redigera frågesyntaxen till följande kod:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Skala upp strömnings jobbet till fler enheter för strömning:

1. **Stoppa** det aktuella jobbet.

2. Uppdatera frågesyntaxen på sidan **< > fråga** och spara ändringarna.

3. Under Konfigurera rubriken för strömnings jobbet väljer du **skala**.

4. Dra skjutreglaget för **strömnings enheter** från 1 till 6. Strömnings enheter definierar mängden beräknings kraft som jobbet kan ta emot. Välj **Spara**.

5. **Starta** direkt uppspelnings jobbet för att demonstrera den ytterligare skalningen. Azure Stream Analytics distribuerar arbetet över fler beräknings resurser och ger bättre data flöde, vilket partitionerar arbetet mellan resurserna med hjälp av kolumnen som anges i partitionen PARTITION BY.

## <a name="monitor-the-job"></a>Övervaka jobbet
**Övervaknings** arean innehåller statistik om det jobb som körs. Konfiguration för första gången krävs för att använda lagrings kontot i samma region (namn avgift som resten av det här dokumentet).

![Azure Stream Analytics jobb övervakning](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Du kan också komma åt **aktivitets loggar** från området **Inställningar** för instrument panelen.

## <a name="clean-up-the-tollapp-resources"></a>Rensa TollApp-resurserna
1. Stoppa Stream Analytics jobb i Azure Portal.

2. Leta upp resurs gruppen som innehåller åtta resurser som är relaterade till TollApp-mallen.

3. Välj **Ta bort resursgrupp**. Skriv namnet på resurs gruppen för att bekräfta borttagningen.

## <a name="conclusion"></a>Slutsats
Den här lösningen introducerade dig för Azure Stream Analytics tjänsten. Det visade hur du konfigurerar indata och utdata för Stream Analytics jobbet. Med hjälp av det här scenariot kan lösningen förklara vanliga typer av problem som uppstår i informations utrymmet i rörelse och hur de kan lösas med enkla SQL-liknande frågor i Azure Stream Analytics. Den lösning som beskrivs i SQL-tillägget för att arbeta med temporala data. Den visade hur du ansluter data strömmar, hur du kan utöka data strömmen med statiska referens data och hur du skalar upp en fråga för att uppnå högre data flöde.

Även om den här lösningen ger en bättre introduktion, är den inte fullständig på något sätt. Du hittar fler fråge mönster med hjälp av SAQL-språket i [fråge exempel för vanliga Stream Analytics användnings mönster](stream-analytics-stream-analytics-query-patterns.md).
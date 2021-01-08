---
title: Felsöka indata för Azure Stream Analytics
description: I den här artikeln beskrivs tekniker för att felsöka dina inaktuella anslutningar i Azure Stream Analytics-jobb.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019915"
---
# <a name="troubleshoot-input-connections"></a>Felsöka indataanslutningar

I den här artikeln beskrivs vanliga problem med Azure Stream Analytics ingångs anslutningar, fel sökning av problem med indatamängd och hur du korrigerar problemen. Många fel söknings steg kräver att resurs loggar aktive ras för ditt Stream Analytics-jobb. Om du inte har aktiverat resurs loggar kan du läsa [felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Inmatade händelser som inte tagits emot av jobb 

1.  Testa din anslutning för indata och utdata. Kontrol lera anslutningen till indata och utdata genom att använda knappen **Testa anslutning** för varje indata och utdata.

2.  Granska indata.

    1. Använd [**exempel data**](./stream-analytics-test-query.md) knappen för varje inmatning. Hämta exempel data för indata.
        
    1. Granska exempel data för att förstå schema-och [data typerna](/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Kontrol lera [Event Hub-mått](../event-hubs/event-hubs-metrics-azure-monitor.md) för att säkerställa att händelser skickas. Meddelande måtten måste vara större än noll om Event Hubs tar emot meddelanden.

3.  Se till att du har valt ett tidsintervall i förhands granskningen. Välj **Välj tidsintervall** och ange sedan en tids längd innan du testar frågan.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Felaktiga indatahändelser resulterar i deserialiseringsfel 

Problem med deserialiseringen orsakas när indata strömmen för Stream Analytics jobbet innehåller felaktigt utformade meddelanden. Ett felaktigt meddelande kan till exempel orsakas av en parentes, eller klammerparentes, i ett JSON-objekt eller felaktigt tidsstämpel-format i fältet Time. 
 
När ett Stream Analytics jobb tar emot ett felaktigt meddelande från inmatade meddelanden, ignoreras meddelandet och du får en varning. En varnings symbol visas på panelen **inmatningar** i ditt Stream Analytics jobb. Följande varnings symbol finns så länge jobbet körs:

![Panel för Azure Stream Analytics indata](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivera resurs loggar om du vill visa information om felet och meddelandet (nytto lasten) som orsakade felet. Det finns flera orsaker till varför deserialiserings-fel kan uppstå. Mer information om vissa deserialiserings fel finns i [indata-fel](data-errors.md#input-data-errors). Om resurs loggar inte är aktiverade är ett kort meddelande tillgängligt i Azure Portal.

![Varnings meddelande om indata](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Om meddelandets nytto Last är större än 32 KB eller är i binärformat, kör du CheckMalformedEvents.cs-koden som finns i [lagrings platsen för GitHub-exempel](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läser partitions-ID, förskjutning och skriver ut de data som finns i den förskjutningen. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Jobbet överskrider maximal mottagare av Event Hub

En bra idé att använda Event Hubs är att använda flera konsument grupper för jobb skalbarhet. Antalet läsare i Stream Analytics jobb för en viss ineffekt påverkar antalet läsare i en enda konsument grupp. Det exakta antalet mottagare baseras på intern implementerings information för logiken för skalbar topologi och exponeras inte externt. Antalet läsare kan ändras när ett jobb startas eller vid jobb uppgraderingar.

Följande fel meddelanden visas när antalet mottagare överskrider max gränsen. Fel meddelandet innehåller en lista över befintliga anslutningar som har gjorts till Event Hub under en konsument grupp. Taggen `AzureStreamAnalytics` indikerar att anslutningarna kommer från tjänsten Azure streaming.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> När antalet läsare ändras under en jobb uppgradering skrivs tillfälliga varningar till gransknings loggar. Stream Analytics-jobb återställs automatiskt från de här tillfälliga problemen.

### <a name="add-a-consumer-group-in-event-hubs"></a>Lägg till en konsument grupp i Event Hubs

Följ dessa steg om du vill lägga till en ny konsument grupp i Event Hubs-instansen:

1. Logga in på Azure-portalen.

2. Leta upp Händelsehubben.

3. Välj **Event Hubs** under rubriken **entiteter** .

4. Välj Event Hub efter namn.

5. På sidan **Event Hubs instans** , under rubriken **entiteter** , väljer du **konsument grupper**. En konsument grupp med namnet **$default** visas.

6. Välj **+ konsument grupp** för att lägga till en ny konsument grupp. 

   ![Lägg till en konsument grupp i Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. När du skapade InInformationen i Stream Analytics jobbet för att peka på Händelsehubben angav du konsument gruppen där. **$Default** används när inget anges. När du har skapat en ny konsument grupp redigerar du inmatade Event Hub i Stream Analytics-jobbet och anger namnet på den nya konsument gruppen.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Läsarna per partition överskrider Event Hubs gränsen

Om den strömmande frågesyntaxen refererar till samma resurs för indata-Händelsehubben flera gånger, kan jobb motorn använda flera läsare per fråga från samma konsument grupp. Om det finns för många referenser till samma konsument grupp kan jobbet överskrida gränsen på fem och genererade ett fel. I dessa fall kan du ytterligare dela upp genom att använda flera inmatningar i flera konsument grupper med hjälp av den lösning som beskrivs i följande avsnitt. 

Scenarier där antalet läsare per partition överskrider Event Hubs gränsen på fem omfattar följande:

* Flera SELECT-uttryck: om du använder flera SELECT-uttryck som refererar till **samma** Event Hub-inmatare, kan varje SELECT-instruktion orsaka att en ny mottagare skapas.

* UNION: när du använder en UNION är det möjligt att ha flera indata som refererar till **samma** händelsehubben och konsument grupp.

* SJÄLV koppling: när du använder en självbetjänings åtgärd är det möjligt att referera till **samma** händelsehubben flera gånger.

Följande metod tips kan hjälpa dig att minimera scenarier där antalet läsare per partition överskrider Event Hubs gränsen på fem.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dela upp frågan i flera steg med hjälp av en WITH-sats

WITH-satsen anger en tillfällig namngiven resultat uppsättning som kan refereras till av en FROM-sats i frågan. Du definierar satsen WITH i körnings omfånget för en enda SELECT-instruktion.

Till exempel, i stället för den här frågan:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Använder du den här frågan:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Se till att indata binds till olika konsument grupper

För frågor där tre eller flera indata är anslutna till samma Event Hubs konsument grupp skapar du separata konsument grupper. Detta kräver att ytterligare Stream Analytics indata skapas.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Skapa separata indata med olika konsument grupper

Du kan skapa separata indata med olika konsument grupper för samma Event Hub. Följande UNIONs fråga är ett exempel där *InputOne* och *InputTwo* refererar till samma Event Hub-källa. Alla frågor kan ha separata indata med olika konsument grupper. UNIONs frågan är bara ett exempel.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Läsarna per partition överskrider IoT Hub gränsen

Stream Analytics-jobb använder IoT Hub inbyggda [Event Hub-kompatibla slut punkter](../iot-hub/iot-hub-devguide-messages-read-builtin.md) för att ansluta och läsa händelser från IoT Hub. Om din läsning per partition överskrider gränserna för IoT Hub kan du använda [lösningarna för Event Hub](#readers-per-partition-exceeds-event-hubs-limit) för att lösa problemet. Du kan skapa en konsument grupp för den inbyggda slut punkten via IoT Hub portalens slut punkt session eller genom [IoT Hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)
---
title: Aktivitets mönster för händelse replikering – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller detaljerad information om hur du implementerar aktivitets mönster för en speciell händ Els replikering
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 370c0f2d5c5c591668aa2dadf0512760a4a9b2f5
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663703"
---
# <a name="event-replication-tasks-patterns"></a>Aktivitets mönster för händelse replikering

[Federations översikten](event-hubs-federation-overview.md) och [översikten över replikerare](event-hubs-federation-replicator-functions.md) förklarar grunderna för och de grundläggande elementen i replikeringen, och vi rekommenderar att du bekantar dig med dem innan du fortsätter med den här artikeln.

I den här artikeln beskriver vi implementerings vägledningen för flera av mönstren som är markerade i översikts avsnittet.

## <a name="replication"></a>Replikering

I det här mönstret kopieras händelser från en händelsehubben till nästa, eller från en Händelsehubben till ett annat mål som en Service Bus kö. Händelserna vidarebefordras utan att göra några ändringar i händelse nytto lasten.

Implementeringen av det här mönstret omfattas av [händelse replikeringen mellan Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) och [Händelseprovidern mellan Event Hubs och Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) exempel.

### <a name="streams-and-order-preservation"></a>Data strömmar och bevarad ordning

Replikering, antingen via Azure Functions eller Azure Stream Analytics, är inte avsett att garantera att exakta 1:1 kloner av en käll händelse hubb till en mål händelsehubben, men fokuserar på att bevara den relativa ordningen på händelser där programmet kräver det. Programmet kommunicerar detta genom att gruppera relaterade händelser med samma partitionsnyckel och [Event Hubs ordnar meddelanden med samma partitionsnyckel i turordning i samma partition](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> "Offset"-informationen är unik för varje Händelsehubben och förskjutningar för samma händelser varierar mellan Event Hub-instanser. Om du vill hitta en position i en kopierad händelse ström, använder du tidsbaserade förskjutningar och refererar till de [spridda tjänste tilldelade metadata](#service-assigned-metadata).
>
> Tidsbaserade förskjutningar startar mottagaren vid en viss tidpunkt:
> - *EventPosition. FromStart ()* – Läs alla data som sparats igen.
> - *EventPosition. FromEnd ()* – Läs alla nya data från anslutnings tiden.
> - *EventPosition. FromEnqueuedTime (datetime)* – alla data som börjar från ett visst datum och en specifik tidpunkt.
>
> I EventProcessor anger du positionen genom InitialOffsetProvider på EventProcessorOptions. Med de andra API: erna för mottagare skickas positionen genom konstruktorn. 


De förbyggda hjälp avsnitten för replikering som [tillhandahålls som exempel](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) som används i Azure Functions-baserade rikt linjerna säkerställer att händelse strömmar med samma partitionsnyckel som hämtats från en källmapp skickas till mål händelsehubben som en batch i den ursprungliga data strömmen och med samma partitionsnyckel.

Om antalet partitioner för käll-och mål Händelsehubben är identiskt, kommer alla strömmar i målet att mappas till samma partitioner som i källan.
Om antalet partitioner skiljer sig, vilket är viktigt i några av de ytterligare mönstren som beskrivs i följande, kommer mappningen att skilja sig åt, men strömmar hålls alltid tillsammans och i rätt ordning.

Den relativa ordningen på händelser som hör till olika strömmar eller oberoende händelser utan en partitionsnyckel i en målnod kan alltid skilja sig från käll-partitionen.

### <a name="service-assigned-metadata"></a>Tjänstetilldelade metadata

De tjänstetilldelade metadata för en händelse som hämtats från käll händelse hubben, ursprunglig kötid, sekvensnummer och förskjutning, ersätts av nya tjänstetilldelade värden i mål händelsehubben, men med [Hjälp funktioner](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), replikerings uppgifter som finns i våra exempel, bevaras de ursprungliga värdena i användar egenskaper: `repl-enqueue-time` (iso8601 sträng), `repl-sequence` , `repl-offset` .

Egenskaperna är av typen sträng och innehåller stringified-värdet för respektive ursprungliga egenskaper. Om händelsen vidarebefordras flera gånger, läggs de tjänstetilldelade metadata för den omedelbara källan till i redan befintliga egenskaper, med värden avgränsade med semikolon.

### <a name="failover"></a>Redundans

Om du använder replikering för haveri beredskap, för att skydda mot regionala tillgänglighets händelser i Event Hubss tjänsten, eller mot nätverks avbrott, kräver ett sådant fel scenario att du utför en redundansväxling från en Händelsehubben till nästa, och meddelar producenter och/eller konsumenter att använda den sekundära slut punkten.

För alla failover-scenarier förutsätts att de nödvändiga elementen i namn områdena är strukturellt identiska, vilket innebär att Event Hubs-och konsument grupper har samma namn och att signaturer för delad åtkomst och/eller rollbaserad åtkomst kontroll har kon figurer ATS på samma sätt. Du kan skapa (och uppdatera) ett sekundärt namn område genom att följa [rikt linjerna för att flytta namnrum](move-across-regions.md) och utelämna steget rensa.

För att göra det möjligt för producenter och konsumenter att byta, måste du göra informationen om vilken namnrymd som ska användas för sökning på en plats som är lätt att komma åt och uppdatera. Om tillverkare eller konsumenter stöter på frekventa eller beständiga fel bör de kontakta den platsen och ändra sin konfiguration. Det finns flera sätt att dela den konfigurationen, men vi pekar på två i följande: DNS-och fil resurser.

#### <a name="dns-based-failover-configuration"></a>Konfiguration av DNS-baserad redundans

En kandidat metod är att lagra informationen i DNS SRV-poster i en DNS som du styr och pekar på respektive Event Hub-slutpunkter. 

> [!IMPORTANT] 
> Observera att Event Hubs inte tillåter att dess slut punkter får ett direkt alias med CNAME-poster, vilket innebär att du kommer att använda DNS som en elastisk söknings funktion för slut punkts adresser och inte att direkt matcha IP-adressinformation.

Anta att du äger domänen `example.com` och, för ditt program, en zon `test.example.com` . För två alternativa Event Hubs kommer du nu att skapa två ytterligare kapslade zoner och en SRV-post i var och en.

SRV-posterna är, efter gemensam konvention, föregås av `_azure_eventhubs._amqp` och innehåller två slut punkts poster: en för AMQP på port 5671 och en för AMQP för WebSockets på port 443, som båda pekar på den Event Hubs slut punkten för namn området som motsvarar zonen.

| Zon                   | SRV-post                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

I programmets zon skapar du en CNAME-post som pekar på den underordnade zonen som motsvarar din primära Händelsehubben:

| CNAME-post                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Om du använder en DNS-klient som tillåter att fråga CNAME-och SRV-poster explicit (de inbyggda klienterna i Java och .NET tillåter endast enkel matchning av namn på IP-adresser) kan du sedan lösa den önskade slut punkten. Till exempel är lookup-funktionen [DnsClient.net](https://dnsclient.michaco.net/):

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Funktionen returnerar det mål värd namn som registrerats för port 5671 för zonen som för närvarande är aliased med CNAME som visas ovan.

Om du utför en redundansväxling måste du redigera CNAME-posten och peka den mot den alternativa zonen.

Fördelen med att använda DNS och specifikt [Azure DNS](../dns/dns-overview.md)är att Azure DNS information replikeras globalt och därmed är elastisk mot drifts avbrott i en region.

Den här proceduren liknar hur [Event Hubs geo-Dr](event-hubs-geo-dr.md) fungerar, men fullständigt under din egen kontroll och fungerar också med aktiva/aktiva scenarier.

#### <a name="file-share-based-failover-configuration"></a>Fil resurs baserad redundanskonfiguration

Det enklaste alternativet att använda DNS för att dela slut punkts information är att ange namnet på den primära slut punkten i en oformaterad textfil och betjäna filen från en infrastruktur som är stabil mot avbrott och fortfarande tillåter uppdateringar.

Om du redan kör en infrastruktur med hög tillgänglighet med global tillgänglighet och innehålls replikering, lägger du till en sådan fil där och publicerar om filen om det behövs en växel.

> [!CAUTION]
> Du bör bara publicera slut punkts namnet på det här sättet, inte en fullständig anslutnings sträng, inklusive hemligheter.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Ytterligare överväganden för att redundansväxla konsumenter

För Event Hub-konsumenter är det mer viktigt att tänka på vad du behöver göra i händelse processorn.

Om det finns en katastrof som kräver att du återskapar ett system, inklusive databaser, från säkerhets kopierings data och databaserna matas direkt eller via mellanliggande bearbetning från händelser som lagras i Händelsehubben, kommer du att återställa säkerhets kopian och sedan starta om händelser i systemet från den tidpunkt då databasen skulle skapas och inte från den tidpunkt då det ursprungliga systemet skulle ha förstörts.

Om ett haveri bara påverkar en sektor av ett system eller enbart en enskild Händelsehubben, som inte kan nås, vill du förmodligen fortsätta att bearbeta händelser från ungefär samma plats som bearbetningen avbröts.

För att kunna använda scenariot och använda händelse processorn för respektive Azure SDK [skapar du en ny kontroll punkts lagrings](event-processor-balance-partition-load.md#checkpointing) plats och anger en inledande partition, baserat på den _tidsstämpel_ som du vill återuppta bearbetningen från.

Om du fortfarande har åtkomst till kontroll punkts arkivet för den Händelsehubben som du växlar bort från, kan de [spridda metadata](#service-assigned-metadata) som beskrivs ovan hjälpa dig att hoppa över händelser som redan har hanterats och sedan återupptas precis som du senast slutade.

## <a name="merge"></a>Sammanfoga

Sammanslagnings mönstret innehåller en eller flera replikeringar som pekar på ett mål, och samtidigt kan de vanliga tillverkarna även skicka händelser till samma mål.

Variationer av dessa patters är:

- Två eller flera funktioner för replikering som samtidigt hämtar händelser från separata källor och skickar dem till samma mål.
- En mer replikerings funktion som hämtar händelser från en källa medan målet också används direkt av producenterna.
- Föregående mönster, men speglade mellan två eller flera Event Hubs, vilket resulterar i att dessa Event Hubs innehåller samma strömmar, oavsett var händelser skapas.

De två första mönster variationerna är enkla och skiljer sig inte från aktiviteter med enkel replikering.

Det sista scenariot kräver att redan replikerade händelser replikeras igen. Tekniken visas och beskrivs i [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/main/code/EventHubToEventHubMerge) -exemplet.

## <a name="editor"></a>Redigerare

Redigerings mönstret bygger på [replikeringsscopet](#replication) , men meddelanden ändras innan de vidarebefordras. 

Exempel på sådana ändringar är:

- **_Omkodning_** – om händelse innehållet (kallas även "brödtext" eller "nytto Last") kommer från källan som kodas med formatet _Apache Avro_ eller ett visst eget serialiserings format, men förväntar dig att systemet som äger målet är att det innehåll som ska vara _JSON_ -kodat, Avserialiserar en omkodning av replikeringen från _Apache Avro_ till ett InMemory-datadiagram och serialiserar sedan grafen till _JSON_ -formatet för den händelse som vidarebefordras. Omkodning omfattar även **innehålls komprimering** och avkomprimerings aktiviteter.
- **_Omvandlingen_** – händelser som innehåller strukturerade data kan kräva att du behöver forma om dessa data för enklare konsumtion av efterföljande konsumenter. Detta kan innebära arbete som att förenkla inkapslade strukturer, rensa främmande data element eller omforma nytto lasten så att de passar exakt för ett angivet schema.
- **_Batching_** – händelser kan tas emot i batchar (flera händelser i en enda överföring) från en källa, men måste vidarebefordras till ett mål eller vice versa. En aktivitet kan därför vidarebefordra flera händelser baserat på en enskild händelse överföring eller samla in en uppsättning händelser som sedan överförs tillsammans.
- **_Verifiering_** – händelse data från externa källor behöver ofta kontrol leras om de är kompatibla med en uppsättning regler innan de kan vidarebefordras. Reglerna kan uttryckas med hjälp av scheman eller kod. Händelser som inte finns i överensstämmelse kan tas bort, med problemet som anges i loggar eller vidarebefordras till ett särskilt mål mål för att hantera dem ytterligare.
- **_Anrikning_** – händelse data som kommer från vissa källor kan kräva berikning med ytterligare kontext för att det ska kunna användas i mål systemen. Detta kan innebära att söka efter referens data och bädda in data med händelsen, eller att lägga till information om källan som är känd för replikeringen, men som inte ingår i händelserna.
- **_Filtrering_** -vissa händelser som kommer från en källa kan behöva dras från målet baserat på en regel. Ett filter testar händelsen mot en regel och släpper händelsen om händelsen inte matchar regeln. Filtrering av duplicerade händelser genom att observera vissa kriterier och släppa efterföljande händelser med samma värden är en typ av filtrering.
- **_Kryptografi_** – en replikering kan behöva dekryptera innehåll som kommer från källan och/eller kryptera innehållet till ett mål och/eller måste kontrol lera integriteten för innehåll och metadata i förhållande till en signatur som befordras i händelsen, eller bifoga en sådan signatur.
- **_Attestering_** -en replikeringslänk kan bifoga metadata, som potentiellt skyddas av en digital signatur, till en händelse som bekräftar att händelsen har tagits emot via en speciell kanal eller vid en angiven tidpunkt.
- **_Länkning_** – en replikeringslänk kan tillämpa signaturer på strömmar av händelser så att data strömmens integritet är skyddat och händelser som saknas kan identifieras.

Transformerings-, batch-och anriknings mönstren är vanligt vis bäst implementerade med [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) -jobb. 

Alla mönster kan implementeras med hjälp av Azure Functions med hjälp av [Event Hubs-utlösaren](../azure-functions/functions-bindings-event-hubs-trigger.md) för att förvärva händelser och den [utgående bindningen för Event Hub](../azure-functions/functions-bindings-event-hubs-output.md) för att leverera dem.

## <a name="routing"></a>Routning

Mönstret för routning bygger på [replik](#replication) mönstret, men i stället för att ha en källa och ett mål har replikeringen flera mål, som illustreras här i C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Funktionen routning kommer att betrakta meddelandets metadata och/eller meddelandets nytto last och sedan välja en av de tillgängliga destinationer som ska skickas till.

I Azure Stream Analytics kan du uppnå samma med att definiera flera utdata och sedan köra en fråga per utdata.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Logg projektion

Mönstret för logg projektion fören klar händelse strömmen till en indexerad databas, där händelser blir poster i databasen. Vanligt vis läggs händelser till i samma samling eller tabell, och Händelsehubben för Händelsehubben blir part av den primära nyckel som söker efter posten unik.

Med logg projektion kan du skapa en Time-Series-historian av dina händelse data eller en komprimerad vy, där endast den senaste händelsen bevaras för varje partitionsnyckel. Mål databasens form är i slut ändan till dig och ditt programs behov. Det här mönstret kallas även för "händelse källa".

> [!TIP]
> Du kan enkelt skapa logg prognoser i [Azure SQL Database](../stream-analytics/sql-database-output.md) och [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) i Azure Stream Analytics och du bör föredra det alternativet.

Följande Azure-funktion projekt innehållet i en Event Hub komprimeras till en Azure CosmosDB-samling.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Event Replicators-program i Azure Functions][1]
- [Replikerar händelser mellan Event Hubs][2]
- [Replikera händelser till Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus

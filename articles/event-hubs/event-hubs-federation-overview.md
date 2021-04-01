---
title: Federation med flera platser och flera regioner – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över Federation med flera platser och flera regioner med Azure Event Hubs.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861465"
---
# <a name="multi-site-and-multi-region-federation"></a>Federation med flera platser och flera regioner

Många avancerade lösningar kräver samma händelse strömmar som görs tillgängliga för användning på flera platser, eller så kräver de händelse strömmar för att samlas in på flera platser och sedan samlas in på en bestämd plats för förbrukning. Det finns också ofta behov av att utöka eller minska händelse strömmar eller konvertering av händelse format, även för inom en och samma region och lösning.

Praktiskt taget innebär det att din lösning kommer att underhålla flera Event Hubs, ofta i olika regioner och Event Hubs namnrum, och sedan replikera händelser mellan dem. Du kan också utbyta händelser med källor och mål som [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)eller [Apache Kafka](https://kafka.apache.org). 

Genom att underhålla flera aktiva Event Hubs i olika regioner kan klienter också välja och växla mellan dem om deras innehåll slås samman, vilket gör det övergripande systemet mer flexibelt mot frågor om regional tillgänglighet.

I det här Federations kapitlet förklaras Federations mönster och hur du kan använda de här mönstren med hjälp av Server lös [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) eller [Azure Functions][1] körningar, med alternativet att använda en egen omvandling eller anriknings kod direkt i händelse flödets sökväg. 

## <a name="federation-patterns"></a>Federations mönster

Det finns många tänkbara orsaker till varför du kanske vill flytta händelser mellan olika Event Hubs eller andra källor och mål, och vi räknar med de viktigaste mönstren i det här avsnittet och länkar till mer detaljerad vägledning för respektive mönster. 

- [Återhämtning mot regionala tillgänglighets händelser](#resiliency-against-regional-availability-events)
- [Optimering av latens](#latency-optimization)
- [Validering, minskning och berikning](#validation-reduction-and-enrichment)
- [Integrering med Analytics-tjänster](#integration-with-analytics-services)
- [Konsolidering och normalisering av händelse strömmar](#consolidation-and-normalization-of-event-streams)
- [Dela och dirigera händelse strömmar](#splitting-and-routing-of-event-streams)
- [Logga projektioner](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Återhämtning mot regionala tillgänglighets händelser 

![Regional tillgänglighet](media/event-hubs-federation-overview/regional-availability.jpg)

Även om maximal tillgänglighet och pålitlighet är de viktigaste drifts prioriteterna för Event Hubs, finns det dock många sätt på vilka en producent eller konsument kan förhindras från att kommunicera med den tilldelade "primära" Händelsehubben på grund av nätverks-eller namn matchnings problem, eller om en Event Hub kanske faktiskt inte svarar eller returnerar fel. 

Sådana villkor är inte "katastrofal" så att du kan överge den regionala distributionen helt på samma sätt som du kan göra i en [katastrof återställnings](event-hubs-geo-dr.md) situation, men affärs scenariot för vissa program kanske redan påverkas av tillgänglighets händelser som senast är några minuter eller till och med sekunder. 

Det finns två grundläggande mönster för att lösa sådana scenarier:

- I [det här][4] mönstret replikeras innehållet i en primär händelsehubben till en sekundär händelsehubben, där den primära händelsehubben vanligt vis används av programmet för både framställning och användning av händelser och den sekundära fungerar som ett reserv alternativ om den primära händelsehubben blir otillgänglig. Eftersom replikering är enkelriktat, från den primära till den sekundära, kommer en växling av både producenter och konsumenter från en otillgänglig primär till den sekundära att den gamla primära inte längre får nya händelser och den kommer därför inte längre att vara aktuell.
  Ren replikering är därför bara lämplig för enkelriktade failover-scenarier. När redundansväxlingen har utförts överges den gamla primären och en ny sekundär Händelsehubben måste skapas i en annan mål region.
- [Sammanslagnings][5] mönstret utökar replikeringens mönster genom att utföra en kontinuerlig sammanslagning av innehållet i två eller flera Event Hubs. Varje händelse som ursprungligen tillverkas till en av de Event Hubs som ingår i schemat replikeras till den andra Event Hubs. När händelser replikeras är de kommenterade så att de sedan ignoreras av replikeringens replikeringsrelation. Resultatet av att använda sammanslagnings mönstret är två eller flera Event Hubs som ska innehålla samma uppsättning händelser på ett konsekvent sätt. 
  
I båda fallen är innehållet i Event Hubs inte identiska. Händelser från en producent och grupperas efter samma partitionsnyckel visas i samma relativa ordning som ursprungligen skickas, men den absoluta ordningen av händelser kan skilja sig. Detta gäller särskilt för scenarier där partitionernas antal käll-och mål Event Hubs skiljer sig, vilket är önskvärt för flera av de utökade mönster som beskrivs här. En [delnings-eller router](#splitting-and-routing-of-event-streams) kan erhålla en sektor av en mycket större händelsehubben med hundratals partitioner och tratt till en mindre händelsehubben med bara en fåtal partitioner, mer lämplig för hantering av del mängden med begränsade bearbetnings resurser. En [konsolidering](#consolidation-and-normalization-of-event-streams) kan sedan tratta data från flera mindre Event Hubs till en enda, större händelsehubben med fler partitioner för att hantera det konsoliderade data flödet och bearbetnings behoven.

Kriteriet för att hålla händelser tillsammans är partitionsnyckel och inte det ursprungliga partitions-ID: t. Ytterligare överväganden om relativ ordning och hur du utför en redundansväxling från en Händelsehubben till nästa utan att förlita dig på samma omfång för ström förskjutningar beskrivs i beskrivningen av ett [replik][4] mönster.


Veta 
- [Mönster för replikering][4]
- [Sammanslagnings mönster][5]

### <a name="latency-optimization"></a>Optimering av latens 

![Optimering av latens](media/event-hubs-federation-overview/latency-optimization.jpg)  

Händelse strömmar skrivs en gång av producenterna, men kan läsa ett valfritt antal gånger av händelse konsumenter. För scenarier där en händelse ström i en region delas av flera konsumenter och behöver nås upprepade gånger under analys bearbetning i en annan region, eller med över gångs krav som skulle strypa av samtidiga konsumenter, kan det vara bra att placera en kopia av händelse strömmen nära analys processorn för att minska tur och retur-fördröjningen. 

Bra exempel på när replikeringen bör prioriteras över användning av händelser via fjärr anslutning från flera regioner är särskilt de där regionerna är mycket stora, för att Europa och Australien är nästan Antipodes, geografiskt och att nätverks fördröjningen kan vara ett enkelt sätt att överskrida 250 MS för en rund resa.
Du kan inte påskynda hastigheten på ljuset, men du kan minska antalet svars tider med hög latens för att interagera med data.

Veta 
- [Mönster för replikering][4]

### <a name="validation-reduction-and-enrichment"></a>Validering, minskning och berikning

![Validering, minskning, berikning](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Händelse strömmar kan skickas till en Event Hub av klienter som är externa till din egen lösning. Sådana händelse strömmar kan krävas för att externt skickade händelser ska kontrol leras för kompatibilitet med ett angivet schema och för icke-kompatibla händelser som ska släppas. 

I scenarier där klienterna är mycket bandbredds begränsade som de är i flera "Sakernas Internet"-scenarier med mätning av bandbredd, eller om händelser ursprungligen skickas över icke-IP-nätverk med begränsade paket storlekar, kan händelserna måste vara omfattande med referens data för att lägga till ytterligare kontext som kan användas av underordnade händelse processorer.

I andra fall, särskilt när strömmar samlas, kan händelse data behöva minskas i komplexitet eller Sheer storlek genom att vissa detaljer utelämnas.

Alla dessa åtgärder kan uppstå som en del av replikering, konsolidering eller sammanslagning av flöden. 

Veta 
- [Redigerings mönster][6]

### <a name="integration-with-analytics-services"></a>Integrering med Analytics-tjänster

![Integrering med Analytics-tjänster](media/event-hubs-federation-overview/integration.jpg)

Flera av Azures molnbaserade analys tjänster som Azure Stream Analytics eller Azure Synapse fungerar bäst med strömmande eller förregistrerade data från Azure Event Hubs, och Azure Event Hubs möjliggör också integrering med flera analys paket med öppen källkod som Apache Samza, Apache Flink, Apache Spark och Apache Storm. 

Om din lösning huvudsakligen använder Service Bus eller Event Grid kan du göra dessa händelser lättillgängliga för sådana analys system och även för arkivering med Event Hubs fånga om du trattar dem i Händelsehubben. Event Grid kan göra det internt med sin [Event Hub-integrering](../event-grid/handler-event-hubs.md)för Service Bus du följer [rikt linjerna för Service Bus replikering](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub).

Azure Stream Analytics [integreras med Event Hubs direkt](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Veta 
- [Mönster för replikering][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Konsolidering och normalisering av händelse strömmar

![Konsolidering och normalisering av händelse strömmar](media/event-hubs-federation-overview/consolidation.jpg)

Globala lösningar består ofta av regionala anpassningar som är mycket oberoende, inklusive deras egna analys funktioner, men Supra-regionala och globala analys perspektiv kräver ett integrerat perspektiv och det är därför en central konsolidering av samma händelse strömmar som utvärderas i respektive regionala avtryck för det lokala perspektivet. 

Normalisering är en smak av konsoliderings scenariot, där två eller flera inkommande händelse strömmar har samma typ av händelser, men med olika strukturer eller olika kodningar, och de händelser som mest kommer att kodas eller omvandlas innan de kan förbrukas. 

Normalisering kan även innehålla kryptografiskt arbete, till exempel dekryptering av krypterade nytto laster från slut punkt till slut punkt och omkrypterar den med olika nycklar och algoritmer för den efterföljande konsument mål gruppen. 

Veta 
- [Sammanslagnings mönster][5]
- [Redigerings mönster][6]

### <a name="splitting-and-routing-of-event-streams"></a>Dela och dirigera händelse strömmar

![Dela och dirigera händelse strömmar](media/event-hubs-federation-overview/splitting.jpg)

Azure Event Hubs används ibland i scenarier med "publicera-prenumerera", där en inkommande torrent av insamlade händelser är mycket större än kapaciteten hos Azure Service Bus eller Azure Event Grid, som båda har interna publicerings prenumerations filtrering och distributions funktioner och som föredras för det här mönstret. 

En sann "publicera-prenumerera"-funktion lämnar den till prenumeranter för att välja de händelser som de vill ha, och delnings mönstret har producent mappnings händelserna till partitioner av en fördefinierad distributions modell och utsedda konsumenter, och tar sedan uteslutande emot från "deras" partition. Med Event Hub-buffringen av den övergripande trafiken kan innehållet i en viss partition som representerar en bråkdel av den ursprungliga data flödes volymen sedan replikeras till en kö för tillförlitlig, transaktionell och konkurrerande konsumtions förbrukning.

Många scenarier där Event Hubs främst används för att flytta händelser i ett program inom en region har vissa fall där Välj händelser, kanske bara från en enda partition, också måste göras tillgängliga någon annan stans. Det här scenariot liknar delnings scenariot, men kan använda en skalbar router som tar hänsyn till alla meddelanden som kommer i en Händelsehubben och körsbär – plockar bara några för att dirigera om metadata eller innehåll i routningen. 

Veta
- [Flödes mönster][7]

### <a name="log-projections"></a>Logga projektioner 

![Logg projektion](media/event-hubs-federation-overview/log-projection.jpg)

I vissa fall vill du ha till gång till det senaste värdet som skickats för alla under strömmar av en händelse, och som ofta åtskiljs av partitionsnyckel. I Apache Kafka uppnås ofta detta genom att aktivera "logg komprimering" på ett ämne, som ignorerar alla utom den senaste händelsen med en unik nyckel. Metoden för logg komprimering har tre sammansatta nack delar: 

- Komprimeringen kräver en kontinuerlig omstrukturering av loggen, vilket är en mycket kostsam åtgärd för en Broker som är optimerad för tilläggs arbets belastningar. 
- Komprimeringen är förstörande och tillåter inte en komprimerad och icke-komprimerad perspektiv av samma ström.
- En komprimerad data ström har fortfarande en sekventiell åtkomst modell, vilket innebär att om du hittar det önskade värdet i loggen måste du läsa hela loggen i värsta fall, vilket vanligt vis leder till optimeringar som implementerar det exakta mönstret som visas här: projicera logg innehållet i en databas eller cache. 

Slutligen är en komprimerad logg ett nyckel värdes lager och det är därför det värsta möjliga implementerings alternativet för en sådan butik. Det är mycket effektivare för sökningar och frågor att skapa och använda en permanent projektion av loggen på ett lämpligt nyckel värdes lager eller en annan databas. 

Eftersom händelserna är oföränderliga och ordningen alltid bevaras i en logg, kommer alla projektioner av en logg till ett nyckel värdes lager alltid vara identiska för samma händelse intervall, vilket innebär att en projektion som du håller på att uppdatera alltid ger en auktoritativ vy och att det aldrig finns någon anledning att återskapa den från logg innehållet när den har skapats. 

Veta
- [Logg projektion][8]

## <a name="replication-application-technologies"></a>Replikering av program tekniker

Implementering av mönster ovan kräver en skalbar och tillförlitlig körnings miljö för de replikeringsalternativ som du vill konfigurera och köra. I Azure är de körnings miljöer som är bäst lämpade för sådana aktiviteter [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) för tillstånds känsliga aktiviteter och [Azure Functions](../azure-functions/functions-overview.md) för tillstånds lösa replikerings aktiviteter.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Tillstånds känsliga replikerings program i Azure Stream Analytics

För tillstånds känsliga replikerings program som behöver ta hänsyn till relationer mellan händelser, skapa sammansatta händelser, utöka händelser eller minska händelser, skapa data agg regeringar och transformera händelse nytto laster [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) är det bästa implementerings alternativet.

I Azure Stream Analytics skapar du [jobb](../stream-analytics/stream-analytics-quick-create-portal.md) [som integrerar indata och](../stream-analytics/stream-analytics-add-inputs.md) [utdata](../stream-analytics/stream-analytics-define-outputs.md) och integrerar data från indata via [frågor](/stream-analytics-query/stream-analytics-query-language-reference) som ger ett resultat som sedan görs tillgängligt i utdata.

Frågor baseras på SQL- [frågespråket](/stream-analytics-query/stream-analytics-query-language-reference) och kan användas för att enkelt filtrera, sortera, sammanställa och koppla strömmande data under en viss tids period. Du kan också utöka detta SQL-språk med [Java Script](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) -och [C#-användardefinierade funktioner (UDF: er)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). Du kan enkelt justera alternativ för händelse ordning och varaktighet för tiden som Windows kan utföra agg regerings åtgärder via enkla språk konstruktioner och/eller konfigurationer.

Varje jobb har en eller flera utdata för transformerade data, och du kan kontrol lera vad som händer i svaret på den information som du har analyserat. Du kan till exempel:

- Skicka data till tjänster som Azure Functions, Service Bus ämnen eller köer för att utlösa kommunikation eller anpassade arbets flöden.
- Skicka data till en Power BI instrument panel för instrument paneler i real tid.
- Lagra data i andra Azure Storage-tjänster (till exempel Azure Data Lake, Azure Synapse Analytics osv.) för att utföra batch Analytics eller träna maskin inlärnings modeller baserat på mycket stora, indexerade pooler med historiska data.
- Lagra projektioner (kallas även "materialiserade vyer") i databaser ([SQL Database](../stream-analytics/sql-database-output.md) [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ).

### <a name="stateless-replication-applications-in-azure-functions"></a>Tillstånds lösa program för replikering i Azure Functions

För tillstånds lösa aktiviteter för replikering där du vill vidarebefordra händelser utan att behöva beakta deras nytto laster eller bearbeta dem på ett enkelt sätt utan att behöva beakta relationerna mellan händelser (förutom deras relativa ordning) kan du använda Azure Functions, vilket ger enorma flexibilitet.

Azure Functions har färdiga, skalbara utlösare och utgående bindningar för [azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), [azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)och [azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), samt anpassade tillägg för [rabbitmq](https://github.com/azure/azure-functions-rabbitmq-extension)och [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). De flesta utlösare kan anpassas dynamiskt efter data flödes behoven genom att skala antalet körnings instanser som körs samtidigt, baserat på dokumenterade mått. 

Azure Functions stöder utgående bindningar för [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) och [Azure-Table Storage](../azure-functions/functions-bindings-storage-table-output.md)för att skapa logg projektioner.

Azure Functions kan köras under en [Azure-hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) och med denna kan den innehålla konfigurations värden för autentiseringsuppgifter i tätt åtkomst till styrt lagrings utrymme i [Azure Key Vault](../key-vault/general/overview.md).

Azure Functions också tillåta att aktiviteterna integreras direkt med virtuella Azure-nätverk och [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) för alla Azure Messaging-tjänster, och det är enkelt att integrera med [Azure Monitor](../azure-monitor/overview.md).

Med Azure Functions-förbruknings planen kan de förinställda utlösarna till och med skala ned till noll medan inga meddelanden är tillgängliga för replikering, vilket innebär att du inte får några kostnader för att hålla konfigurationen redo att skala upp. den viktigaste nack delen med användningen av förbruknings planen är att svars tiden för replikerings aktiviteterna som aktive ras från det här läget är betydligt högre än med värd avtalen där infrastrukturen behålls.  

Till skillnad från allt detta kräver de flesta vanliga MirrorMaker för meddelanden och händelser, till exempel Apache Kafkaens [](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) , att du tillhandahåller en värd miljö och skalar själva replikeringstopologin själv. Det innefattar att konfigurera och integrera säkerhets-och nätverksfunktioner och under lätta flödet av övervaknings data, och sedan har du fortfarande ingen möjlighet att mata in aktiviteter för anpassad replikering i flödet. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Välja mellan Azure Functions och Azure Stream Analytics

Azure Stream Analytics (ASA) är det bästa alternativet när du behöver bearbeta nytto lasten för dina händelser samtidigt som du replikerar dem. ASA kan kopiera händelser en i taget eller så kan den skapa mängder som komprimerar informationen i händelse strömmar innan den vidarebefordras. Det kan [lätt luta till kompletterande referens data](../stream-analytics/stream-analytics-use-reference-data.md) som finns i Azure Blob Storage eller Azure SQL Database utan att behöva importera sådana data till en ström.

Med ASA kan du enkelt skapa beständiga, materialiserade vyer av strömmar i storskaliga databaser. Det är en mycket överlägsen metod för clunky-modellen för "logg komprimering" för Apache Kafka och de temporära tabellerna på klient sidan av Kafka-strömmar. 

ASA kan enkelt bearbeta händelser med nytto laster som är kodade i [CSV-, JSON-och Apache Avro-format](../stream-analytics/stream-analytics-parsing-json.md) och du kan koppla in [anpassade deserialiserare](../stream-analytics/custom-deserializer.md) för andra format.

För alla replikerings aktiviteter där du vill kopiera händelse strömmar "i befintligt skick" och utan att behöva använda nytto lasterna, eller om du behöver implementera en router, utföra ett kryptografiskt arbete, ändra kodningen för nytto laster eller om du behöver fullständig kontroll över innehållet i data strömmen, Azure Functions är det bästa alternativet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi utforskat en rad Federations mönster och förklarat rollen Azure Functions som event-och Messaging Replication runtime i Azure.

Härnäst kanske du vill läsa mer om hur du konfigurerar ett replikerande program med Azure Stream Analytics eller Azure Functions och hur du replikerar händelse flöden mellan Event Hubs och olika händelse-och meddelande system:

- [Mönster för händelsereplikeringsaktiviteter][10]
- [Bearbeta data med Azure Stream Analytics][9]
- [Event Replicators-program i Azure Functions][1]
- [Replikerar händelser mellan Event Hubs][2]
- [Replikera händelser till Azure Service Bus][3]
- [Använd Apache Kafka MirrorMaker med Event Hubs][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md
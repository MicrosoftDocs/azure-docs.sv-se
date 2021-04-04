---
title: Azure Stream Analytics utdata till Azure Cosmos DB
description: Den här artikeln beskriver hur du använder Azure Stream Analytics för att spara utdata till Azure Cosmos DB för JSON-utdata, för dataarkivering och frågor med låg latens i ostrukturerade JSON-data.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 2d00d489ff248ecf5599d78e0a351c93248cf8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018098"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics utdata till Azure Cosmos DB  
Azure Stream Analytics kan rikta [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) för JSON-utdata, aktivera dataarkivering och frågor med låg latens i OSTRUKTURERAde JSON-data. Det här dokumentet beskriver några metod tips för att implementera den här konfigurationen. Vi rekommenderar att du ställer in jobbet på kompatibilitetsnivå nivå 1,2 när du använder Azure Cosmos DB som utdata.

Om du inte känner till Azure Cosmos DB kan du gå till [Azure Cosmos DB-dokumentationen](../cosmos-db/index.yml) för att komma igång. 

> [!Note]
> För tillfället har Stream Analytics endast stöd för anslutning till Azure Cosmos DB via *SQL-API: et*.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du pekar Stream Analytics för att Azure Cosmos DB konton som skapats med andra API: er, kanske data inte lagras korrekt. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Grunderna för Azure Cosmos DB som ett utgående mål
Azure Cosmos DB utdata i Stream Analytics gör det möjligt att skriva data ström bearbetnings resultatet som JSON-utdata till dina Azure Cosmos DB-behållare. 

Stream Analytics skapar inte behållare i databasen. I stället måste du skapa dem direkt. Sedan kan du kontrol lera fakturerings kostnaderna för Azure Cosmos DB behållare. Du kan också justera prestanda, konsekvens och kapacitet för dina behållare direkt med hjälp av [Azure Cosmos DB API: er](/rest/api/cosmos-db/).

> [!Note]
> Du måste lägga till 0.0.0.0 i listan över tillåtna IP-adresser från din Azure Cosmos DB-brandvägg.

I följande avsnitt beskrivs några av behållar alternativen för Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Justera konsekvens, tillgänglighet och svars tid
För att matcha dina program krav kan du Azure Cosmos DB finjustera databasen och behållarna och göra kompromisser mellan konsekvens, tillgänglighet, svars tid och data flöde. 

Beroende på vilka nivåer av Läs konsekvens som ditt scenario behöver för Läs-och skriv fördröjning kan du välja en konsekvens nivå för ditt databas konto. Du kan förbättra genomflödet genom att skala upp begär ande enheter (ru: er) på behållaren. 

Som standard aktiverar Azure Cosmos DB synkron indexering på varje CRUD-åtgärd till din behållare. Detta är ett annat användbart alternativ för att kontrol lera Skriv-/Läs prestanda i Azure Cosmos DB. 

Mer information finns i artikeln [ändra din databas och fråga konsekvens nivåer](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsertar från Stream Analytics
Med Stream Analytics integration med Azure Cosmos DB kan du infoga eller uppdatera poster i din behållare baserat på en specifik **dokument-ID-** kolumn. Detta kallas även för en *upsert*.

Stream Analytics använder en optimistisk upsert metod. Uppdateringar sker bara när en infogning Miss lyckas med en dokument-ID-konflikt. 

Med kompatibilitetsnivån 1,0 utför Stream Analytics den här uppdateringen som en KORRIGERINGs åtgärd, så den aktiverar delvis uppdateringar av dokumentet. Stream Analytics lägger till nya egenskaper eller ersätter en befintlig egenskap stegvis. Men ändringar i värdena för mat ris egenskaper i JSON-dokumentet resulterar i att hela matrisen skrivs över. Det vill säga att matrisen inte sammanfogas. 

Med 1,2 ändras upsert-beteendet för att infoga eller ersätta dokumentet. Det senare avsnittet om kompatibilitetsnivå 1,2 beskriver det här beteendet.

Om det inkommande JSON-dokumentet har ett befintligt ID-fält används automatiskt det fältet som **dokument-ID-** kolumn i Azure Cosmos dB. Eventuella efterföljande skrivningar hanteras som sådana, vilket leder till någon av dessa situationer:

- Unika ID: n som ska infogas.
- Dubbla ID: n och **dokument-ID** : **t** har angetts till upsert.
- Dubblett-ID: n och **dokument-ID: t** har inte angett något lead till fel efter det första dokumentet.

Om du vill spara *alla* dokument, inklusive de som har ett duplicerat ID, byter du namn på fältet ID i frågan (genom att använda nyckelordet **som** ). Låt Azure Cosmos DB skapa fältet ID eller Ersätt ID: t med en annan kolumns värde (genom att använda nyckelordet **som** eller med inställningen **dokument-ID** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Data partitionering i Azure Cosmos DB
Azure Cosmos DB skalar automatiskt partitioner utifrån din arbets belastning. Vi rekommenderar därför [obegränsade](../cosmos-db/partitioning-overview.md) behållare som metod för att partitionera data. När Stream Analytics skriver till obegränsade behållare, används så många parallella skrivare som föregående frågeuttryck eller schema för inschemat.

> [!NOTE]
> Azure Stream Analytics stöder endast obegränsade behållare med partitionsnyckel på den högsta nivån. Stöds till exempel `/region` . Kapslade partitionsnyckel (till exempel `/region/name` ) stöds inte. 

Beroende på ditt val av partitionsnyckel kan du få den här _varningen_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Det är viktigt att välja en partitionsnyckel som har ett antal distinkta värden och som gör att du kan distribuera din arbets belastning jämnt över dessa värden. Som en naturlig artefakt för partitionering begränsas begär Anden som involverar samma partitionsnyckel av det maximala data flödet för en enda partition. 

Lagrings storleken för dokument som tillhör samma partitionsnyckel är begränsad till 20 GB ( [storleks gränsen för den fysiska partitionen](../cosmos-db/partitioning-overview.md) är 50 GB). En [idealisk partitionsnyckel](../cosmos-db/partitioning-overview.md#choose-partitionkey) är en som ofta visas som ett filter i dina frågor och har tillräckligt med kardinalitet för att säkerställa att din lösning är skalbar.

Partitionsnyckel som används för Stream Analytics frågor och Cosmos DB behöver inte vara identiska. Helt parallella topologier rekommenderar att du använder *Indatamask*, `PartitionId` som den Stream Analytics frågans partitionsnyckel, men det kanske inte är det rekommenderade alternativet för en Cosmos DB behållares partitionsnyckel.

En partitionsnyckel är också en avgränsning för transaktioner i lagrade procedurer och utlösare för Azure Cosmos DB. Du bör välja partitionsnyckel så att dokument som förekommer tillsammans i transaktioner delar samma partitionsnyckel. Artikeln [partitionering i Azure Cosmos DB](../cosmos-db/partitioning-overview.md) ger mer information om hur du väljer en partitionsnyckel.

För fasta Azure Cosmos DB behållare kan Stream Analytics inte skala upp eller ut efter att de är fulla. De har en övre gräns på 10 GB och 10 000 RU/s av data flödet. Om du vill migrera data från en fast behållare till en obegränsad behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel) använder du [verktyget datamigrering](../cosmos-db/import-data.md) eller [ändra feed-biblioteket](../cosmos-db/change-feed.md).

Möjligheten att skriva till flera fasta behållare är inaktuell. Vi rekommenderar inte det för att skala ut ditt Stream Analytics-jobb.

## <a name="improved-throughput-with-compatibility-level-12"></a>Förbättrat data flöde med kompatibilitetsnivå 1,2
Med kompatibilitetsnivå 1,2 stöder Stream Analytics inbyggd integrering för Mass skrivning till Azure Cosmos DB. Den här integrationen gör det möjligt att skriva effektivt till Azure Cosmos DB samtidigt som data flödet maximeras och effektiv hantering av begränsnings begär Anden. 

Den förbättrade Skriv mekanismen är tillgänglig under en ny kompatibilitetsnivå på grund av en skillnad i upsert-beteendet. Med nivåer före 1,2 är beteendet för upsert att infoga eller sammanfoga dokumentet. Med 1,2 ändras upsert-beteendet för att infoga eller ersätta dokumentet.

Med nivåer före 1,2 använder Stream Analytics en anpassad lagrad procedur för att massredigera upsert-dokument per partitionsnyckel i Azure Cosmos DB. En batch skrivs som en transaktion. Även om en enskild post träffar ett tillfälligt fel (begränsning), måste hela gruppen provas igen. Detta gör scenarier med mycket rimlig begränsning relativt långsamma.

I följande exempel visas två identiska Stream Analytics jobb som läser från samma Azure Event Hubs-inflöde. Både Stream Analytics jobb är [helt partitionerade](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) med en passthrough-fråga och skrivs till identiska Azure Cosmos DB behållare. Mått till vänster är från det jobb som kon figurer ATS med kompatibilitetsnivå 1,0. Mått till höger konfigureras med 1,2. En partitions nyckel för en Azure Cosmos DB behållare är ett unikt GUID som kommer från indatamängden.

![Jämförelse av Stream Analytics mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Antalet inkommande händelser i Event Hubs är två gånger högre än Azure Cosmos DB behållare (20 000 ru: er) har kon figurer ATS att ta i, vilket innebär att begränsningen förväntas i Azure Cosmos DB. Jobbet med 1,2 skriver dock konsekvent med ett högre data flöde (utgående händelser per minut) och med en lägre genomsnittlig SU%-användning. I din miljö beror skillnaden på några fler faktorer. Dessa faktorer inkluderar val av händelse format, inloggs-/meddelande storlek, partitionsalternativ och fråga.

![Jämförelse av Azure Cosmos DB mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Med 1,2 är Stream Analytics mer intelligent med att använda 100 procent av det tillgängliga data flödet i Azure Cosmos DB med mycket få återsändningar från begränsning eller hastighets begränsning. Detta ger en bättre upplevelse för andra arbets belastningar, t. ex. frågor som körs på behållaren på samma gång. Om du vill se hur Stream Analytics skalas ut med Azure Cosmos DB som mottagare för 1 000 till 10 000 meddelanden per sekund, kan du prova  [det här Azure-exempelprojektet](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb).

Data flödet för Azure Cosmos DB utdata är identiskt med 1,0 och 1,1. Vi *rekommenderar starkt* att du använder kompatibilitetsnivån 1,2 i Stream Analytics med Azure Cosmos dB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Azure Cosmos DB inställningar för JSON-utdata

Om du använder Azure Cosmos DB som utdata i Stream Analytics genereras följande prompt efter information.

![Informations fält för en Azure Cosmos DB-utdataström](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Fält           | Beskrivning|
|-------------   | -------------|
|Utdataalias    | Ett alias som refererar till utdata i din Stream Analytics fråga.|
|Prenumeration    | Azure-prenumerationen.|
|Konto-ID      | Azure Cosmos DB kontots namn eller slut punkts-URI.|
|Kontonyckel     | Den delade åtkomst nyckeln för det Azure Cosmos DB kontot.|
|Databas        | Namnet på Azure Cosmos DBs databasen.|
|Containerns namn | Behållarens namn, till exempel `MyContainer` . En behållare med namnet `MyContainer` måste finnas.  |
|Dokument-ID     | Valfritt. Kolumn namnet i utmatnings händelser som används som den unika nyckel som infognings-eller uppdaterings åtgärder måste baseras på. Om du låter den vara tom infogas alla händelser, utan uppdaterings alternativ.|

När du har konfigurerat Azure Cosmos DB utdata kan du använda den i frågan som mål för en [into-instruktion](/stream-analytics-query/into-azure-stream-analytics). När du använder en Azure Cosmos DB utdata på samma sätt [måste en partitionsnyckel anges explicit](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). 

Output-posten måste innehålla en Skift läges känslig kolumn med namnet efter partitionsnyckel i Azure Cosmos DB. För att uppnå större parallellisering kan instruktionen kräva en [partition by-sats](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) som använder samma kolumn.

Här är en exempel fråga:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Felhantering och återförsök

Om ett tillfälligt fel, tjänsten inte är tillgänglig eller om begränsningen inträffar medan Stream Analytics skickar händelser till Azure Cosmos DB, Stream Analytics försök att slutföra åtgärden på obestämd tid. Men försöker inte försöka igen för följande misslyckade försök:

- Obehörig (HTTP-felkod 401)
- NotFound (HTTP-felkod 404)
- Tillåts inte (HTTP-felkod 403)
- BadRequest (HTTP-felkod 400)

## <a name="common-issues"></a>Vanliga problem

1. En unik index begränsning läggs till i samlingen och utgående data från Stream Analytics bryter mot den här begränsningen. Se till att utdata från Stream Analytics inte bryter mot unika begränsningar eller ta bort begränsningar. Mer information finns i [unika nyckel begränsningar i Azure Cosmos DB](../cosmos-db/unique-keys.md).

2. `PartitionKey`Kolumnen finns inte.

3. `Id`Kolumnen finns inte.

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md) 
* [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure Stream Analytics partitionering av anpassad BLOB-utdata](stream-analytics-custom-path-patterns-blob-storage-output.md)
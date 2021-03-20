---
title: Vanliga frågor och svar om API för Cassandra för Azure Cosmos DB
description: Få svar på vanliga frågor om API för Cassandra för Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030840"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Vanliga frågor och svar om API för Cassandra i Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

I den här artikeln beskrivs skillnaderna mellan Apache Cassandra och API för Cassandra i Azure Cosmos DB. Den innehåller också svar på vanliga frågor om API för Cassandra i Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Viktiga skillnader mellan Apache Cassandra och API för Cassandra

- Apache Cassandra rekommenderar en gräns på 100 MB för storleken på en partitionsnyckel. API för Cassandra för Azure Cosmos DB tillåter upp till 20 GB per partition.
- Med Apache Cassandra kan du inaktivera varaktiga incheckningar. Du kan hoppa över skrivningen till inchecknings loggen och gå direkt till memtables. Detta kan leda till data förlust om noden slutar innan memtables töms till SSTables på disk. Azure Cosmos DB alltid varaktiga genomförande för att förhindra data förlust.
- Apache Cassandra kan se försämrade prestanda om arbets belastningen omfattar många ersättningar eller borttagningar. Orsaken är ett tombstone-värde som Läs arbets belastningen måste hoppa över för att hämta den senaste informationen. API för Cassandra ser inte försämrad läsnings prestanda när arbets belastningen har många ersättningar eller borttagningar.
- Under scenarier med arbets belastningar med hög ersättning måste komprimeringen köras för att slå samman SSTables på disk. (En sammanslagning krävs eftersom Apache Cassandra-skrivningar endast är bifogade. Flera uppdateringar lagras som enskilda SSTable-poster som måste slås samman regelbundet). Den här situationen kan också leda till sänkta Läs prestanda under komprimeringen. Prestanda påverkan inträffar inte i API för Cassandra eftersom API inte implementerar komprimering.
- Det är möjligt att ange en replikeringsrelation på 1 med Apache Cassandra. Det leder dock till låg tillgänglighet om den enda noden med data slutar fungera. Detta är inte ett problem med API för Cassandra för Azure Cosmos DB eftersom det alltid finns en replikeringsrelation på 4 (kvorum 3).
- Att lägga till eller ta bort noder i Apache Cassandra kräver manuella åtgärder, tillsammans med hög CPU-användning på den nya noden medan befintliga noder flyttar några av deras token-intervall till den nya noden. Den här situationen är samma när du inaktiverar en befintlig nod. API för Cassandra skalar dock ut utan problem som observerats i tjänsten eller programmet.
- Du behöver inte ange **num_tokens** på varje nod i klustret som i Apache Cassandra. Azure Cosmos DB hanterar noder och token helt.
- API för Cassandra är fullständigt hanterad. Du behöver inte **nodetool** -kommandona, till exempel Repair och deprovision, som används i Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Andra vanliga frågor och svar

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Vilken protokoll version stöder API för Cassandra?

API för Cassandra för Azure Cosmos DB stöder CQL version 3. x. Dess CQL-kompatibilitet baseras på den offentliga [Apache Cassandra GitHub-lagringsplatsen](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Om du har feedback om stöd för andra protokoll kan du meddela oss via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Varför väljer du data flöde för en tabell som krävs?

Azure Cosmos DB anger standard data flödet för din behållare baserat på var du skapar tabellen från: Azure Portal eller CQL.

Azure Cosmos DB ger garantier för prestanda och svars tid, med övre gränser för åtgärder. Dessa garantier är möjliga när motorn kan genomdriva styrning på klientens verksamhet. Genom att ställa in data flöde ser du till att du får garanterat data flöde och svars tid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden
Du kan [ändra data flödet elastiskt](manage-scale-cassandra.md) för att dra nytta av säsongs beroende i ditt program och spara kostnaderna.

Data flödes konceptet förklaras i [enheter för programbegäran i Azure Cosmos DB](request-units.md) artikeln. Data flödet för en tabell är jämnt fördelat över de underliggande fysiska partitionerna.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Vad är genomflödet i en tabell som skapas via CQL?

Azure Cosmos DB använder enheter för programbegäran per sekund (RU/s) som en valuta för att tillhandahålla data flöde. Tabeller som skapats med CQL har 400 RU som standard. Du kan ändra RU från Azure Portal.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Vad händer när data flödet används?

Azure Cosmos DB ger garantier för prestanda och svars tid, med övre gränser för åtgärder. Dessa garantier är möjliga när motorn kan genomdriva styrning på klientens verksamhet. Genom att ställa in data flöde ser du till att du får garanterat data flöde och svars tid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden

När du går över den här kapaciteten visas följande fel meddelande som anger att din kapacitet har använts:

**överbelastade 0x1001: begäran kan inte bearbetas eftersom "begär ande frekvens är stor"** 

Det är viktigt att se vilka åtgärder (och deras volym) som orsakar det här problemet. Du kan få en uppfattning om förbrukad kapacitet som går över den etablerade kapaciteten med mått på Azure Portal. Sedan måste du se till att kapaciteten förbrukas nästan jämnt över alla underliggande partitioner. Om du ser att en partition utnyttjar merparten av data flödet har du skevat arbets belastning.

Mått är tillgängliga som visar hur data flödet används över timmar, över dagar och per sju dagar, över partitioner eller i mängd. Mer information finns i [övervakning och fel sökning med mått i Azure Cosmos DB](use-metrics.md).

Diagnostikloggar beskrivs i artikeln [Azure Cosmos DB diagnostisk loggning](./monitor-cosmos-db.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Mappar primär nyckeln till partitionens nyckel koncept för Azure Cosmos DB?

Ja, partitionsnyckel används för att placera entiteten på rätt plats. I Azure Cosmos DB används den för att hitta rätt logisk partition som lagras på en fysisk partition. Partitionerings begreppet är väl förklarat i [partitionen och skala i Azure Cosmos DB](partitioning-overview.md) artikel. Den viktigaste sak här är att en logisk partition inte ska gå över 20 GB-gränsen.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Vad händer när jag får ett meddelande om att en partition är full?

Azure Cosmos DB är ett system baserat på service nivå avtal (SLA). Den ger obegränsad skalning, med garantier för svars tid, data flöde, tillgänglighet och konsekvens. Det här obegränsade lagrings utrymmet baseras på vågrätt nedskalning av data, med partitionering som nyckel koncept. Partitionerings begreppet är väl förklarat i [partitionen och skala i Azure Cosmos DB](partitioning-overview.md) artikel.

Du bör följa 20 GB-gränsen för antalet entiteter eller objekt per logisk partition. För att säkerställa att ditt program skalar bra, rekommenderar vi att du *inte* skapar en aktiv partition genom att lagra all information i en partition och fråga den. Det här felet kan bara förekomma om dina data är skevade: det vill säga att du har massor av data för en partitionsnyckel (mer än 20 GB). Du kan hitta data fördelningen med hjälp av lagrings portalen. Sättet att åtgärda det här felet är att återskapa tabellen och välja en detaljerad primär nyckel (partitionsnyckel), vilket gör att data kan distribueras bättre.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Kan jag använda API för Cassandra som ett nyckel värdes lager med miljon tals eller miljard tals partitionsnyckel?

Azure Cosmos DB kan lagra obegränsade data genom att skala ut lagringen. Den här lagringen är oberoende av data flödet. Ja, du kan alltid använda API för Cassandra bara för att lagra och hämta nycklar och värden genom att ange rätt primär nyckel/partitionsnyckel. Dessa enskilda nycklar får sin egen logiska partition och Sit ovanpå en fysisk partition utan problem.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Kan jag skapa mer än en tabell med API för Cassandra?

Ja, det är möjligt att skapa mer än en tabell med API för Cassandra. Var och en av dessa tabeller behandlas som enhet för data flöde och lagring.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Kan jag skapa fler än en tabell i följd?

Azure Cosmos DB är ett resurs styrt system för både data-och kontroll Plans aktiviteter. Behållare, t. ex. samlingar och tabeller, är körnings enheter som är etablerade för en viss data flödes kapacitet. Att skapa dessa behållare i snabb följd är inte en förväntad aktivitet och kan vara begränsad. Om du har test som släpper eller skapar tabeller direkt kan du försöka med att ta bort dem.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Vilket är det maximala antalet tabeller som jag kan skapa?

Det finns ingen fysisk gräns för antalet tabeller. Om du har ett stort antal tabeller (där den totala förvarade storleken är över 10 TB data) som behöver skapas, inte vanliga eller hundratals, skickar du e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Vilket är det maximala antalet tecken som jag kan skapa?

Det finns ingen fysisk gräns för antalet blank steg eftersom de är metadata-behållare. Om du har ett stort antal tecken mellanslag skickar du e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Kan jag få en stor mängd data när de har startat från en vanlig tabell?

Ja. Om du antar enhetligt distribuerade partitioner, hanteras lagrings kapaciteten automatiskt och ökar när du push-överför data. Det innebär att du på ett säkert sätt kan importera så mycket data som du behöver utan att behöva hantera och etablering av noder. Men om du förväntar dig mycket omedelbar data tillväxt är det mer meningsfullt att direkt [etablera för det förväntade data flödet i stället för att](set-throughput.md) starta lägre och öka det direkt.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Kan jag använda YAML-filinställningar för att konfigurera API-beteende?

API för Cassandra för Azure Cosmos DB tillhandahåller kompatibilitet på protokoll nivå för att köra åtgärder. Den döljer komplexiteten vid hantering, övervakning och konfiguration. Som utvecklare/användare behöver du inte bekymra dig om tillgänglighet, tombstones, nyckel cache, rad-cache, blomma-filter och en mängd andra inställningar. API för Cassandra fokuserar på att tillhandahålla de Läs-och skriv prestanda som du behöver utan att behöva konfigurera och hantera.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Är kommandona för att lägga till noder, kluster status och status för noden API för Cassandra stöd?

API för Cassandra fören klar kapacitets planeringen och svarar på de elastiska kraven för data flöde och lagring. Med Azure Cosmos DB etablerar du det data flöde som du behöver. Sedan kan du skala upp och ned valfritt antal gånger under dagen, utan att oroa dig för att lägga till, ta bort eller hantera noder. Du behöver inte använda verktyg för nod-och kluster hantering.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Vad händer med olika konfigurations inställningar för att skapa och skapa tecken i ett enkelt nätverk?

Azure Cosmos DB tillhandahåller global distribution direkt från rutan för tillgänglighets-och låg latens orsaker. Du behöver inte konfigurera repliker eller andra saker. Skrivningar är alltid varaktigt-kvorum allokerat i valfri region där du skriver, samtidigt som du ger prestanda garantier.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Vad händer med olika inställningar för metadata i tabeller?

Azure Cosmos DB ger prestanda garantier för läsningar, skrivningar och data flöden. Så du behöver inte bekymra dig om att använda konfigurations inställningarna och manipulera dem av misstag. De här inställningarna inkluderar filter för blomma, cachelagring, Läs reparation, gc_grace och komprimering memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Stöds Time to Live för Cassandra-tabeller?

Ja, TTL stöds.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hur kan jag övervaka infrastrukturen tillsammans med data flöde?

Azure Cosmos DB är en plattforms tjänst som hjälper dig att öka produktiviteten och inte oroa dig för att hantera och övervaka infrastruktur. Du behöver till exempel inte övervaka nodens status, replik status, GC och OS-parametrar tidigare med olika verktyg. Du behöver bara ta hand om data flödet som är tillgängligt i Portal mått för att se om du får en begränsning och sedan ökar eller minskar data flödet. Du kan:

- Övervaka [service avtal](./monitor-cosmos-db.md)
- Använd [mått](use-metrics.md)
- Använda [diagnostikloggar](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Vilka klient-SDK: er kan fungera med API för Cassandra?

Apache Cassandra SDK: s klient driv rutiner som använder CQLv3 användes för klient program. Om du har andra driv rutiner som du använder eller om du har problem kan du skicka e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Stöds sammansatta partitionsnyckel?

Ja, du kan använda normal syntax för att skapa sammansatta partitionsnyckel.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan jag använda sstableloader för data inläsning?

Nej, sstableloader stöds inte.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Kan jag para ihop ett lokalt Apache Cassandra-kluster med API för Cassandra?

Azure Cosmos DB har för närvarande en optimerad upplevelse för en moln miljö utan att du behöver använda åtgärder. Om du behöver par koppling skickar du e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) med en beskrivning av ditt scenario. Vi arbetar på ett erbjudande för att hjälpa till att para ihop det lokala eller molnbaserade Cassandra-klustret med API för Cassandra för Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Tillhandahåller API för Cassandra fullständiga säkerhets kopior?

Azure Cosmos DB ger två kostnads fria fullständiga säkerhets kopieringar med fyra timmars intervall över alla API: er. Så du behöver inte skapa ett schema för säkerhets kopiering. 

Om du vill ändra kvarhållning och frekvens skickar du ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) eller utlöser ett support ärende. Information om säkerhets kopierings funktioner finns i den [automatiska säkerhets kopieringen och återställningen med Azure Cosmos DB](online-backup-and-restore.md) artikel.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar API för Cassandra-kontot redundans om en region kraschar?

API för Cassandra lånar från den globalt distribuerade plattformen av Azure Cosmos DB. För att säkerställa att ditt program kan tolerera Data Center stillestånd, aktiverar du minst en region för kontot i Azure Portal. Mer information finns i [hög tillgänglighet med Azure Cosmos DB](high-availability.md).

Du kan lägga till så många regioner som du vill för kontot och styra vart det kan redundansväxla till genom att ange en växlings prioritet. Om du vill använda databasen måste du ange ett program där. När du gör det kommer kunderna inte att uppleva nedtid.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Har API för Cassandra indexera alla attribut för en entitet som standard?

Nej. API för Cassandra stöder [sekundära index](cassandra-secondary-index.md), som fungerar på samma sätt som Apache Cassandra. API: t indexerar inte varje attribut som standard.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda den nya API för Cassandra SDK lokalt med emulatorn?

Ja, det stöds. Du hittar information om hur du aktiverar detta i [använd Azure Cosmos DB emulatorn för den lokala utvecklings-och test](local-emulator.md#cassandra-api) artikeln.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Hur migrerar jag data från Apache Cassandra-kluster till Azure Cosmos DB?

Du kan läsa om migreringsåtgärder i själv studie kursen om [att migrera dina data till API för Cassandra kontot i Azure Cosmos DB](cassandra-import-data.md) .


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Var kan jag ge feedback om API för Cassandra funktioner?

Ge feedback via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Nästa steg

- Kom igång med [elastisk skalning av ett Azure Cosmos DB API för Cassandra konto](manage-scale-cassandra.md).

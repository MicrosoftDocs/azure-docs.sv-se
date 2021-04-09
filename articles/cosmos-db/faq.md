---
title: 'Vanliga frågor om olika API: er i Azure Cosmos DB'
description: Få svar på vanliga frågor om Azure Cosmos DB, en globalt distribuerad databas tjänst för flera data modeller. Lär dig mer om kapacitet, prestanda nivåer och skalning.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359837"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Vanliga frågor och svar om olika API: er i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vilka är de vanligaste användnings fallen för Azure Cosmos DB?

Azure Cosmos DB är ett bra alternativ för nya webb-, mobil-, spel-och IoT-program där automatisk skalning, förutsägbar prestanda, snabb ordning i millisekunder och möjligheten att fråga över schema fria data är viktigt. Azure Cosmos DB lånar ut sig för att snabbt utveckla och stödja kontinuerlig upprepning av program data modeller. Program som hanterar innehåll som skapats av användare och data är [vanliga användnings fall för Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder Azure Cosmos DB förutsägbar prestanda?

En [enhet för programbegäran](request-units.md) (ru) är måttet för data flödet i Azure Cosmos dB. Ett 1RU-genomflöde motsvarar genomflödet i Hämta ett dokument på 1 KB. Varje åtgärd i Azure Cosmos DB, inklusive läsningar, skrivningar, SQL-frågor och lagrade procedur körningar har ett deterministiskt RU-värde som baseras på det data flöde som krävs för att slutföra åtgärden. I stället för att tänka på CPU, i/o och minne och hur de påverkar programmets data flöde kan du tänka på ett enda RU-mått.

Du kan konfigurera varje Azure Cosmos-behållare med ett tillhandahållet data flöde i form av ru: er data flöde per sekund. För program oavsett skala kan du mäta enskilda begär Anden för att mäta sina RU-värden och etablera en behållare för att hantera summan av enheter för programbegäran över alla förfrågningar. Du kan också skala upp eller ned din behållares data flöde när behoven i programmet utvecklas. Om du vill ha mer information om enheter för programbegäran och om hur du avgör vilka behållare du behöver kan du prova med [data flödes kalkylatorn](https://www.documentdb.com/capacityplanner)

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder Azure Cosmos DB olika data modeller som nyckel/värde, kolumner, dokument och diagram?

Nyckel/värde-(tabell), kolumner, dokument-och diagram data modeller stöds helt och hållet på grund av designen för ARS (atomer, poster och sekvenser) som Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och projiceras till olika data modeller. API: erna för en delmängd av modeller är tillgängliga just nu (SQL, MongoDB, Table och Gremlin) och andra som är speciella för ytterligare data modeller kommer att vara tillgängliga i framtiden.

Azure Cosmos DB har en schema oberoende indexerings motor som kan indexera alla data som matas in utan att kräva något schema eller sekundära index från utvecklaren. Motorn förlitar sig på en uppsättning logiska index-layouter (inverterad, kolumn, träd) som frikopplar lagrings platsen från under systemen index och fråga bearbetning. Cosmos DB har också möjlighet att stödja en uppsättning av kabel protokoll och API: er på ett utöknings Bart sätt och översätta dem effektivt till kärn data modellen (1) och de logiska index layouterna (2) som gör det möjligt att stödja mer än en data modell internt.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan jag använda flera API: er för att få åtkomst till mina data?

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Om flera modeller innebär Azure Cosmos DB stöder flera API: er och flera data modeller, använder olika API: er olika data format för lagrings-och överförings protokoll. SQL använder till exempel JSON, MongoDB använder BSON, tabellen använder EDM, Cassandra använder CQL, Gremlin använder JSON-format. Därför rekommenderar vi att du använder samma API för all åtkomst till data i ett specifikt konto.

Varje API fungerar oberoende, förutom Gremlin och SQL API, som är driftskompatibla.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA-kompatibel?

Ja, Azure Cosmos DB är HIPAA-kompatibel. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](/compliance/regulatory/offering-hipaa-hitech).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vilka är lagrings gränserna för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vilka är data flödes gränserna för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden data flöde som en behållare har stöd för i Azure Cosmos DB. Den viktiga idén är att distribuera arbets belastningen jämnt mellan ett tillräckligt stort antal partitionstyper.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Är direkta och gateway-anslutnings lägen krypterade?

Ja båda lägena är alltid fullständigt krypterade.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?

Mer information finns på sidan med [pris information för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Azure Cosmos DB användnings kostnader bestäms av antalet etablerade behållare, antalet timmar som behållarna var online och det etablerade data flödet för varje behållare.

### <a name="is-a-free-account-available"></a>Är ett kostnads fritt konto tillgängligt?

Ja, du kan registrera dig för ett tidsbegränsat konto utan kostnad, utan något åtagande. Registrera dig genom att gå till [Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) eller Läs mer i [Azure Cosmos DB vanliga frågor och svar](#try-cosmos-db).

Om du är nybörjare på Azure kan du registrera dig för ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/), vilket ger 30 dagar och en kredit för att testa alla Azure-tjänster. Om du har en Visual Studio-prenumeration är du också berättigad till [kostnads fria Azure-krediter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) som kan användas på alla Azure-tjänster.

Du kan också använda [Azure Cosmos DB emulatorn](local-emulator.md) för att utveckla och testa programmet lokalt utan kostnad, utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?

Om du vill ställa en teknisk fråga kan du publicera till någon av dessa två fråge-och svars Forum:

* [Sidan Microsoft Q&en fråga](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmerings frågor. Se till att din fråga är [i ämne](https://stackoverflow.com/help/on-topic) och [Ange så många detaljer som möjligt, så att frågan blir tydlig och kan besvaras](https://stackoverflow.com/help/how-to-ask).

Om du vill begära nya funktioner skapar du en ny begäran på [användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Försök Azure Cosmos DB prenumerationer

Du kan nu få en tidsbegränsad Azure Cosmos DB upplevelse utan prenumeration, utan kostnad och åtaganden. Om du vill registrera dig för en try Azure Cosmos DB-prenumeration går du till [Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) och använder alla personliga Microsoft-konto (MSA). Den här prenumerationen är separat från den [kostnads fria utvärderings versionen av Azure](https://azure.microsoft.com/free/)och kan användas tillsammans med en kostnads fri utvärderings version av Azure eller en Azure-betald prenumeration.

Försök Azure Cosmos DB prenumerationer visas i Azure Portal nästa andra prenumerationer som är associerade med ditt användar-ID.

Följande villkor gäller för försök Azure Cosmos DB prenumerationer:

* Konto åtkomst kan beviljas till personliga Microsoft-konton (MSA). Undvik att använda Azure Active Directory-konton (Azure AD) eller konton som tillhör företagets Azure AD-klienter, men de kan ha begränsningar på plats som kan blockera åtkomst beviljande.
* En samlad [data flödes behållare](./set-throughput.md#set-throughput-on-a-container) per prenumeration för SQL-, GREMLIN-API och tabell konton.
* Upp till tre [data flöden etablerade samlingar](./set-throughput.md#set-throughput-on-a-container) per prenumeration för MongoDB-konton.
* En [data flödes etablerad databas](./set-throughput.md#set-throughput-on-a-database) per prenumeration. Data flödes etablerade databaser kan innehålla valfritt antal behållare i.
* 10 GB lagrings kapacitet.
* Global replikering är tillgängligt i följande [Azure-regioner](https://azure.microsoft.com/regions/): centrala USA, norra europa och Sydostasien
* Maximalt data flöde på 5 K RU/s vid etablering på container nivå.
* Maximalt data flöde för 20 000 RU/s vid etablering på databas nivå.
* Prenumerationerna upphör att gälla efter 30 dagar och kan utökas till maximalt 31 dagar. Efter förfallo datum raderas informationen som ingår.
* Det går inte att skapa biljetter för Azure-Support för try Azure Cosmos DB-konton. Support ges dock för prenumeranter med befintliga support avtal.

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur gör jag för att registrera dig för Azure Cosmos DB?

Azure Cosmos DB finns i Azure Portal. Börja med att registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB-konto i din Azure-prenumeration.

### <a name="what-is-a-primary-key"></a>Vad är en primär nyckel?

En primär nyckel är en säkerhetstoken för att få åtkomst till alla resurser för ett konto. Individer med nyckeln har Läs-och Skriv behörighet för alla resurser i databas kontot. Använd försiktighet när du distribuerar primära nycklar. Primär primär nyckel och sekundär primär nyckel är tillgängliga på bladet **nycklar** i [Azure Portal][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka regioner som PreferredLocations kan ställas in på?

PreferredLocations-värdet kan anges till någon av de Azure-regioner där Cosmos DB är tillgängligt. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Är det något jag bör vara medveten om när jag distribuerar data över hela världen via Azure-datacentren?

Azure Cosmos DB finns i alla Azure-regioner, enligt vad som anges på sidan [Azure-regioner](https://azure.microsoft.com/regions/) . Eftersom det är kärn tjänsten har alla nya data Center en Azure Cosmos DB närvaro.

När du ställer in en region bör du komma ihåg att Azure Cosmos DB respekterar suveräna och offentliga moln. Det innebär att om du skapar ett konto i en [suverän region](https://azure.microsoft.com/global-infrastructure/)kan du inte replikera ut ur den [suveräna regionen](https://azure.microsoft.com/global-infrastructure/). På samma sätt kan du inte aktivera replikering till andra suveräna platser från ett externt konto.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Är det möjligt att växla från data flödes etablering på behållar nivå till data flödes etablering på databas nivå? Eller vice versa

Data flödes etablering för behållare och databas nivå är separata erbjudanden och växling mellan dessa kräver att data migreras från källa till mål. Det innebär att du måste skapa en ny databas eller en ny behållare och sedan migrera data med hjälp av [utförar-bibliotek](bulk-executor-overview.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Stöder Azure CosmosDB Time Series Analysis?

Ja Azure-CosmosDB stöder tids serie analys, här är ett exempel på ett [mönster för tids serier](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Det här exemplet visar hur du använder ändra feed för att bygga sammansatta vyer över tids serie data. Du kan utöka den här metoden genom att använda Spark-direktuppspelning eller en annan data processor.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Vilka är Azure Cosmos DB tjänst kvoter och data flödes gränser

Mer information finns i avsnittet om kvoter för Azure Cosmos DB [-tjänsten](concepts-limits.md) och över [gränser per behållare och databas](set-throughput.md#comparison-of-models) artiklar.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Vanliga frågor om SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hur gör jag för att börja utveckla mot SQL-API: et?

Först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig för en Azure-prenumeration kan du lägga till en SQL API-behållare i din Azure-prenumeration. Anvisningar om hur du lägger till ett Azure Cosmos DB konto finns i [skapa ett Azure Cosmos Database-konto](create-sql-api-dotnet.md#create-account).

[SDK:er](sql-api-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda [RESTful HTTP-API: er](/rest/api/cosmos-db/) för att interagera med Azure Cosmos DB resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan jag komma åt några färdiga exempel för att få en första start?

Exempel på SQL API [.net](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [Node.js](sql-api-nodejs-samples.md)-och [python](sql-api-python-samples.md) SDK: er finns på GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Stöder SQL-API-databasen schema fria data?

Ja, SQL-API: et gör det möjligt för program att lagra godtyckliga JSON-dokument utan schema definitioner eller tips. Data är omedelbart tillgängliga för frågor via gränssnittet för Azure Cosmos DB SQL-fråga.

### <a name="does-the-sql-api-support-acid-transactions"></a>Har SQL API stöd för transaktioner i SQL API?

Ja, SQL-API: et stöder transaktioner mellan dokument, uttryckt som JavaScript-lagrade procedurer och utlösare. Transaktionerna är begränsade till en enda partition inom varje behållare och körs med sur semantik som "allt eller Nothing", isolerade från andra körning av kod-och användar förfrågningar som körs samtidigt. Om undantag uppstår genom körning av JavaScript-programkod på Server sidan återställs hela transaktionen. 

### <a name="what-is-a-container"></a>Vad är en container?

En behållare är en grupp med dokument och deras associerade JavaScript-programlogik. En behållare är en fakturerbar entitet där [kostnaden](performance-levels.md) bestäms av data flödet och det använda lagrings utrymmet. Behållare kan omfatta en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller data flöde.

* För SQL API kallas resursen för en behållare.
* För Cosmos DB s API för MongoDB-konton mappar en behållare till en samling.
* För Cassandra-och Tabell-API-konton mappar en behållare till en tabell.
* För Gremlin-API-konton mappar en behållare till en graf.

Behållare är även fakturerings enheter för Azure Cosmos DB. Varje behållare faktureras per timme baserat på det etablerade data flödet och det använda lagrings utrymmet. Mer information finns i [Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?

Du kan skapa databaser med hjälp av [Azure Portal](https://portal.azure.com), enligt beskrivningen i [Lägg till en behållare](create-sql-api-java.md#add-a-container), en av [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md): er eller [REST-API: er](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?

Du kan skapa användare och behörigheter genom att använda någon av [Cosmos DB API-SDK](sql-api-sdk-dotnet.md) : [er eller REST-API: er](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Stöder SQL-API SQL?

SQL-frågespråket som stöds av SQL API-konton är en förbättrad delmängd av de fråge funktioner som stöds av SQL Server. Språket Azure Cosmos DB SQL-fråga ger omfattande hierarkiska och relationella operatörer och utöknings barhet via JavaScript-baserade, användardefinierade funktioner (UDF: er). Med JSON-grammatik kan du utforma JSON-dokument som träd med märkta noder, vilka används av både Azure Cosmos DB automatiska indexerings tekniker och SQL-dialekten för Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i artikeln [SQL-fråga][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Stöder SQL-API SQL agg regerings funktioner?

SQL-API: et stöder låg latens agg regering i valfri skala via mängd funktioner,,, `COUNT` `MIN` `MAX` `AVG` och `SUM` via SQL-grammatik. Mer information finns i [mängd funktioner](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hur tillhandahåller SQL-API samtidighet?

SQL-API: et stöder optimistisk samtidighets kontroll (OCC) via HTTP-entitetstyper eller ETags. Varje SQL-API-resurs har en ETag och ETag anges på servern varje gång ett dokument uppdateras. ETag-rubriken och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med If-Match-rubriken för att tillåta att servern bestämmer om en resurs ska uppdateras. Värdet If-Match är det ETag-värde som ska kontrol leras mot. Om ETag-värdet matchar Server-ETag-värdet, uppdateras resursen. Om ETag inte längre är aktuell, avvisar servern åtgärden med svars koden "HTTP 412-förväntas haveri". Klienten hämtar sedan resursen för att hämta det aktuella ETag-värdet för resursen. Dessutom kan ETags användas med rubriken If-None-Match för att avgöra om en återhämtning av en resurs krävs.

Om du vill använda optimistisk samtidighet i .NET använder du klassen [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) . Ett .NET-exempel finns i [program. cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) i DocumentManagement-exemplet på GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hur gör jag för att utföra transaktioner i SQL-API: et?

SQL-API: et stöder språk integrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databas åtgärder inuti skript körs under ögonblicks bild isolering. Om det är en behållare för en enda partition, är körningen begränsad till behållaren. Om behållaren är partitionerad, begränsas körningen till dokument med samma partitions nyckel värde i behållaren. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [JavaScript-programmering på Server sidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag massredigera dokument i Cosmos DB?

Du kan massredigera dokument i Azure Cosmos DB på något av följande sätt:

* Verktyget för Mass utförar, som beskrivs i [använda Mass utförar .NET-bibliotek](bulk-executor-dot-net.md) och [med hjälp av utförar Java-bibliotek](bulk-executor-java.md)
* Verktyget datamigrering, enligt beskrivningen i [Database Migration Tool för Azure Cosmos DB](import-data.md).
* Lagrade procedurer, enligt beskrivningen i [programmerings programmering på Server sidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Stöder SQL-API-cachelagring av resurs länkar?

Ja, eftersom Azure Cosmos DB är en RESTful-tjänst är resurs länkar oföränderliga och kan cachelagras. SQL-API-klienter kan ange ett "If-None-Match"-huvud för läsningar mot resurs-liknande dokument eller behållare och sedan uppdatera sina lokala kopior när Server versionen har ändrats.

### <a name="is-a-local-instance-of-sql-api-available"></a>Är en lokal instans av SQL API tillgängligt?

Ja. [Azure Cosmos DB emulatorn](local-emulator.md) ger en effektiv emulering av Cosmos DBS tjänsten. Den har stöd för funktioner som är identiska med Azure Cosmos DB, inklusive stöd för att skapa och skicka frågor mot JSON-dokument, etablerings-och skalnings samlingar och körning av lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB emulatorn och distribuera dem till Azure i en global skala genom att göra en enda konfigurations ändring av anslutnings slut punkten för Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Varför är långa flytt ALS värden i ett dokument avrundade när de visas från data Utforskaren i portalen.

Detta är en begränsning i JavaScript. Java Script använder dubbla precisions tal för flytt ALS formatering som anges i IEEE 754 och det kan på ett säkert sätt innehålla tal mellan-(2<sup>53</sup> -1) och 2<sup>53</sup>-1 (dvs. 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Var är behörigheter tillåtna i Object-hierarkin?

Att skapa behörigheter med ResourceTokens tillåts på behållar nivån och dess underordnade (till exempel dokument, bilagor). Detta innebär att försök att skapa en behörighet till databasen eller en konto nivå inte är tillåtet för tillfället.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om vanliga frågor och svar i andra API: er, se:

* Vanliga frågor och svar om [Azure Cosmos DB s API för MongoDB](mongodb-api-faq.md)
* Vanliga frågor om [Gremlin-API i Azure Cosmos DB](gremlin-api-faq.md)
* Vanliga frågor och svar om [API för Cassandra i Azure Cosmos DB](cassandra-faq.md)
* Vanliga frågor och svar om [tabell-API i Azure Cosmos DB](table-api-faq.md)
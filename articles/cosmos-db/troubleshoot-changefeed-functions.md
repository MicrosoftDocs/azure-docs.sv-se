---
title: Felsöka problem när du använder Azure Functions utlösare för Cosmos DB
description: Vanliga problem, lösningar och diagnostiska steg när du använder Azure Functions utlösare för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825626"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostisera och Felsök problem när du använder Azure Functions utlösare för Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver vanliga problem, lösningar och diagnostiska steg när du använder [Azure Functions utlösare för Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Beroenden

Azure Functions utlösare och bindningar för Cosmos DB beror på tilläggs paketen över bas Azure Functions Runtime. Behåll alltid dessa paket uppdaterade eftersom de kan innehålla korrigeringar och nya funktioner som kan åtgärda eventuella problem som kan uppstå:

* Azure Functions v2 finns i [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* För Azure Functions v1, se [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Den här artikeln kommer alltid att referera till Azure Functions v2 När körnings miljön anges, såvida det inte uttryckligen anges.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Använda Azure Cosmos DB SDK oberoende

De viktigaste funktionerna i tilläggs paketet är att tillhandahålla stöd för Azure Functions utlösare och bindningar för Cosmos DB. Den innehåller också [Azure Cosmos dB .NET SDK](sql-api-sdk-dotnet-core.md), vilket är användbart om du vill interagera med Azure Cosmos DB program mässigt utan att använda utlösare och bindningar.

Om du vill använda Azure Cosmos DB SDK ser du till att du inte lägger till ett annat NuGet-paket referens i projektet. I stället kan **SDK-referensen lösas via Azure Functions tilläggs paketet**. Använda Azure Cosmos DB SDK separat från utlösaren och bindningarna

Om du dessutom skapar en egen instans av [Azure Cosmos DB SDK-klienten](./sql-api-sdk-dotnet-core.md)manuellt bör du följa mönstret för att bara ha en instans av klienten [med hjälp av en metod för singleton-mönster](../azure-functions/manage-connections.md#documentclient-code-example-c). Den här processen kommer att undvika potentiella problem med socketen i dina åtgärder.

## <a name="common-scenarios-and-workarounds"></a>Vanliga scenarier och lösningar

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function Miss lyckas med fel meddelande samlingen finns inte

Azure Function Miss lyckas med fel meddelandet "det finns ingen käll samlings samling, namn (i databasens databas namn) eller collection2-namnet (i databasen Databas2-Name). Båda samlingarna måste finnas innan lyssnaren startar. Ange "CreateLeaseCollectionIfNotExists" till "true" om du vill skapa en låne samling automatiskt

Det innebär att antingen en eller båda av de Azure Cosmos-behållare som krävs för att utlösaren ska fungera, inte finns eller inte kan komma åt Azure-funktionen. **Själva felet anger vilken Azure Cosmos-databas och container som är den utlösare som söker efter** baserat på din konfiguration.

1. Kontrol lera `ConnectionStringSetting` attributet och att det **refererar till en inställning som finns i din Azure-Funktionsapp**. Värdet för det här attributet får inte vara själva anslutnings strängen, utan namnet på konfigurations inställningen.
2. Kontrol lera att `databaseName` och `collectionName` finns i ditt Azure Cosmos-konto. Om du använder automatisk ersättning (med `%settingName%` mönster) ser du till att namnet på inställningen finns i Azure-Funktionsapp.
3. Om du inte anger någon `LeaseCollectionName/leaseCollectionName` är standardvärdet "lån". Kontrol lera att behållaren finns. Du kan också ställa in `CreateLeaseCollectionIfNotExists` attributet i utlösaren så att `true` det skapas automatiskt.
4. Verifiera ditt [Azure Cosmos-kontos brand Väggs konfiguration](how-to-configure-firewall.md) för att se att den inte blockerar Azure-funktionen.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function kan inte starta med "delad data flödes samling ska ha en partitionsnyckel"

Föregående versioner av Azure Cosmos DB-tillägget har inte stöd för användning av en behållare för lån som skapades i en [delad data flödes databas](./set-throughput.md#set-throughput-on-a-database). Lös problemet genom att uppdatera tillägget [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) för att hämta den senaste versionen.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Det går inte att starta Azure-funktionen med "PartitionKey måste anges för den här åtgärden".

Det här felet innebär att du för närvarande använder en partitionerad låne samling med ett gammalt [anknytnings beroende](#dependencies). Uppgradera till den senaste tillgängliga versionen. Om du för närvarande kör Azure Functions v1 måste du uppgradera till Azure Functions v2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Function kan inte starta med "Lease-samlingen, om partitionerad", måste ha en partitionsnyckel som är lika med ID. "

Det här felet innebär att din aktuella Lease container är partitionerad, men att sökvägen till partitionsnyckel inte är det `/id` . För att lösa det här problemet måste du återskapa behållaren för lån med `/id` som partitionsnyckel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Du ser ett "värde får inte vara null. Parameter namn: o "i Azure Functions loggar när du försöker köra utlösaren

Det här problemet uppstår om du använder Azure Portal och du försöker välja knappen **Kör** på skärmen när du inspekterar en Azure-funktion som använder utlösaren. Utlösaren kräver inte att du väljer Kör för att starta. den startas automatiskt när Azure-funktionen distribueras. Om du vill kontrol lera logg strömmen i Azure-funktionen på Azure Portal, går du bara till den övervakade behållaren och infogar nya objekt. då visas automatiskt den utlösare som körs.

### <a name="my-changes-take-too-long-to-be-received"></a>Mina ändringar tar för lång tid att tas emot

Det här scenariot kan ha flera orsaker och alla bör kontrol leras:

1. Distribueras din Azure-funktion i samma region som ditt Azure Cosmos-konto? För optimal nätverksfördröjning bör både Azure-funktionen och ditt Azure Cosmos-konto vara i samma Azure-region.
2. Sker ändringarna i din Azure Cosmos-container kontinuerligt eller sporadiskt?
Om de sker sporadiskt kan det uppstå en fördröjning mellan det att ändringarna sparas och Azure-funktionen hämtar dem. Detta beror på att när utlösaren söker efter ändringar i Azure Cosmos-containern och inte hittar någon som väntar på att läsas hamnar den i viloläge en viss tid (fem sekunder som standard) innan den söker efter nya ändringar (för att undvika hög RU-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) för utlösaren (värdet förväntas anges i millisekunder).
3. Din Azure Cosmos-behållare kan vara [begränsad till hastighet](./request-units.md).
4. Du kan använda `PreferredLocations` attributet i utlösaren för att ange en kommaavgränsad lista över Azure-regioner för att definiera en anpassad prioriterad anslutnings ordning.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Vissa ändringar upprepas i min utlösare

Begreppet "ändra" är en åtgärd i ett dokument. De vanligaste scenarier där händelser för samma dokument tas emot är:
* Kontot använder eventuell konsekvens. När du förbrukar ändrings flödet i en eventuell konsekvens nivå, kan det finnas dubbla händelser i-mellan efterföljande Läs åtgärder för ändrings flöden (den sista händelsen i en Läs åtgärd visas som första av nästa).
* Dokumentet uppdateras. Ändrings flödet kan innehålla flera åtgärder för samma dokument, om dokumentet tar emot uppdateringar kan det hämta flera händelser (ett för varje uppdatering). Ett enkelt sätt att skilja mellan olika åtgärder för samma dokument är att spåra `_lsn` [egenskapen för varje ändring](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Om de inte matchar varandra är de olika ändringar i samma dokument.
* `id`Kom ihåg att det unika ID: t för ett dokument är `id` och dess partitionsnyckel (det kan finnas två dokument med samma `id` men olika partitionsnyckel) om du identifierar dokument.

### <a name="some-changes-are-missing-in-my-trigger"></a>Vissa ändringar saknas i min utlösare

Om du upptäcker att några av ändringarna som hände i din Azure Cosmos-behållare inte hämtas av Azure-funktionen eller om vissa ändringar saknas i målet när du kopierar dem, följer du stegen nedan.

När din Azure-funktion tar emot ändringarna, bearbetar den ofta dem och kan också skicka resultatet till ett annat mål. När du undersöker saknade ändringar, se till att du **mäter vilka ändringar som tas emot vid** inmatnings punkten (när Azure-funktionen startar), inte på målet.

Om vissa ändringar saknas på målet kan detta betyda att vissa fel inträffar under Azure Function-körningen när ändringarna har tagits emot.

I det här scenariot är det bästa sättet att lägga till `try/catch` block i koden och inom de slingor som kan bearbeta ändringarna, för att upptäcka eventuella fel för en viss delmängd av objekt och hantera dem efter behov (skicka dem till en annan lagrings plats för ytterligare analys eller försök).

> [!NOTE]
> Som standard kör Azure Functions-utlösaren inte en ändringssats om det inträffar ett ohanterat undantag under körningen av din kod. Det innebär att det inte går att bearbeta ändringarna på grund av att det inte gick att bearbeta dem.

Om målet är en annan Cosmos-behållare och du utför upsert-åtgärder för att kopiera objekten, **kontrollerar du att partitionens nyckel definition på både den övervakade behållaren och mål behållaren är densamma**. Upsert-åtgärder kan spara flera käll objekt som ett i målet på grund av den här konfigurations skillnaden.

Om du upptäcker att vissa ändringar inte tagits emot alls av utlösaren är det vanligaste scenariot att **en annan Azure-funktion körs**. Det kan vara en annan Azure Function som distribuerats i Azure eller en Azure-funktion som körs lokalt på en utvecklares dator som har **exakt samma konfiguration** (samma övervakade och lånade behållare) och den här Azure-funktionen stjäl en del av de ändringar som du förväntar dig att Azure-funktionen ska bearbeta.

Scenariot kan också verifieras om du vet hur många Azure Funktionsapp-instanser som du kör. Om du inspekterar din lån behållare och räknar antalet låne objekt i, ska de distinkta värdena för `Owner` egenskapen i dem vara lika med antalet instanser av Funktionsapp. Om det finns fler ägare än de kända Azure Funktionsapp-instanserna innebär det att dessa extra ägare är de som "stjäl" ändringarna.

Ett enkelt sätt att kringgå den här situationen är att använda en `LeaseCollectionPrefix/leaseCollectionPrefix` i din funktion med ett nytt/annat värde eller, även testa med en ny container container.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Måste starta om och bearbeta alla objekt i min behållare från början 
Så här bearbetar du om alla objekt i en behållare från början:
1. Stoppa din Azure-funktion om den körs. 
1. Ta bort dokumenten i låne samlingen (eller ta bort och återskapa låne samlingen så att den är tom)
1. Ange attributet [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger i din funktion till true. 
1. Starta om Azure-funktionen. Nu kommer den att läsa och bearbeta alla ändringar från början. 

Om du anger [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) till True kommer Azure-funktionen att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara när det inte redan har skapats några lån (det vill säga dokument i samlingen lån). Om du anger den här egenskapen till sant, har redan skapade lån ingen påverkan. När en funktion stoppas och startas om i det här scenariot börjar den att läsa från den senaste kontroll punkten, enligt definitionen i samlingen lån. Följ stegen ovan 1-4 om du vill bearbeta från början.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bindning kan bara göras med IReadOnlyList \<Document> eller JArray

Det här felet uppstår om ditt Azure Functions-projekt (eller ett refererat projekt) innehåller en manuell NuGet-referens till Azure Cosmos DB SDK med en annan version än den som anges av [Azure Functions Cosmos DB-tillägget](./troubleshoot-changefeed-functions.md#dependencies).

Undvik den här situationen genom att ta bort den manuella NuGet-referensen som lades till och låta Azure Cosmos DB SDK-referensen matcha genom Azure Functions Cosmos DB tilläggs paketet.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Ändra avsöknings intervall för Azure Function för att identifiera ändringar

Som förklarat tidigare för [mina ändringar tar](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)det för lång tid att ta emot, Azure-funktionen försätts i vilo läge under en konfigurerbar tids period (5 sekunder som standard) innan du söker efter nya ändringar (för att undvika hög ru-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) för utlösaren (värdet förväntas anges i millisekunder).

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning för Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB fel sökning av .NET SDK](./troubleshoot-dot-net-sdk.md)

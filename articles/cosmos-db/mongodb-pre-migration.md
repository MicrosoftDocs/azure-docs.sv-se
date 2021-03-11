---
title: Steg före migrering för datamigrering till Azure Cosmos DBs API för MongoDB
description: Det här dokumentet innehåller en översikt över förutsättningarna för en datamigrering från MongoDB till Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anfeldma
ms.openlocfilehash: cdc5dc9cee3520d9a3f22ff710dfa193e6ef4fed
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553297"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Steg före migrering för datamigrering från MongoDB till Azure Cosmos DB s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Den här MongoDB-guiden är den första i en serie vid migrering av MongoDB till Azure Cosmos DB Mongo-API i skala. Kunder som licensierar och distribuerar MongoDB på en egen hanterad infrastruktur kan vilja minska och hantera kostnaden för sin datafastighet genom att migrera till en hanterad moln tjänst som Azure Cosmos DB med priser för betala per användning och elastisk skalbarhet. Målet med den här serien är att vägleda kunden genom migreringsprocessen:
>
> 1. [För migrering](mongodb-pre-migration.md) – inventera den befintliga MongoDB datafastighet, Planera migreringen och Välj lämpliga Migreringsverktyg.
> 2. Körning – migrera från MongoDB till Azure Cosmos DB med hjälp av de angivna [självstudierna]().
> 3. [Efter migreringen](mongodb-post-migration.md) – uppdatera och optimera befintliga program för att köra mot din nya Azure Cosmos db data fastighets.
>

En solid pre-migration-plan kan ha en storleks ändring som påverkar tids linjen och framgången av teamets migrering. En lämplig jämförbarhet för förmigreringen är att starta ett nytt projekt – du kan börja med att definiera krav, sedan disponera de uppgifter som är inblandade och även prioritera de största uppgifter som ska behandlas först. Detta hjälper till att göra ditt projekt schema förutsägbart, men naturligtvis kan oväntade krav uppstå och komplicera projektschemat. Om du kommer tillbaka till migreringen och skapandet av en omfattande körnings plan i fasen för migrering minimeras risken att du upptäcker oväntade migreringar i processen, vilket sparar tid under migreringen och hjälper dig att säkerställa att målen är uppfyllda.

Innan du migrerar dina data från MongoDB (antingen lokalt eller i molnet) till Azure Cosmos DB s API för MongoDB, bör du:

1. [Läs viktiga överväganden om hur du använder Azure Cosmos DBs API för MongoDB](#considerations)
2. [Välj ett alternativ för att migrera dina data](#options)
3. [Beräkna det data flöde som krävs för dina arbets belastningar](#estimate-throughput)
4. [Välj en optimal partitionsnyckel för dina data](#partitioning)
5. [Förstå indexerings principen som du kan ange för dina data](#indexing)

Om du redan har slutfört ovanstående krav för migrering kan du [migrera MongoDB-data till Azure Cosmos DB s API för MongoDB med hjälp av Azure Database migration service](../dms/tutorial-mongodb-cosmos-db.md). Om du inte har skapat ett konto kan du dessutom bläddra bland de [snabb starter](create-mongodb-dotnet.md) som visar stegen för att skapa ett konto.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Att tänka på när du använder Azure Cosmos DBs API för MongoDB

Följande är särskilda egenskaper för Azure Cosmos DB s API för MongoDB:

- **Kapacitets modell**: databasens kapacitet på Azure Cosmos DB baseras på en data flödes-baserad modell. Den här modellen är baserad på [enheter för programbegäran per sekund](request-units.md), som är en enhet som representerar antalet databas åtgärder som kan utföras mot en samling per sekund. Den här kapaciteten kan allokeras på [en databas eller samlings nivå](set-throughput.md), och den kan tillhandahållas i en fördelnings modell, eller med hjälp av [autoskalning av allokerat data flöde](provision-throughput-autoscale.md).

- **Enheter för programbegäran**: varje databas åtgärd har en ru: er-kostnad (associerad Request units) i Azure Cosmos dB. Vid körning subtraheras detta från den tillgängliga enhets enhets nivån på en specifik sekund. Om en begäran kräver mer ru: er än den för närvarande allokerade RU/s finns det två alternativ för att lösa problemet, öka mängden ru: er eller vänta tills nästa sekund startar och försök sedan igen.

- **Elastisk kapacitet**: kapaciteten för en specifik samling eller databas kan ändras när som helst. Detta gör att databasen kan anpassas elastiskt till data flödes kraven för din arbets belastning.

- **Automatisk horisontell partitionering**: Azure Cosmos DB tillhandahåller ett automatiskt partitionerings system som bara kräver en Shard (eller en partitionsnyckel). [Mekanismen för automatisk partitionering](partitioning-overview.md) delas över alla Azure Cosmos DB-API: er och möjliggör sömlös data och genom hela skalan genom horisontell distribution.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Migrations alternativ för Azure Cosmos DBs API för MongoDB

[Azure Database migration service för Azure Cosmos DB s API för MongoDB](../dms/tutorial-mongodb-cosmos-db.md) ger en mekanism som fören klar migreringen av data genom att tillhandahålla en helt hanterad värd plattform, alternativ för migrerings övervakning och automatisk begränsnings hantering. Den fullständiga listan med alternativ är följande:

|**Typ av migrering**|**Lösning**|**Överväganden**|
|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; Lämplig för stora data uppsättningar och sköter att replikera Live-ändringar <br/>&bull; Fungerar endast med andra MongoDB-källor|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; Lämplig för stora data uppsättningar och sköter att replikera Live-ändringar <br/>&bull; Fungerar endast med andra MongoDB-källor|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Enkelt att konfigurera och stödja flera källor <br/>&bull; Använder Azure Cosmos DB bulk utförar-biblioteket <br/>&bull; Lämplig för stora data uppsättningar <br/>&bull; Brist på kontroll punkter innebär att eventuella problem under migreringen skulle kräva en omstart av hela migreringsprocessen<br/>&bull; Brist på en kö för obeställbara meddelanden skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen <br/>&bull; Behöver anpassad kod för att öka Läs data flödet för vissa data källor|
|Offline|[Befintliga Mongo-verktyg (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Enkelt att konfigurera och integrera <br/>&bull; Kräver anpassad hantering för begränsningar|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Beräkna data flödes behovet för dina arbets belastningar

I Azure Cosmos DB allokeras data flödet i förväg och mäts i enheter för programbegäran (RU) per sekund. Till skillnad från virtuella datorer eller lokala servrar är ru: er enkelt att skala upp och ned när som helst. Du kan ändra antalet etablerade ru: er direkt. Mer information finns i [enheter för programbegäran i Azure Cosmos DB](request-units.md).

Du kan använda [Azure Cosmos DB kapacitets kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att fastställa hur många enheter för programbegäran som baseras på databas konto konfigurationen, mängden data, dokument storlek och nödvändiga läsningar och skrivningar per sekund.

Följande är viktiga faktorer som påverkar antalet ru: er som krävs:
- **Dokument storlek**: när ett objekts eller dokuments storlek ökar ökar antalet ru: er som förbrukas för att läsa eller skriva objektet/dokumentet också att öka.

- **Antal dokument egenskaper**: antalet ru: er som används för att skapa eller uppdatera ett dokument är relaterat till antal, komplexitet och längd på egenskaperna. Du kan minska användningen av begär ande enheter för Skriv åtgärder genom [att begränsa antalet indexerade egenskaper](mongodb-indexing.md).

- **Fråga mönster**: en frågas komplexitet påverkar hur många enheter för programbegäran som används av frågan. 

Det bästa sättet att förstå kostnaden för frågor är att använda exempel data i Azure Cosmos DB [och köra exempel frågor från MongoDB-gränssnittet](connect-mongodb-account.md) med hjälp av `getLastRequestStastistics` kommandot för att hämta begär ande avgiften, vilket kommer att ge ut antalet ru: er som förbrukas:

`db.runCommand({getLastRequestStatistics: 1})`

Det här kommandot kommer att skriva ut ett JSON-dokument som liknar följande:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Du kan också använda [diagnostikinställningar](cosmosdb-monitor-resource-logs.md) för att förstå frekvensen och mönstren för de frågor som körs mot Azure Cosmos dB. Resultaten från diagnostikloggar kan skickas till ett lagrings konto, en EventHub-instans eller Azure- [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Välj partitionsnyckel
Partitionering, även kallat horisontell partitionering, är en viktig faktor innan data migreras. Azure Cosmos DB använder fullständigt hanterad partitionering för att öka kapaciteten i en databas för att uppfylla kraven för lagring och data flöde. Den här funktionen behöver inte vara värd för eller konfigurering av routningsservrar.   

På ett liknande sätt lägger partitionerings kapaciteten automatiskt till kapacitet och återskapar data enligt detta. Mer information och rekommendationer om hur du väljer rätt partitionsnyckel för dina data finns i artikeln om [att välja en partitionsnyckel](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexera dina data

Azure Cosmos DBens API för MongoDB Server version 3,6 och högre indexerar automatiskt `_id` fältet endast. Det går inte att ta bort det här fältet. Det tillämpar automatiskt `_id` fältets unikhet per Shard-nyckel. Om du vill indexera ytterligare fält tillämpar du [MongoDB index-Management-kommandon](mongodb-indexing.md). Den här indexeringspolicyn skiljer sig från Azure Cosmos DB SQL-API:et som standardmässigt indexerar alla fält.

De indexerings funktioner som tillhandahålls av Azure Cosmos DB innefattar att lägga till sammansatta index, unika index och Time to Live (TTL) index. Index hanterings gränssnittet är mappat till `createIndex()` kommandot. Läs mer vid [indexering i Azure Cosmos DBS API för MongoDB](mongodb-indexing.md)-artikel.

[Azure Database migration service](../dms/tutorial-mongodb-cosmos-db.md) migrerar automatiskt MongoDB-samlingar med unika index. Däremot måste de unika indexen skapas innan migreringen. Azure Cosmos DB har inte stöd för att skapa unika index när det redan finns data i samlingarna. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera dina MongoDB-data till Cosmos DB med hjälp av Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Etablera data flöde i Azure Cosmos-behållare och databaser](set-throughput.md)
* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Global distribution i Azure Cosmos DB](distribute-data-globally.md)
* [Indexering i Azure Cosmos DB](index-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)

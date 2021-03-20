---
title: Alternativ för Cosmos DB migrering
description: Det här dokumentet beskriver de olika alternativen för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097591"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Alternativ för att migrera dina lokala eller molnbaserade data till Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Du kan läsa in data från olika data källor till Azure Cosmos DB. Eftersom Azure Cosmos DB stöder flera API: er kan målen vara alla befintliga API: er. Här följer några scenarier där du migrerar data till Azure Cosmos DB:

* Flytta data från en Azure Cosmos-behållare till en annan behållare i samma databas eller till en annan databas.
* Flytta data mellan dedikerade behållare till delade databas behållare.
* Flytta data från ett Azure Cosmos-konto som finns i Region1 till ett annat Azure Cosmos-konto i samma eller en annan region.
* Flytta data från en källa, till exempel Azure Blob Storage, en JSON-fil, Oracle Database, Couchbase, DynamoDB till Azure Cosmos DB.

För att stödja migrations vägar från olika källor till de olika Azure Cosmos DB-API: erna finns det flera lösningar som ger specialiserad hantering för varje sökväg för migrering. Det här dokumentet listar de tillgängliga lösningarna och beskriver deras fördelar och begränsningar.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Faktorer som påverkar valet av migreringsverktyg

Följande faktorer är avgörande för valet av migreringsverktyg:

* **Online- eller offlinemigrering**: Med många migreringsverktyg kan du bara göra en engångsmigrering. Det innebär att de program som använder databasen kanske får en viss stilleståndstid. Vissa migreringslösningar kan tillhandahålla ett sätt att göra en direktmigrering med en konfigurerad replikeringspipeline mellan källan och målet.

* **Data källa**: befintliga data kan finnas i olika data källor som Oracle DB2, DataStax Cassanda, Azure SQL Database, postgresql osv. Data kan också finnas i ett befintligt Azure Cosmos DB konto och syftet med migreringen kan vara att ändra data modellen eller partitionera om data i en behållare med en annan partitionsnyckel.

* **Azure Cosmos DB-API**: För SQL-API:et i Azure Cosmos DB finns flera olika verktyg som har utvecklats av Azure Cosmos DB-teamet. Dessa kan vara till hjälp i olika migreringsscenarier. Alla andra API:er har egna specialiserade verktyg som har utvecklats och underhålls av communityn. Eftersom Azure Cosmos DB stöder dessa API:er på trådprotokollnivå ska dessa verktyg också fungera vid migrering av data till Azure Cosmos DB. Men hanteringen av begränsningar kan behöva anpassas för dessa verktyg eftersom det här konceptet är specifikt för Azure Cosmos DB.

* **Storlek på data**: De flesta migreringsverktyg fungerar mycket bra för mindre datauppsättningar. Om datauppsättningen överskrider ett par hundra GB är valet av migreringsverktyg begränsat. 

* **Förväntad varaktighet för migreringen**: Migreringar kan konfigureras så att de utförs i en långsam, inkrementell takt där ett mindre dataflöde används, eller så kan hela dataflödet som etablerats på Azure Cosmos DB-målcontainern användas för att slutföra migreringen på kortare tid.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|Typ av migrering|Lösning|Källor som stöds|Mål som stöds|Överväganden|
|---------|---------|---------|---------|---------|
|Offline|[Datamigreringsverktyget](import-data.md)| &bull;JSON/CSV-filer<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure-Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;API för Azure Cosmos DB tabeller<br/>&bull;JSON-filer |&bull; Enkelt att konfigurera och stödja flera källor. <br/>&bull; Passar inte för stora data mängder.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV-filer<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;API för Azure Cosmos DB för MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/> <br/>Se [Azure Data Factory](../data-factory/connector-overview.md) -artikeln för andra källor som stöds.|&bull;Azure Cosmos DB SQL API<br/>&bull;API för Azure Cosmos DB för MongoDB<br/>&bull;JSON-filer <br/><br/> Se [Azure Data Factory](../data-factory/connector-overview.md) -artikeln för andra mål som stöds. |&bull; Enkelt att konfigurera och stödja flera källor.<br/>&bull; Använder Azure Cosmos DB bulk utförar-biblioteket. <br/>&bull; Lämplig för stora data uppsättningar. <br/>&bull; Brist på kontroll punkt – det innebär att om ett problem inträffar under migreringen måste du starta om hela migreringsprocessen.<br/>&bull; Avsaknad av kö för obeställbara meddelanden – det innebär att några felaktiga filer kan stoppa hela migreringsprocessen.|
|Offline|[Azure Cosmos DB Spark-anslutning](spark-connector.md)|Azure Cosmos DB SQL API. <br/><br/>Du kan använda andra källor med ytterligare anslutningar från Spark-eko systemet.| Azure Cosmos DB SQL API. <br/><br/>Du kan använda andra mål med ytterligare anslutningar från Spark-eko systemet.| &bull; Använder Azure Cosmos DB bulk utförar-biblioteket. <br/>&bull; Lämplig för stora data uppsättningar. <br/>&bull; Kräver en anpassad Spark-installation. <br/>&bull; Spark är känsligt för inkonsekvenser i schemat och det kan vara ett problem under migreringen. |
|Offline|[Anpassat verktyg med Cosmos DB-bibliotek för Mass utförar](migrate-cosmosdb-data.md)| Källan är beroende av din anpassade kod | Azure Cosmos DB SQL API| &bull; Tillhandahåller kontroll punkter, funktioner för obeställbara meddelanden som ökar återhämtnings kapaciteten för migreringen. <br/>&bull; Lämplig för mycket stora data uppsättningar (10 TB +).  <br/>&bull; Kräver anpassad installation av det här verktyget som körs som en App Service. |
|Online|[Cosmos DB Functions + ChangeFeed-API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Enkelt att konfigurera. <br/>&bull; Fungerar bara om källan är en Azure Cosmos DB behållare. <br/>&bull; Passar inte för stora data mängder. <br/>&bull; Fångar inte in borttagningar från käll behållaren. |
|Online|[Tjänsten för anpassad migrering med ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Tillhandahåller förlopps spårning. <br/>&bull; Fungerar bara om källan är en Azure Cosmos DB behållare. <br/>&bull; Fungerar även för större data uppsättningar.<br/>&bull; Kräver att användaren konfigurerar en App Service som ska vara värd för bytet av Change-feed. <br/>&bull; Fångar inte in borttagningar från käll behållaren.|
|Online|[Striims](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Se [striims-webbplatsen](https://www.striim.com/sources-and-targets/) för andra källor som stöds. |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB API för Cassandra<br/><br/> Se [striims-webbplatsen](https://www.striim.com/sources-and-targets/) för andra mål som stöds. | &bull; Fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server.<br/>&bull; Lätt att bygga ETL-pipelines och ger en instrument panel för övervakning. <br/>&bull; Har stöd för större data mängder. <br/>&bull; Eftersom det här är ett verktyg från tredje part måste det köpas från Marketplace och installeras i användarens miljö.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo-API

|Typ av migrering|Lösning|Källor som stöds|Mål som stöds|Överväganden|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|API för Azure Cosmos DB för MongoDB |&bull; Använder Azure Cosmos DB bulk utförar-biblioteket. <br/>&bull; Lämplig för stora data uppsättningar och sköter att replikera Live-ändringar. <br/>&bull; Fungerar endast med andra MongoDB-källor.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| API för Azure Cosmos DB för MongoDB| &bull; Använder Azure Cosmos DB bulk utförar-biblioteket. <br/>&bull; Lämplig för stora data uppsättningar och sköter att replikera Live-ändringar. <br/>&bull; Fungerar endast med andra MongoDB-källor.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV-filer<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;API för Azure Cosmos DB för MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/><br/> Se [Azure Data Factory](../data-factory/connector-overview.md) -artikeln för andra källor som stöds. | &bull;Azure Cosmos DB SQL API<br/>&bull;API för Azure Cosmos DB för MongoDB <br/>&bull; JSON-filer <br/><br/> Se [Azure Data Factory](../data-factory/connector-overview.md) -artikeln för andra mål som stöds.| &bull; Enkelt att konfigurera och stödja flera källor. <br/>&bull; Använder Azure Cosmos DB bulk utförar-biblioteket. <br/>&bull; Lämplig för stora data uppsättningar. <br/>&bull; Brist på kontroll punkter innebär att eventuella problem under migreringen skulle kräva en omstart av hela migreringsprocessen.<br/>&bull; Brist på kö för obeställbara meddelanden skulle innebära att några felaktiga filer kan stoppa hela migreringsprocessen. <br/>&bull; Behöver anpassad kod för att öka Läs data flödet för vissa data källor.|
|Offline|[Befintliga Mongo-verktyg (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | API för Azure Cosmos DB för MongoDB| &bull; Enkelt att konfigurera och integrera. <br/>&bull; Kräver anpassad hantering för begränsningar.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

|Typ av migrering|Lösning|Källor som stöds|Mål som stöds|Överväganden|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh COPY-kommando](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV-filer | Azure Cosmos DB Cassandra API| &bull; Enkelt att konfigurera. <br/>&bull; Passar inte för stora data mängder. <br/>&bull; Fungerar bara när källan är en Cassandra-tabell.|
|Offline|[Kopiera tabell med Spark](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; Kan använda Spark-funktioner för att parallellisera omvandling och inmatning. <br/>&bull; Behöver konfigureras med en anpassad princip för återförsök för att hantera begränsningar.|
|Online|[Striims (från Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Se [striims-webbplatsen](https://www.striim.com/sources-and-targets/) för andra källor som stöds.|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> Se [striims-webbplatsen](https://www.striim.com/sources-and-targets/) för andra mål som stöds.| &bull; Fungerar med en stor mängd olika källor som Oracle, DB2, SQL Server. <br/>&bull; Lätt att bygga ETL-pipelines och ger en instrument panel för övervakning. <br/>&bull; Har stöd för större data mängder. <br/>&bull; Eftersom det här är ett verktyg från tredje part måste det köpas från Marketplace och installeras i användarens miljö.|
|Online|[Blitzz (från Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Se [Blitzz-webbplatsen](https://www.blitzz.io/) för andra källor som stöds. |Azure Cosmos DB API för Cassandra. <br/><br/>Se [Blitzz-webbplatsen](https://www.blitzz.io/) för andra mål som stöds. | &bull; Har stöd för större data mängder. <br/>&bull; Eftersom det här är ett verktyg från tredje part måste det köpas från Marketplace och installeras i användarens miljö.|

## <a name="other-apis"></a>Andra API:er

För andra API: er än SQL API, Mongo API och API för Cassandra, finns det olika verktyg som stöds av varje API: s befintliga eko system. 

**Tabell-API** 

* [Datamigreringsverktyget](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin-API**

* [Graph utförar-bibliotek](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer genom att testa exempel programmen som använder bulk utförar-biblioteket i [.net](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Bulk utförar-biblioteket är integrerat i Cosmos DB Spark-anslutningsprogrammet för mer information finns i artikeln [Azure Cosmos DB Spark Connector](spark-connector.md) .  
* Kontakta Azure Cosmos DB produkt teamet genom att öppna ett support ärende under "allmän råd givande" problem typ och "stora (TB +) migreringar" problem under typ för ytterligare hjälp med storskalig migrering.

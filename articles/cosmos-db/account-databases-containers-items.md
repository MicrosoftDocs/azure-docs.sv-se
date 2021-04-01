---
title: Resursmodell för Azure Cosmos DB
description: I den här artikeln beskrivs Azure Cosmos DB resurs modell som innehåller Azure Cosmos-kontot, databasen, behållaren och objekten. Den omfattar också hierarkin för dessa element i ett Azure Cosmos-konto.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627356"
---
# <a name="azure-cosmos-db-resource-model"></a>Resursmodell för Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB är en fullständigt hanterad PaaS-tjänst (Platform-as-a-Service). Om du vill börja använda Azure Cosmos DB bör du först skapa ett Azure Cosmos-konto i din Azure-prenumeration och databaser, behållare, objekt under det. I den här artikeln beskrivs Azure Cosmos DB resurs modell och olika entiteter i resurs modellens hierarki.

Azure Cosmos-kontot är den grundläggande enheten för global distribution och hög tillgänglighet. Ditt Azure Cosmos-konto innehåller ett unikt DNS-namn och du kan hantera ett konto med hjälp av Azure Portal eller Azure CLI, eller genom att använda olika språkspecifika SDK: er. Mer information finns i [Hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md). För att globalt distribuera dina data och data flödet i flera Azure-regioner kan du när som helst lägga till och ta bort Azure-regioner i ditt konto. Du kan konfigurera ditt konto så att det antingen har en enda region eller flera Skriv regioner. Mer information finns i [så här lägger du till och tar bort Azure-regioner i ditt konto](how-to-manage-database-account.md). Du kan konfigurera [standard konsekvens](consistency-levels.md) nivån för ett konto.

## <a name="elements-in-an-azure-cosmos-account"></a>Element i ett Azure Cosmos-konto

En Azure Cosmos-behållare är den grundläggande enheten för skalbarhet. Du kan i princip ha ett obegränsat, allokerat data flöde (RU/s) och lagrings utrymme i en behållare. Azure Cosmos DB transparent partitionering av din behållare med hjälp av den logiska partitionsnyckel som du anger för att elastiskt skala ditt etablerade data flöde och lagrings utrymme elastiskt.

För närvarande kan du skapa högst 50 Azure Cosmos-konton under en Azure-prenumeration (detta är en mjuk gräns som kan ökas via support förfrågan). Ett enda Azure Cosmos-konto kan i princip hantera en obegränsad mängd data och ett allokerat data flöde. Om du vill hantera dina data och ett allokerat data flöde kan du skapa en eller flera Azure Cosmos-databaser under ditt konto och inom den databasen, kan du skapa en eller flera behållare. Följande bild visar hierarkin för element i ett Azure Cosmos-konto:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Hierarki för ett Azure Cosmos-konto" border="false":::

När du har skapat ett konto i din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. 

Följande bild visar hierarkin för olika entiteter i ett Azure Cosmos DB konto:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos Account-entiteter" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure Cosmos-databaser under ditt konto. En databas motsvarar ett namn område. En databas är enhets hantering för en uppsättning av Azure Cosmos-behållare. Följande tabell visar hur en databas mappas till olika API-/regionsspecifika entiteter:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Keyspace | Databas | Databas | NA |

> [!NOTE]
> När du skapar din första tabell med Tabell-API-konton skapas en standard databas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med Azure Cosmos-API: er enligt beskrivningen i följande tabell:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Ja | NA | NA |
|Läs databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Ja | NA | NA |
|Skapa ny databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Ja | NA | NA |
|Uppdatera databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Ja | NA | NA |

## <a name="azure-cosmos-containers"></a>Azure Cosmos-containrar

En Azure Cosmos-behållare är enhets skalbarhet både för allokerat data flöde och lagring. En behållare är vågrätt partitionerad och replikeras sedan över flera regioner. De objekt som du lägger till i behållaren grupperas automatiskt i logiska partitioner, som distribueras över fysiska partitioner utifrån partitionsnyckel. Data flödet i en behållare distribueras jämnt över de fysiska partitionerna. Mer information om partitionering och partitionerings nycklar finns i [partitionera data](partitioning-overview.md). 

När du skapar en behållare konfigurerar du data flödet i något av följande lägen:

* **Dedikerat allokerat data flödes läge**: det data flöde som har allokerats på en behållare är exklusivt reserverat för den behållaren och den backas upp av service avtal. Mer information finns i [så här etablerar du data flöde på en behållare](how-to-provision-container-throughput.md).

* **Delat etablerat data flödes läge**: de här behållarna delar det etablerade data flödet med andra behållare i samma databas (exklusive behållare som har kon figurer ATS med dedikerat etablerat data flöde). Med andra ord delas det etablerade data flödet på databasen över alla "delade data flöde"-behållare. Mer information finns i [så här etablerar du data flöde i en databas](how-to-provision-database-throughput.md).

> [!NOTE]
> Du kan bara konfigurera delade och dedikerade data flöde när du skapar databasen och behållaren. Om du vill byta från dedikerat dataflödesläge till delat dataflödesläge (och vice versa) efter det att containern har skapats måste du skapa en ny container och migrera data till den nya containern. Du kan migrera data med hjälp av funktionen Azure Cosmos DB ändra feed.

En Azure Cosmos-behållare kan skala elastiskt, oavsett om du skapar behållare med hjälp av dedikerade eller delade allokerade data flödes lägen.

En behållare är en schema-oberoende behållare med objekt. Objekt i en behållare kan ha godtyckliga scheman. Till exempel kan ett objekt som representerar en person och ett objekt som representerar en bil placeras i *samma behållare*. Som standard indexeras alla objekt som du lägger till i en behållare automatiskt utan att det krävs explicita index eller schema hantering. Du kan anpassa indexerings beteendet genom att konfigurera [indexerings principen](index-overview.md) på en behållare. 

Du kan ställa in [Time to Live (TTL)](time-to-live.md) på valda objekt i en behållare eller för hela behållaren för att på ett städat sätt Rensa objekten från systemet. Azure Cosmos DB tar automatiskt bort objekten när de upphör att gälla. Det garanterar också att en fråga som utförs på behållaren inte returnerar de inaktuella objekten inom en fast bindning. Läs mer i [Konfigurera TTL på din behållare](how-to-time-to-live.md).

Du kan använda [ändra feed](change-feed.md) för att prenumerera på den Operations logg som hanteras för varje logisk partition i din behållare. Ändra feed innehåller loggen över alla uppdateringar som utförs på behållaren, tillsammans med objekten före och efter för objekten. Mer information finns i [bygga reaktiva program med hjälp av ändra feed](serverless-computing-database.md). Du kan också konfigurera Retentions tiden för ändrings flödet med hjälp av principen för att ändra feed i behållaren.

Du kan registrera [lagrade procedurer, utlösare, användardefinierade funktioner (UDF: er)](stored-procedures-triggers-udfs.md)och [samkörnings procedurer](how-to-manage-conflicts.md) för din behållare.

Du kan ange en [unik nyckel begränsning](unique-keys.md) i din Azure Cosmos-behållare. Genom att skapa en unik nyckel princip säkerställer du att ett eller flera värden är unika per logisk partitionsnyckel. Om du skapar en behållare med hjälp av en unik nyckel princip kan inga nya eller uppdaterade objekt med värden som duplicerar värdena som anges av den unika nyckel begränsningen skapas. Mer information finns i [unika nyckel begränsningar](unique-keys.md).

En behållare är specialiserad i API-specifika entiteter som visas i följande tabell:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Container | Tabell | Samling | Graph | Tabell |

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaper för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa egenskaper inte är direkt exponerade. I följande tabell beskrivs en lista över systemdefinierade egenskaper:

| Systemdefinierad egenskap | Systemgenererad eller användare kan konfigureras | Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unikt ID för behållare | Ja | Inga | Inga | Inga | Inga |
|\_etag | Systemgenererad | Entity-taggen används för optimistisk concurrency-kontroll | Ja | Inga | Inga | Inga | Inga |
|\_via | Systemgenererad | Senast uppdaterad tidsstämpel för behållaren | Ja | Inga | Inga | Inga | Inga |
|\_ständiga | Systemgenererad | Adresser bara URI för behållaren | Ja | Inga | Inga | Inga | Inga |
|id | Kan konfigureras av användare | Användardefinierat unikt namn på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Kan konfigureras av användare | Ger möjlighet att ändra index Sök väg, index typ och index läge | Ja | Inga | Inga | Inga | Ja |
|TimeToLive | Kan konfigureras av användare | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en angiven tids period. Mer information finns i [Time to Live](time-to-live.md). | Ja | Inga | Inga | Inga | Ja |
|changeFeedPolicy | Kan konfigureras av användare | Används för att läsa ändringar som gjorts i objekt i en behållare. Mer information finns i [ändra feed](change-feed.md). | Ja | Inga | Inga | Inga | Ja |
|uniqueKeyPolicy | Kan konfigureras av användare | Används för att säkerställa att ett eller flera värden är unika i en logisk partition. Mer information finns i [unika nyckel begränsningar](unique-keys.md). | Ja | Inga | Inga | Inga | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare stöder följande åtgärder när du använder någon av Azure Cosmos-API: erna:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja | Ja | Ja | Ja | NA | NA |
| Läsa en behållare | Ja | Ja | Ja | Ja | NA | NA |
| Skapa en ny behållare | Ja | Ja | Ja | Ja | NA | NA |
| Uppdatera en behållare | Ja | Ja | Ja | Ja | NA | NA |
| Ta bort en container | Ja | Ja | Ja | Ja | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos-objekt

Beroende på vilket API du använder kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod eller kant i ett diagram. I följande tabell visas mappningen av API-/regionsspecifika entiteter till ett Azure Cosmos-objekt:

| Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-objekt | Objekt | Rad | Dokument | Nod eller kant | Objekt |

### <a name="properties-of-an-item"></a>Egenskaper för ett objekt

Varje Azure Cosmos-objekt har följande systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa av dem inte är direkt exponerade.

| Systemdefinierad egenskap | Systemgenererad eller användare kan konfigureras| Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unikt ID för objektet | Ja | Inga | Inga | Inga | Inga |
|\_etag | Systemgenererad | Entity-taggen används för optimistisk concurrency-kontroll | Ja | Inga | Inga | Inga | Inga |
|\_via | Systemgenererad | Tidsstämpel för den senaste uppdateringen av objektet | Ja | Inga | Inga | Inga | Inga |
|\_ständiga | Systemgenererad | Adresser bara URI för objektet | Ja | Inga | Inga | Inga | Inga |
|id | Vilken som helst | Användardefinierat unikt namn i en logisk partition. | Ja | Ja | Ja | Ja | Ja |
|Godtyckliga användardefinierade egenskaper | Användardefinierade | Användardefinierade egenskaper som representeras i API – intern representation (inklusive JSON, BSON och CQL) | Ja | Ja | Ja | Ja | Ja |

> [!NOTE]
> `id`Egenskapens unikhet är endast tvingande inom varje logisk partition. Flera dokument kan ha samma `id` egenskap med olika nyckel värden.

### <a name="operations-on-items"></a>Åtgärder för objekt

Azure Cosmos-objekt har stöd för följande åtgärder. Du kan använda någon av Azure Cosmos-API: erna för att utföra åtgärderna.

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Infoga, ersätta, ta bort, upsert, läsa | Inga | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar ditt Azure Cosmos-konto och andra koncept:

* [Så här hanterar du ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Global distribution](distribute-data-globally.md)
* [Konsekvensnivåer](consistency-levels.md)
* [VNET-tjänstens slut punkt för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)
* [IP-brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)
* [Anvisningar för att lägga till och ta bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

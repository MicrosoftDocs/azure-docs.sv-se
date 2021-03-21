---
title: Etablera data flöde i Azure Cosmos-behållare och databaser
description: Lär dig hur du ställer in tillhandahållet data flöde för dina Azure Cosmos-behållare och-databaser.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: 1d5fba12ce9b1ce8d30c59a08aa36e1222abe3c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433938"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Introduktion till etablerade data flöden i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Med Azure Cosmos DB kan du ange tillhandahållet data flöde på dina databaser och behållare. Det finns två typer av tillhandahållet data flöde, standard (manuell) eller autoskalning. Den här artikeln ger en översikt över hur det etablerade data flödet fungerar. 

En Azure Cosmos-databas är en hanteringsenhet för en uppsättning containrar. En databas består av en uppsättning schemaoberoende containrar. En Azure Cosmos-container är skalbarhetsenheten för både dataflöde och lagring. En container partitioneras horisontellt över en uppsättning datorer i en Azure-region och distribueras i alla Azure-regioner som är kopplade till ditt Azure Cosmos-konto.

Med Azure Cosmos DB kan du etablera data flöde med två granularitet:
 
- Azure Cosmos-containrar
- Azure Cosmos-databaser

## <a name="set-throughput-on-a-container"></a>Ange data flöde i en behållare  

Det data flöde som har allokerats på en Azure Cosmos-behållare är exklusivt reserverat för den behållaren. Behållaren tar emot det etablerade data flödet hela tiden. Det etablerade data flödet på en behållare backas upp av service avtal. Information om hur du konfigurerar data flöde för standard (manuell) i en behållare finns i [etablera data flöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md). Information om hur du konfigurerar autoskalning av data flöde i en behållare finns i [etablera autoskalning genom strömning](how-to-provision-autoscale-throughput.md).

Att ställa in tillhandahållet data flöde på en behållare är det alternativ som används oftast. Du kan skala data flöde elastiskt för en behållare genom att tillhandahålla valfri mängd data flöde med hjälp av [enheter för programbegäran (ru: er)](request-units.md). 

Det data flöde som har allokerats för en behållare distribueras jämnt mellan de fysiska partitionerna, och en lämplig partitionsnyckel som distribuerar de logiska partitionerna jämnt mellan de fysiska partitionerna distribueras även genom data flödet jämnt över alla logiska partitioner i behållaren. Du kan inte selektivt ange data flödet för logiska partitioner. Eftersom en eller flera logiska partitioner i en behållare finns i en fysisk partition, hör de fysiska partitionerna exklusivt till behållaren och har stöd för det data flöde som har allokerats på behållaren. 

Om arbets belastningen som körs på en logisk partition förbrukar mer än det data flöde som allokerats till den underliggande fysiska partitionen, är det möjligt att dina åtgärder kommer att begränsas. Vad som kallas för en _aktiv partition_ inträffar när en logisk partition har oproportionerligt fler förfrågningar än andra nyckel värden.

När Rate-Limiting inträffar kan du antingen öka det etablerade data flödet för hela behållaren eller utföra åtgärderna igen. Du bör också se till att du väljer en partitionsnyckel som jämnt distribuerar lagringen och begär volym. Mer information om partitionering finns [i partitionering och horisontell skalning i Azure Cosmos DB](partitioning-overview.md).

Vi rekommenderar att du konfigurerar data flödet på behållar precisionen om du vill ha förutsägbara prestanda för behållaren.

Följande bild visar hur en fysisk partition är värd för en eller flera logiska partitioner i en behållare:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Fysisk partition som är värd för en eller flera logiska partitioner i en behållare" border="false":::

## <a name="set-throughput-on-a-database"></a>Ange dataflöde för en databas

När du etablerar data flöde i en Azure Cosmos-databas delas data flödet över alla behållare (kallas delade databas behållare) i databasen. Ett undantag är om du har angett ett etablerat dataflöde för specifika containrar i databasen. Att dela databas nivåns etablerade data flöde bland dess behållare är detsamma som att vara värd för en databas på ett kluster med datorer. Eftersom alla behållare i en databas delar resurserna som är tillgängliga på en dator, kan du naturligt inte få förutsägbara prestanda för en viss behållare. Information om hur du konfigurerar tillhandahållet data flöde på en databas finns i [Konfigurera etablerade data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md). Information om hur du konfigurerar autoskalning av data flödet i en databas finns i [etablera autoskalning genom strömning](how-to-provision-autoscale-throughput.md).

Eftersom alla behållare i databasen delar det etablerade data flödet ger Azure Cosmos DB inga förutsägbara data flödes garantier för en viss behållare i databasen. Den del av data flödet som en viss behållare kan ta emot är beroende av:

* Antalet behållare.
* Val av partitionsnyckel för olika behållare.
* Distributionen av arbets belastningen mellan olika logiska partitioner i behållarna. 

Vi rekommenderar att du konfigurerar data flödet på en databas när du vill dela data flödet över flera behållare, men inte vill tilldela data flödet till en viss behållare. 

Följande exempel visar var det är önskvärt att etablera data flöde på databas nivå:

* Att dela en Databass etablerade data flöde i en uppsättning behållare är användbart för ett program med flera innehavare. Varje användare kan representeras av en separat Azure Cosmos-behållare.

* Att dela en Databass etablerade data flöde i en uppsättning behållare är användbart när du migrerar en NoSQL-databas, till exempel MongoDB eller Cassandra, som finns på ett kluster med virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Tänk på det etablerade data flödet som kon figurer ATS på din Azure Cosmos-databas som en logisk motsvarighet, men mer kostnads effektivt och elastiskt, till beräknings kapaciteten för ditt MongoDB-eller Cassandra-kluster.  

Alla behållare som skapats i en databas med ett allokerat data flöde måste skapas med en [partitionsnyckel](partitioning-overview.md). Vid en viss tidpunkt distribueras det data flöde som allokerats till en behållare i en databas över alla logiska partitioner i behållaren. När du har behållare som delar ett allokerat data flöde som kon figurer ATS för en databas, kan du inte selektivt använda genomflödet för en speciell behållare eller en logisk partition. 

Om arbets belastningen på en logisk partition förbrukar mer än det data flöde som har allokerats till en viss logisk partition, är dina åtgärder avgiftsbelagda. När Rate-Limiting sker kan du antingen öka data flödet för hela databasen eller försöka utföra åtgärderna igen. Mer information om partitionering finns i [logiska partitioner](partitioning-overview.md).

Containrar i en databas med delat dataflöde delar på dataflödet (RU/s) som allokerats till databasen. Med standard (manuellt) tillhandahållet data flöde kan du ha upp till 25 behållare med minst 400 RU/s på databasen. Med autoskalning av allokerat data flöde kan du ha upp till 25 behållare i en databas med autoskalning Max 4000 RU/s (skalas mellan 400-4000 RU/s).

> [!NOTE]
> I februari 2020 införde vi en ändring som gör att du kan ha högst 25 behållare i en delad data flödes databas, vilket bättre möjliggör data flödes delning över behållarna. Efter de första 25 behållarna kan du bara lägga till fler behållare i databasen om de är [etablerade med dedikerat data flöde](#set-throughput-on-a-database-and-a-container), som är åtskilda från det delade data flödet i databasen.<br>
Men om ditt Azure Cosmos DB-konto redan innehåller en databas med delat dataflöde med >=25 containrar gäller inte den här ändringen för kontot eller andra konton i samma Azure-prenumeration. [Kontakta produkt supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du har feedback eller frågor. 

Om arbets belastningarna innebär att du tar bort och återskapar alla samlingar i en databas, rekommenderar vi att du släpper den tomma databasen och återskapar en ny databas innan du skapar samlingen. Följande bild visar hur en fysisk partition kan vara värd för en eller flera logiska partitioner som tillhör olika behållare i en databas:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Fysisk partition som är värd för en eller flera logiska partitioner som tillhör olika behållare " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Ange data flöde för en databas och en behållare

Du kan kombinera de två modellerna. Etablering av data flöde på både databasen och behållaren tillåts. Följande exempel visar hur du etablerar standard (manuellt) etablerat data flöde på en Azure Cosmos-databas och en behållare:

* Du kan skapa en Azure Cosmos-databas med namnet *Z* med standard (manuellt) allokerat data flöde för *"K"* ru: er. 
* Skapa sedan fem behållare med namnet *A*, *B*, *C*, *D* och *E* i databasen. När du skapar container B, se till att aktivera **etablera dedikerat data flöde för det här behållar** alternativet och konfigurera *"P"* -ru: er av etablerat data flöde på den här behållaren. Du kan bara konfigurera delade och dedikerade data flöde när du skapar databasen och behållaren. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Ange data flödet på behållar nivån":::

* *"K"* ru: er-dataflödet delas mellan de fyra behållarna *A*, *C*, *D*, och *E*. Den exakta mängden data flöde som är tillgängliga för *A*, *C*, *D* eller *E* varierar. Det finns inga service avtal för varje enskild behållares data flöde.
* Behållaren med namnet *B* garanterar att du kan hämta *"P"* ru: er-dataflöde hela tiden. Den backas upp av service avtal.

> [!NOTE]
> Det går inte att konvertera en behållare med ett allokerat data flöde till en delad databas behållare. Det går inte att konvertera en delad databas behållare till ett dedikerat data flöde.

## <a name="update-throughput-on-a-database-or-a-container"></a>Uppdatera data flödet för en databas eller en behållare

När du har skapat en Azure Cosmos-behållare eller en databas kan du uppdatera det etablerade data flödet. Det finns ingen gräns för maximalt tillhandahållet data flöde som du kan konfigurera i databasen eller behållaren.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Aktuellt allokerat data flöde

Du kan hämta det etablerade data flödet för en behållare eller en databas i Azure Portal eller genom att använda SDK: erna:

* [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync) på .NET SDK.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput) i Java SDK.

Svaret på dessa metoder innehåller också det [lägsta allokerade data flödet](concepts-limits.md#storage-and-database-operations) för behållaren eller databasen:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput) på .NET SDK.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput) i Java SDK.

Det faktiska antalet RU/s kan variera beroende på din konto konfiguration. Men vanligt vis är det högst:

* 400 RU/s 
* Aktuellt lagrings utrymme i GB * 10 RU/s (den här begränsningen kan vara avslappnad i vissa fall, se vårt [höga lagrings-/låg data flödes program](#high-storage-low-throughput-program))
* Mest RU/s etablerad i databasen eller containern/100

### <a name="changing-the-provisioned-throughput"></a>Ändra det etablerade data flödet

Du kan skala det etablerade data flödet för en behållare eller en databas via Azure Portal eller genom att använda SDK: erna:

* [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) på .NET SDK.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput) i Java SDK.

Om du **minskar det etablerade data flödet** kommer du att kunna göra det till ett [minimum](#current-provisioned-throughput).

Om du **ökar det etablerade data flödet**, är det mesta av tiden att göra en omedelbar åtgärd. Det finns dock fall där åtgärden kan ta längre tid på grund av system aktiviteterna för att etablera nödvändiga resurser. I det här fallet kommer ett försök att ändra det etablerade data flödet medan den här åtgärden pågår att ge ett HTTP 423-svar med ett fel meddelande som förklarar att en annan skalnings åtgärd pågår.

> [!NOTE]
> Om du planerar för en mycket stor inmatnings arbets belastning som kräver en stor ökning av det etablerade data flödet, bör du tänka på att skalnings åtgärden inte har något service avtal och, vilket beskrivs i föregående stycke, kan ta lång tid när ökningen är stor. Du kanske vill planera framåt och påbörja skalningen innan arbets belastningen startar och använda nedanstående metoder för att kontrol lera förloppet.

Du kan kontrol lera skalnings förloppet program mässigt genom att läsa det [aktuella etablerade data flödet](#current-provisioned-throughput) och använda:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending) på .NET SDK.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending) i Java SDK.

Du kan använda [Azure Monitor mått](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) för att visa historiken över det etablerade data flödet (ru/s) och lagrings utrymme på en resurs.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Program med hög lagring/låg genom strömning

Som det beskrivs i avsnittet [Aktuellt etablerat data flöde](#current-provisioned-throughput) ovan, är det minsta data flöde som du kan etablera på en behållare eller databas beroende av ett antal faktorer. En av dem är den mängd data som för närvarande lagras, eftersom Azure Cosmos DB tillämpar ett minsta data flöde på 10 RU/s per GB lagrings utrymme.

Detta kan vara ett problem i situationer där du behöver lagra stora mängder data, men ha låga data flödes krav i jämförelse. För att bättre kunna hantera dessa scenarier har Azure Cosmos DB infört ett **"högt lagrings-/låg data flöde"-program** som minskar begränsningen ru/s per GB för berättigade konton.

Om du vill delta i programmet och utvärdera din fullständiga behörighet måste du fylla i [den här undersökningen](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u). Azure Cosmos DBs teamet följer sedan upp och fortsätter med din onboarding.

## <a name="comparison-of-models"></a>Jämförelse av modeller
Den här tabellen visar en jämförelse mellan Provisioning standard (manuell) data flöde i en databas jämfört med på en behållare. 

|**Parameter**  |**Standard (manuell) genom strömning på en databas**  |**Standard (manuell) genom strömning på en behållare**|**Autoskalning av data flöde på en databas** | **Autoskalning av data flöde på en behållare**|
|---------|---------|---------|---------|---------|
|Start punkt (minst RU/s) |400 RU/s. Kan ha upp till 25 behållare utan RU/s minimum per container.</li> |400| Skala mellan 400-4000 RU/s. Kan ha upp till 25 behållare utan RU/s minimum per container.</li> | Skala mellan 400-4000 RU/s.|
|Lägsta RU/s per behållare|--|400|--|Skala mellan 400-4000 RU/s|
|Maximalt ru: er|Obegränsat, på databasen.|Obegränsat, på behållaren.|Obegränsat, på databasen.|Obegränsat, på behållaren.
|Ru: er tilldelad eller tillgänglig för en speciell behållare|Inga garantier. Ru: er som tilldelas en specifik behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnyckel för behållare som delar data flödet, distributionen av arbets belastningen och antalet behållare. |Alla ru: er som kon figurer ATS på behållaren är exklusivt reserverade för behållaren.|Inga garantier. Ru: er som tilldelas en specifik behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnyckel för behållare som delar data flödet, distributionen av arbets belastningen och antalet behållare. |Alla ru: er som kon figurer ATS på behållaren är exklusivt reserverade för behållaren.|
|Maximalt lagrings utrymme för en behållare|Många.|Obegränsat|Obegränsat|Obegränsat|
|Maximalt data flöde per logisk partition för en behållare|10 000 RU/s|10 000 RU/s|10 000 RU/s|10 000 RU/s|
|Maximalt lagrings utrymme (data index) per logisk partition för en behållare|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partitioning-overview.md).
* Lär dig hur du [etablerar standard (manuell) i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar standard-genomflödet (manuell) i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
* Lär dig hur du [etablerar autoskalning genom strömning på en Azure Cosmos-databas eller-behållare](how-to-provision-autoscale-throughput.md).

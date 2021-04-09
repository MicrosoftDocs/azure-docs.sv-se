---
title: Introduktion till Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du kan använda Azure Cosmos DB för att lagra och fråga stora mängder data med hjälp av Azure Cosmos DB:s API för MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5820592bf06cc9427e12aa0cd79c54dc1f0156e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658003"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partitioning-overview.md) världen över, ensiffrig svarstid som den 99:e percentilen, och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. Azure Cosmos DB tjänst implementerar kabel protokoll för vanliga NoSQL-API: er, inklusive Cassandra, MongoDB, Gremlin och Azure Table Storage. På så sätt kan du använda välbekanta NoSQL-klientdrivrutiner och verktyg för att interagera med din Cosmos-databas.

> [!NOTE]
> [Läget för Server lös kapacitet](serverless.md) är nu tillgängligt på Azure Cosmos DB s API för MongoDB.

## <a name="wire-protocol-compatibility"></a>Trådprotokollkompatibilitet

Azure Cosmos DB implementerar Wire-protokollet för MongoDB. Den här implementeringen möjliggör transparent kompatibilitet med inbyggda MongoDB-klient-SDK: er, driv rutiner och verktyg. Azure Cosmos DB är värd för MongoDB-databasmotorn. Information om vilka funktioner som stöds efter MongoDB finns här: 
- [Azure Cosmos DB s API för mongo DB version 4,0](mongodb-feature-support-40.md)
- [Azure Cosmos DB s API för mongo DB version 3,6](mongodb-feature-support-36.md)

Som standard är nya konton som skapats med Azure Cosmos DBs API för MongoDB kompatibla med version 3,6 av MongoDB-Wire-protokollet. Eventuella MongoDB-klientdatorer som förstår den här protokoll versionen bör kunna ansluta till Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="API för Azure Cosmos DB för MongoDB" border="false":::

## <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med Cosmos DB som en fullständigt hanterad och globalt distribuerad databas som en tjänst beskrivs [här](introduction.md). Genom att implementera trådprotokoll internt för populära NoSQL-API:er tillhandahåller Cosmos DB dessutom följande fördelar:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Hålla ditt program portabelt och förbli molnleverantörsoberoende.
* Skaffa branschledande, ekonomiskt uppbackade serviceavtal för vanliga Cosmos DB-baserade NoSQL-API:er.
* Skala det etablerade dataflödet och lagringen elastiskt för dina Cosmos-databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.
* Nyckel färdiga, global distribution med flera regioner skriver replikering.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB:s API:er för MongoDB

Följ snabb starterna för att skapa ett Azure Cosmos-konto och migrera ditt befintliga MongoDB-program för att använda Azure Cosmos DB, eller skapa ett nytt:

* [Migrera en befintlig MongoDB Node.js-webbapp](create-mongodb-nodejs.md).
* [Skapa en webbapp med Azure Cosmos DB:s API för MongoDB och .NET SDK](create-mongodb-dotnet.md)
* [Skapa en konsolapp med Azure Cosmos DB:s API för MongoDB och Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:

* Följ självstudien [Anslut ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) om du vill lära dig hur du hämtar information om anslutningssträngar för ditt konto.
* Följ självstudiekursen [Använda Studio 3T med Azure Cosmos DB](mongodb-mongochef.md) om du vill lära dig hur du skapar en anslutning mellan din Cosmos-databas och MongoDB-appen i Studio 3T.
* Följ självstudiekursen [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) om du vill lära dig hur du importerar dina data till en Cosmos-databas.
* Anslut till ett Cosmos-konto med [Robo 3T](mongodb-robomongo.md).
* Lär dig hur du [konfigurerar läsinställningar för globalt distribuerade appar](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Hitta lösningarna som ofta hittar fel i vår [fel söknings guide](mongodb-troubleshoot.md)


<sup>OBS! den här artikeln beskriver en funktion i Azure Cosmos DB som ger till gång till Wire Protocol-kompatibilitet med MongoDB-databaser. Microsoft kör inte MongoDB-databaser för att tillhandahålla den här tjänsten. Azure Cosmos DB är inte kopplad till MongoDB, Inc.</sup>
